# 在 Go 中构建发布/订阅服务

> 原文：<https://blog.logrocket.com/building-pub-sub-service-go/>

## 介绍

发布/订阅或发布-订阅服务是一种消息传递模式，在这种模式下，发布者(发送者)通过将消息按主题或类别进行分类，向订阅者(接收者)发送消息，而不知道任何单个订阅者的具体情况。

另一方面，订阅者订阅特定的类或主题，并接收由发布者发布的与该主题相关联的消息，而不知道关于发布者的任何细节。

该系统提供了更大的网络可扩展性，可用于多种应用，如流分析或数据集成管道，以接收和分发数据。

在本指南中，我将简要讨论如何使用 [Go 模式](http://tmrts.com/go-patterns/)在 Go 中实现发布/订阅服务。我们将使用通道上几个 [Goroutines](https://blog.logrocket.com/concurrency-patterns-golang-waitgroups-goroutines/) 之间的进程内通信来实现发布/订阅服务；因为我们将使用并发编程，通道有助于独立运行的 Goroutines 之间的通信。

## 文件结构

在本指南中，我们将遵循下面的文件结构。我们已经创建了一个名为`pubsub`的新包和一个名为`main.go`的模块，我们将在其中运行加密价格示例:

```
├── main.go
└── pubsub
    ├── broker.go
    ├── go.mod
    ├── go.sum
    ├── message.go
    └── subscriber.go

```

## 创建和发布消息

现在让我们从一个简单的实现开始。首先，让我们从讨论消息结构开始。这里，每个消息对象可以有多个属性，包括主题和消息体:

```
type Message struct {
    topic string
    body string
}

```

接下来，我们来谈谈订户。`Subscriber`包含一个地图的唯一标识符字符串(我们将在后面讨论)。它拥有的一个重要属性是信息渠道。发布者通过`signal()`方法将消息推送到这个通道:

```
type Subscriber struct {
    id string // id of subscriber
    messages chan* Message // messages channel
    topics map[string]bool // topics it is subscribed to.
    active bool // if given subscriber is active
    mutex sync.RWMutex // lock
}

```

最后，`Broker`结构由所有订阅者和一个供订阅者订阅的主题图组成:

```
type Broker struct {
    subscribers Subscribers // map of subscribers id:Subscriber
    topics map[string]Subscribers // map of topic to subscribers
    mut sync.RWMutex // mutex lock
}

```

上面的`Subscribe`方法为给定的订阅者订阅给定的主题。这是通过向`Subscriber`添加一个主题，然后向代理主题中添加一个条目来实现的，该条目具有一个订阅者 ID:

```
func (b *Broker) Subscribe(s *Subscriber, topic string) {
    b.mut.Lock()
    defer b.mut.Unlock()
    if  b.topics[topic] == nil {
        b.topics[topic] = Subscribers{}
    }
    s.AddTopic(topic)
    b.topics\[topic\][s.id] = s
}

```

在上面的代码中，`Publisher`方法将给定的消息发布到给定的主题。其工作原理是创建一个新的消息对象，然后将其推送到所有订阅了该主题的订阅者通道。

可以使用`signal()`方法推送消息，如下所示:

```
func (b *Broker) Publish(topic string, msg string) {
    // publish the message to given topic.
    b.mut.RLock()
    bTopics := b.topics[topic]
    b.mut.RUnlock()
    for _, s := range bTopics {
        m:= NewMessage(msg, topic)
        if !s.active{
            return
        }
        go (func(s *Subscriber){
            s.Signal(m)
        })(s)
    }
}

```

## 取消消息订阅并删除订阅者

方法的作用是取消给定主题的订阅。取消订阅过程会从特定主题映射中删除订阅者 ID，然后从该订阅者的主题列表中删除该主题:

```
func (b *Broker) Unsubscribe(s *Subscriber, topic string) {
    b.mut.RLock()
    defer b.mut.RUnlock()
    delete(b.topics[topic], s.id)
    s.RemoveTopic(topic)
}

```

`signal`方法将消息推送到消息通道。在推送至某个频道之前，它会检查该频道是活动的还是关闭的:

```
func (s *Subscriber)Signal(msg *Message) () {
    // Gets the message from the channel
    s.mutex.RLock()
    defer s.mutex.RUnlock()
    if s.active{
        s.messages <- msg
    }
}

```

从代理中删除给定的订户。为此，它取消订阅者订阅的所有主题，并从主订阅者列表中删除订阅者:

```
func (b *Broker)RemoveSubscriber(s *Subscriber)(){
    for topic := range(s.topics){
        b.Unsubscribe(s, topic)
    }
    b.mut.Lock()
    delete(b.subscribers, s.id)
    b.mut.Unlock()
    s.Destruct()
}

```

subscriber 的`Destruct`方法将 active 设置为 false，这意味着一旦我们完成发送，它就关闭消息通道。这在 Go 中很重要，因为它的目的是在工作完成后清理资源:

```
func (s *Subscriber)Destruct() {
    // destructor for subscriber.
    s.mutex.RLock()
    defer s.mutex.RUnlock()
    s.active = false
    close(s.messages)
}

```

注意，Go 遵循了几个与众不同的方法，其中之一就是“不要通过共享内存来交流，通过交流来共享内存”。但是 Go 是一种实用的语言，所以当我们有一个被多个 Goroutines 访问的共享数据结构时，为了保护访问而锁定是可以的。

## 最终代码

现在您已经对一些重要的代码片段有了一些了解，让我们来讨论一下最终的完整代码。

从`pubsub/message.go`开始。在这个模块中，定义了消息结构和一些有用的方法，比如`NewMessage(msg string, topic string) (* Message)`，它创建一个新的消息对象并返回它。

`GetTopic() string`返回给定消息对象的主题，`GetMessageBody() string`返回给定消息对象的消息体:

```
package pubsub
type Message struct {
    topic string
    body string
}
func NewMessage(msg string, topic string) (* Message) {
    // Returns the message object
    return &Message{
        topic: topic,
        body: msg,
    }
}
func (m *Message) GetTopic() string {
    // returns the topic of the message
    return m.topic
}
func (m *Message) GetMessageBody() string {
    // returns the message body.
    return m.body
}

```

接下来是`pubsub/subscriber.go`。在本模块中，定义了订户及其有用的方法:

*   `CreateNewSubscriber() (string, *Subscriber)`返回一个新的`Subscriber`对象
*   `AddTopic(topic string)`给订阅者添加给定主题
*   从订阅者中删除给定的主题
*   `GetTopics()([]string)`返回订阅者已订阅的主题列表
*   最后，`Listen()`方法监听订户的消息通道并打印消息

```
package pubsub
import (
    "crypto/rand"
    "fmt"
    "log"
    "sync"
)

type Subscriber struct {
    id string // id of subscriber
    messages chan* Message // messages channel
    topics map[string]bool // topics it is subscribed to.
    active bool // if given subscriber is active
    mutex sync.RWMutex // lock
}
func CreateNewSubscriber() (string, *Subscriber) {
    // returns a new subscriber.
    b := make([]byte, 8)
    _, err := rand.Read(b)
    if err != nil {
        log.Fatal(err)
    }
    id := fmt.Sprintf("%X-%X", b[0:4], b[4:8])
    return id, &Subscriber{
        id: id,
        messages: make(chan *Message),
        topics: map[string]bool{},
        active: true,
    }
}
func (s * Subscriber)AddTopic(topic string)(){
    // add topic to the subscriber
    s.mutex.RLock()
    defer s.mutex.RUnlock()
    s.topics[topic] = true
}
func (s * Subscriber)RemoveTopic(topic string)(){
    // remove topic to the subscriber
    s.mutex.RLock()
    defer s.mutex.RUnlock()
    delete(s.topics, topic)
}
func (s * Subscriber)GetTopics()([]string){
    // Get all topic of the subscriber
    s.mutex.RLock()
    defer s.mutex.RUnlock()
    topics := []string{}
    for topic, _ := range s.topics {
        topics = append(topics, topic)
    }
    return topics
}
func (s *Subscriber)Destruct() {
    // destructor for subscriber.
    s.mutex.RLock()
    defer s.mutex.RUnlock()
    s.active = false
    close(s.messages)
}
func (s *Subscriber)Signal(msg *Message) () {
    // Gets the message from the channel
    s.mutex.RLock()
    defer s.mutex.RUnlock()
    if s.active{
        s.messages <- msg
    }
}
func (s *Subscriber)Listen() {
    // Listens to the message channel, prints once received.
    for {
        if msg, ok := <- s.messages; ok {
            fmt.Printf("Subscriber %s, received: %s from topic: %s\n", s.id, msg.GetMessageBody(), msg.GetTopic())
        }
    }
}
```

最后，我们有`pubsub/broker.go`，其中定义了代理及其方法:

*   `NewBroker() (*Broker)`返回新的代理对象
*   将给定的新订户添加到代理
*   从代理中删除给定的订户
*   向给定的主题列表广播给定的消息
*   并且`GetSubscribers(topic string) int`返回订阅给定主题的订阅者的数量

```
package pubsub
import (
    "fmt"
    "sync”
)
type Subscribers map[string]*Subscriber
type Broker struct {
    subscribers Subscribers // map of subscribers id:Subscriber
    topics map[string]Subscribers // map of topic to subscribers
    mut sync.RWMutex // mutex lock
}
func NewBroker() (*Broker){
    // returns new broker object
    return &Broker{
        subscribers: Subscribers{},
        topics: map[string]Subscribers{},
    }
}
func (b *Broker)AddSubscriber()(*Subscriber){
    // Add subscriber to the broker.
    b.mut.Lock()
    defer b.mut.Unlock()
    id, s := CreateNewSubscriber()
    b.subscribers[id] = s;
    return s
}
func (b *Broker)RemoveSubscriber(s *Subscriber)(){
    // remove subscriber to the broker.
    //unsubscribe to all topics which s is subscribed to.
    for topic := range(s.topics){
        b.Unsubscribe(s, topic)
    }
    b.mut.Lock()
    // remove subscriber from list of subscribers.
    delete(b.subscribers, s.id)
    b.mut.Unlock()
    s.Destruct()
}
func (b *Broker)Broadcast(msg string, topics []string){
    // broadcast message to all topics.
    for _, topic:=range(topics) {
        for _, s := range(b.topics[topic]){
            m:= NewMessage(msg, topic)
            go (func(s *Subscriber){
                s.Signal(m)
            })(s)
        }
    }
}
func (b *Broker) GetSubscribers(topic string) int {
    // get total subscribers subscribed to given topic.
    b.mut.RLock()
    defer b.mut.RUnlock()
    return len(b.topics[topic])
}
func (b *Broker) Subscribe(s *Subscriber, topic string) {
    // subscribe to given topic
    b.mut.Lock()
    defer b.mut.Unlock()
    if  b.topics[topic] == nil {
        b.topics[topic] = Subscribers{}
    }
    s.AddTopic(topic)
    b.topics[topic][s.id] = s
    fmt.Printf("%s Subscribed for topic: %s\n", s.id, topic)
}
func (b *Broker) Unsubscribe(s *Subscriber, topic string) {
    // unsubscribe to given topic
    b.mut.RLock()
    defer b.mut.RUnlock()
    delete(b.topics[topic], s.id)
    s.RemoveTopic(topic)
    fmt.Printf("%s Unsubscribed for topic: %s\n", s.id, topic)
}
func (b *Broker) Publish(topic string, msg string) {
    // publish the message to given topic.
    b.mut.RLock()
    bTopics := b.topics[topic]
    b.mut.RUnlock()
    for _, s := range bTopics {
        m:= NewMessage(msg, topic)
        if !s.active{
            return
        }
        go (func(s *Subscriber){
            s.Signal(m)
        })(s)
    }
}
```

## 最后一个例子

既然我们的发布/订阅服务已经创建，让我们以它为例来获取加密货币的价格更新。在这个例子中，发布者发布加密货币的价格值，任何订阅特定加密货币的人都会收到价格更新。

在这种情况下，发布者随机生成每个加密货币的价格值，并发布它们各自的主题名称(加密货币的名称)。接下来，有两个订阅了一组加密主题的订阅者，因此一旦消息可用，这两个订阅者就会收到消息，并在控制台中显示它们:

```
package main
import (
    "fmt"
    "math/rand”
    "time"
    "./pubsub"
)
// available topics
var availableTopics = map[string]string{
    "BTC": "BITCOIN",
    "ETH": "ETHEREUM",
    "DOT": "POLKADOT",
    "SOL": "SOLANA",
}
func pricePublisher(broker *pubsub.Broker)(){
    topicKeys := make([]string, 0, len(availableTopics))
    topicValues := make([]string, 0, len(availableTopics))
    for k, v := range availableTopics {
        topicKeys = append(topicKeys, k)
        topicValues = append(topicValues, v)
    }
    for {
        randValue := topicValues[rand.Intn(len(topicValues))] // all topic values.
        msg:= fmt.Sprintf("%f", rand.Float64())
        // fmt.Printf("Publishing %s to %s topic\n", msg, randKey)
        go broker.Publish(randValue, msg)
        // Uncomment if you want to broadcast to all topics.
        // go broker.Broadcast(msg, topicValues)
        r := rand.Intn(4)
        time.Sleep(time.Duration(r) * time.Second) //sleep for random secs.
    }
}

func main(){
    // construct new broker.
    broker := pubsub.NewBroker()
    // create new subscriber
    s1 := broker.AddSubscriber()
    // subscribe BTC and ETH to s1.
    broker.Subscribe(s1, availableTopics["BTC"])
    broker.Subscribe(s1, availableTopics["ETH"])
    // create new subscriber
    s2 := broker.AddSubscriber()
    // subscribe ETH and SOL to s2.
    broker.Subscribe(s2, availableTopics["ETH"])
    broker.Subscribe(s2, availableTopics["SOL"])
    go (func(){
        // sleep for 5 sec, and then subscribe for topic DOT for s2
        time.Sleep(3*time.Second)
        broker.Subscribe(s2, availableTopics["DOT"])
    })()
    go (func(){
        // s;eep for 5 sec, and then unsubscribe for topic SOL for s2
        time.Sleep(5*time.Second)
        broker.Unsubscribe(s2, availableTopics["SOL"])
        fmt.Printf("Total subscribers for topic ETH is %v\n", broker.GetSubscribers(availableTopics["ETH"]))
    })()

    go (func(){
        // s;eep for 5 sec, and then unsubscribe for topic SOL for s2
        time.Sleep(10*time.Second)
        broker.RemoveSubscriber(s2)
        fmt.Printf("Total subscribers for topic ETH is %v\n", broker.GetSubscribers(availableTopics["ETH"]))
    })()
    // Concurrently publish the values.
    go pricePublisher(broker)
    // Concurrently listens from s1.
    go s1.Listen()
    // Concurrently listens from s2.
    go s2.Listen()
    // to prevent terminate
    fmt.Scanln()
    fmt.Println("Done!")
}

```

输出将如下所示:

```
❯ GO111MODULE=off go run main.go
208B51C5-1F40B37F Subscribed for topic: BITCOIN
208B51C5-1F40B37F Subscribed for topic: ETHEREUM
60466C8A-3662A48A Subscribed for topic: ETHEREUM
60466C8A-3662A48A Subscribed for topic: SOLANA
Subscriber 60466C8A-3662A48A, received: 0.940509 from topic: ETHEREUM
Subscriber 208B51C5-1F40B37F, received: 0.940509 from topic: ETHEREUM
60466C8A-3662A48A Subscribed for topic: POLKADOT
Subscriber 60466C8A-3662A48A, received: 0.424637 from topic: SOLANA
60466C8A-3662A48A Unsubscribed for topic: SOLANA
Total subscribers for topic ETH is 2
Subscriber 208B51C5-1F40B37F, received: 0.515213 from topic: BITCOIN
Subscriber 60466C8A-3662A48A, received: 0.156519 from topic: ETHEREUM
Subscriber 208B51C5-1F40B37F, received: 0.156519 from topic: ETHEREUM
Subscriber 60466C8A-3662A48A, received: 0.283034 from topic: POLKADOT
Subscriber 60466C8A-3662A48A, received: 0.380657 from topic: POLKADOT
Subscriber 60466C8A-3662A48A, received: 0.218553 from topic: ETHEREUM
Subscriber 208B51C5-1F40B37F, received: 0.218553 from topic: ETHEREUM
60466C8A-3662A48A Unsubscribed for topic: ETHEREUM
60466C8A-3662A48A Unsubscribed for topic: POLKADOT
Total subscribers for topic ETH is 1
Subscriber 208B51C5-1F40B37F, received: 0.865335 from topic: BITCOIN
Subscriber 208B51C5-1F40B37F, received: 0.028303 from topic: ETHEREUM
Subscriber 208B51C5-1F40B37F, received: 0.059121 from topic: ETHEREUM

```

## 结论

在本指南中，我们讨论并演示了在 Go 中使用 Goroutines 和 channels 的发布/订阅服务的一些设计选择。但是，这种实现使用通道上多个 Goroutines 之间的进程内通信，这与分布式发布/订阅服务不同。分布式服务需要一种复杂的容错方法。

本教程的完整代码[可以在这里找到](https://github.com/krazygaurav/pubsub-go)。编码快乐！

## 使用 [LogRocket](https://lp.logrocket.com/blg/signup) 消除传统错误报告的干扰

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/signup)

[LogRocket](https://lp.logrocket.com/blg/signup) 是一个数字体验分析解决方案，它可以保护您免受数百个假阳性错误警报的影响，只针对几个真正重要的项目。LogRocket 会告诉您应用程序中实际影响用户的最具影响力的 bug 和 UX 问题。

然后，使用具有深层技术遥测的会话重放来确切地查看用户看到了什么以及是什么导致了问题，就像你在他们身后看一样。

LogRocket 自动聚合客户端错误、JS 异常、前端性能指标和用户交互。然后 LogRocket 使用机器学习来告诉你哪些问题正在影响大多数用户，并提供你需要修复它的上下文。

关注重要的 bug—[今天就试试 LogRocket】。](https://lp.logrocket.com/blg/signup-issue-free)