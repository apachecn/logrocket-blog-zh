# 坚实的原则:JavaScript 框架中的单一责任

> 原文：<https://blog.logrocket.com/solid-principles-single-responsibility-in-javascript-frameworks/>

[单一责任原则](https://stackify.com/solid-design-principles/)是组成[实体设计原则](https://en.wikipedia.org/wiki/SOLID)的五个面向对象设计(OOD)准则之一。

在本教程中，我们将关注单一责任原则，并演示它如何帮助指导您在 JavaScript 框架中的设计决策，特别是 [Angular](https://angular.io/) 和 [React](https://reactjs.org/) 。

以下是我们将要介绍的内容:

## 什么是坚实的原则？

SOLID 是首字母缩写词，代表著名软件工程师 Robert C. Martin 概述的前五个 OOD 原则。坚实的原则旨在帮助开发人员设计健壮的、可维护的应用程序。

五项坚实的原则是:

1.  单一责任原则
2.  开闭原则
3.  利斯科夫替代原理
4.  界面分离原理
5.  从属倒置原则

## 什么是单一责任原则？

JavaScript 中的单一责任原则处理模块的内聚性。它声明函数和类应该只有一个任务。

以`Car`模型为例:

```
class Car {
    constructor(name,model,year) {
        this.name=name
        this.model=model
        this.year=year
    }
getCar(id) {
        return this.http.get('api/cars/'+id)
    }
saveCar() {
        return this.post('api/cars', { name: this.name, year: this.year, model: this.model })
    }
}

```

上述例子违反了单一责任原则。为什么？`Car`模型的本意是保存/表示一辆汽车，但是它有一个`getCar`方法从互联网上获取一辆汽车。这给了它另一个从一个端点获取汽车的责任。

需要在`Car`类的责任上画一条线:它将被用作模型还是对象？

如果我们触摸`saveCar`或`getCar`方法进行更改，这种更改可能会迫使我们重新设计`Car`模型，要么添加一个额外的属性，要么在`Car`类中添加另一个东西。如果我们忘记这样做，应用程序可能会以不可预知的方式崩溃。

我们可以将责任划分到不同的类别:

```
class Car {
    constructor(name, model, year) {
        this.name = name
        this.model = model
        this.year = year
    }
}
class CarService {
    getCar(id) {
        return this.http.get('api/cars/'+id)
    }
    saveCar(car) {
        this.http.post('api/cars', car)
    }
}

```

从这个例子中可以看出，我们现在已经分离了职责。现在，`Car`模型管理一辆汽车，而`CarService`负责从一个端点获取和保存汽车。

如果一个类有不止一个责任，那么这些责任就成了一对。对一项职责的更改可能会抑制班级满足其他职责的能力。这种耦合导致脆弱的设计，当改变时会以意想不到的方式破裂。

以下示例显示了如何在 React 和 Angular 组件中使用单一责任原则。这些例子同样适用于其他 JavaScript 框架，比如 [Vue.js](https://blog.logrocket.com/angular-vs-react-vs-vue-a-performance-comparison/) 、 [Svelte](https://blog.logrocket.com/should-you-use-svelte-in-production/) 等。

## 反应中的单一责任原则

假设我们有以下 React 组件:

```
class Movies extends Component {
    componentDidMount() {
        store.subscribe(() => this.forceUpdate())
    }
render() {
        const state = store.getState()
        const movies = state.movies.map((movie, index) => {
                <div className="movie-card" key={index}>
                    {{movie.name}}
                    Year: {{movie.year}}
                    Gross: {{movie.gross}}
                </div>
        })
        return (
            <div>
                <div className="movie-header">Movies App</div>
                <div className="movies-list">
                    {movies} 
                </div>
            </div>
        )
    }
}

```

这个组件有一些问题:

*   状态管理—组件订阅存储
*   数据获取—它从存储中获取状态
*   UI 演示——它呈现电影列表
*   业务逻辑——它与应用程序的业务逻辑(如何获得电影的逻辑)联系在一起

该 React 组件不可重用。如果我们希望在应用程序的另一个组件中重用电影列表，例如，显示高票房电影、按年份显示电影等的组件。—我们必须重写每个组件中的代码，即使它们是相同的。

这个组件很难维护，因为它包含了太多的部件。如果一个部分发生变化，就会有突破性的变化。它不能被优化，它会产生副作用，并且我们不能有效地[记忆 React 组件](https://blog.logrocket.com/rethinking-hooks-memoization/)以获得性能，因为这样做会导致陈旧的数据。

### 分离 React 组件中的关注点

继续我们上面的 React 组件示例，我们需要从`Movies`组件中提取 UI 表示。

我们将创建另一个组件`MoviesList`来处理这个问题。`MoviesList`组件将从其道具中期待电影数组:

```
class MoviesList extends Component {
    render() {
        const movies = props.movies.map((movie, index) => {
                <div className="movie-card" key={index}>
                    {{movie.name}}
                    Year: {{movie.year}}
                    Gross: {{movie.gross}}
                </div>
        })
        return (
            <div className="movies-list">
                {movies} 
            </div>
        )
    }
}
class Movies extends Component {
    componentDidMount() {
        store.subscribe(() => this.forceUpdate())
    }
render() {
        const state = store.getState()
        const movies = state.movies        return (
            <div>
                <div className="movie-header">Movies App</div>
                <MoviesList movies={movies} />
            </div>
        )
    }
}

```

我们重构了`Movies`组件，并从中分离出 UI 表示代码。现在它只关心如何订阅商店，从商店获取电影数据，并将其传递给`MoviesList`组件。它不再关心如何渲染电影；那现在是`MoviesList`组件的责任。

`MoviesList`组件是表示组件。它只呈现通过`movies`道具给它的电影。它不关心电影是从哪里得到的，是从商店、`localStorage`还是虚拟服务器/虚拟数据等等。

有了这些，我们可以在 React 应用甚至其他项目的任何地方重用`MoviesList`组件。这个 React 组件可以与 Bit cloud 共享，以使世界各地的其他用户能够在他们的项目中使用该组件。

## 英美法系的单一责任原则

Angular apps 是由组件组成的。一个组件拥有一个由元素组成的视图。

组件使得从单一、简单的视图单元构建复杂的应用程序变得更加容易。组件使你能够分解复杂的应用程序，并由小单元组成应用程序，而不是一头扎进构建复杂的应用程序。

例如，假设你想开发一个类似脸书的社交媒体应用。你不能只创建 HTML 文件和注入元素。您需要将它分解成小的视图单元，以类似如下的结构组织您的 HTML 文件:

*   Feed 页面
*   个人资料页面
*   注册页面
*   登录页面

每个文件将由组件组成。例如，feed 页面将包括来自我们朋友的 feed、评论、喜欢和分享，等等。这些都需要单独处理。

如果我们将这些组合成组件，我们有一个`FeedList`组件获取从 API 获取的一组提要，还有一个`FeedView`组件处理数据提要的显示。

构建新的角度应用程序时，从以下方面开始:

1.  将应用程序分解成独立的组件
2.  描述每个组件的职责
3.  描述每个组件的输入和输出，即其面向公众的接口。

我们编写的大多数组件都违反了单一责任原则。比方说，我们有一个从某个端点列出电影的应用程序:

```
@Component({
    selector: 'movies',
    template: `
        <div>
            <div>
                <div *ngFor="let movie of movies">
                    <h3>{{movie.name}}</h3>
                    <h3>{{movie.year}}</h3>
                    <h3>{{movie.producer}}</h3>
                    <button (click)="delMovie(movie)">Del</button>
                </div>
            </div>
        </div>
    `
})
export class MoviesComponent implements OnInit {
    this.movies = []
    constructor(private http: Http) {}
ngOnInit() {
        this.http.get('api/movies/').subscribe(data=> {
            this.movies = data.movies
        })
    }
delMovie(movie) {
        // deletion algo
    }
}

```

该组件负责:

*   从`api/movies` API 获取电影
*   管理一系列电影

这对生意不好。为什么？该组件应该负责一项任务或另一项任务；它不可能对两者都负责。

给每个组件分配一个单独的职责是为了使其可重用和可优化。我们需要重构我们的示例组件，将一些责任推给其他组件。另一个组件需要处理 movies 数组，数据获取逻辑应该由一个`Service`类来处理。

```
@Injectable() {
    providedIn: 'root'
}
export class MoviesService {
    constructor(private http: Http) {}
getAllMoives() {...}
    getMovies(id) {...}
    saveMovie(movie: Movie) {...}
    deleteMovie(movie: Movie) {...}
}
@Component({
    selector: 'movies',
    template: `
        <div>
            <div>
                <movies-list [movies]="movies"></movies-list>
            </div>
        </div>
    `
})
export class MoviesComponent implements OnInit {
    this.movies = []
    constructor(private moviesService: MoviesService) {}
ngOnInit() {
        this.moviesService.getAllMovies().subscribe(data=> {
            this.movies = data.movies
        })
    }
}
@Component({
    selector: 'movies-list',
    template: `
        <div *ngFor="let movie of movies">
            <h3>{{movie.name}}</h3>
            <h3>{{movie.year}}</h3>
            <h3>{{movie.producer}}</h3>
            <button (click)="delMovie(movie)">Del</button>
        </div>
    `
})
export class MoviesList {
    @Input() movies = null
delMovie(movie) {
        // deletion algo
    }
}

```

这里，我们分离了`MoviesComponent`中的多个关注点。现在，`MoviesList`处理电影数组，`MoviesComponent`现在是它的父节点，通过电影输入将电影数组发送给`MoviesList`。`MoviesComponent`不知道数组将如何格式化和呈现；这取决于`MoviesList`组件。`MoviesList`的唯一职责是通过电影输入接受电影数组，并显示/管理电影。

假设我们想要在电影简介页面中显示最近的电影或相关电影。我们可以重用电影列表，而无需为它编写新的组件:

```
@Component({
    template: `
    <div>
        <div>
            <h3>Movie Profile Page</h3>
            Name: {{movie.name}}
            Year: {{movie.year}}
            Producer: {{movie.producer}}        
        </div>
<br />
<h4>Movie Description</h4>
        <div>
            {{movie.description}}
        </div>
        <h6>Related Movies</h6>
        <movies-list [movies]="relatedMovies"></movies-list>
    </div>    
    `
})
export class MovieProfile {
    movie: Movie = null;
    relatedMovies = null;
    constructor(private moviesService: MoviesService) {}
}

```

由于我们的`MoviesComponent`用于在应用程序的主页中显示电影，我们可以重用侧边栏中的`MovieList`来显示热门电影、收视率最高的电影、票房最高的电影、最佳动漫电影等。无论如何，`MovieList`组件可以无缝地融入其中。我们还可以在`Movie`类中添加一个额外的属性，这不会破坏我们使用`MovieList`组件的代码。

接下来，我们将电影数据获取逻辑移到了一个`MoviesService`。该服务处理我们的 movies API 上的任何 [CRUD 操作](https://blog.logrocket.com/creating-a-crud-firebase-documents-in-angular/)。

```
@Injectable() {
    providedIn: 'root'
}
export class MoviesService {
    constructor(private http: Http) {}
getAllMovies() {...}
    getMovies(id) {...}
    saveMovie(movie: Movie) {...}
    deleteMovie(movie: Movie) {...}
}

```

`MoviesComponent`注入`MoviesService`并调用它需要的任何方法。关注点分离的一个好处是我们可以优化这个类来防止浪费渲染。

角度变化检测从根组件或触发它的组件开始。`MoviesComponent`渲染`MovieList`；每当 CD 运行时，`MoviesComponent`被重新渲染，然后是`MovieList`。如果输入没有改变，重新呈现组件可能是浪费。

把`MoviesComponent`想成一个智能组件，把`MovieList`想成一个哑组件。为什么？因为`MoviesComponent`获取要渲染的数据，而`MovieList`接收要渲染的电影。如果它不接收任何东西，它就什么也不呈现。

智能组件无法优化，因为它们具有/会导致不可预测的副作用。试图优化它们会导致显示错误的数据。哑组件可以被优化，因为它们是可预测的；他们输出给他们的，他们的图表是线性的。智能组件的图形就像一条分形曲线，有无数的异常差异。

换句话说，智能组件就像不纯的函数，而哑组件是纯函数，就像 Redux 中的[减速器。我们可以通过将`changeDetection`添加到`OnPush`来优化`MovieList`组件:](https://blog.logrocket.com/why-use-redux-reasons-with-clear-examples-d21bffd5835/#reducers)

```
@Component({
    selector: 'movies-list',
    template: `
        <div *ngFor="let movie of movies">
            <h3>{{movie.name}}</h3>
            <h3>{{movie.year}}</h3>
            <h3>{{movie.producer}}</h3>
            <button (click)="delMovie(movie)">Del</button>
        </div>
    `,
    changeDetection: ChangeDetectionStrategy.OnPush
})
export class MoviesList {
    @Input() movies = null
delMovie(movie) {
        // deletion algo
    }
}

```

`MovieList`将仅在以下情况下重新呈现:

*   电影数组输入改变
*   点击`Del`按钮

检查一下。如果电影以前的价值是:

```
[
    {
        name: 'MK',
        year: 'Unknown'
    }
]

```

当前值是:

```
[
    {
        name: 'MK',
        year: 'Unknown'
    },
    {
        name: 'AEG',
        year: '2019'
    }
]

```

组件需要重新呈现以反映新的更改。当我们点击`Del`按钮时，将进行重新渲染。这里，Angular 没有从根开始重新渲染；它从`MovieList`组件的父组件开始。这是因为我们要从 movies 数组中删除一部电影，所以组件应该重新呈现以反映剩余的数组。该组件从其电影数组中删除一部电影，这可能会限制其可重用性。

如果一个父组件想从数组中删除两部电影会发生什么？我们会看到，触摸`MovieList`来适应变化会违反单一责任原则。

它实际上不应该从数组中删除一部电影。它应该发出一个事件，该事件将导致父组件拾取该事件，从其数组中删除一部电影，并将数组中的剩余值传递回组件。

```
@Component({
    selector: 'movies-list',
    template: `
        <div *ngFor="let movie of movies">
            <h3>{{movie.name}}</h3>
            <h3>{{movie.year}}</h3>
            <h3>{{movie.producer}}</h3>
            <button (click)="delMovie(movie)">Del</button>
        </div>
    `,
    changeDetection: ChangeDetectionStrategy.OnPush
})
export class MoviesList {
    @Input() movies = null
    @Output() deleteMovie = new EventEmitter()
delMovie(movie) {
        // deletion algo
        this.deleteMovie.emit(movie)
    }
}

```

因此，如果父组件想要删除两部电影，它可以发出两个事件。

```
@Component({
    selector: 'movies',
    template: `
        <div>
            <div>
                <movies-list [movies]="movies" (deleteMovie)="delMovie"></movies-list>
            </div>
        </div>
    `
})
export class MoviesComponent implements OnInit {
    this.movies = []
    constructor(private moviesService: MoviesService) {}
ngOnInit() {
        this.moviesService.getAllMovies().subscribe(data=> {
            this.movies = data.movies
        })
    }
    delMovie() {
        this.movies.splice(this.movies.length,2)
    }
}

```

如您所见，哑组件基于父组件和用户交互重新呈现，这是可预测的，因此是可优化的。

智能组件可以通过添加`OnPush`变化检测策略进行优化:

```
@Component({
    selector: 'movies',
    template: `
        <div>
            <div>
                <movies-list [movies]="movies"></movies-list>
            </div>
        </div>
    `,
    changeDetection: ChangeDetctionStrategy.OnPush
})
export class MoviesComponent implements OnInit {
    this.movies = []
    constructor(private moviesService: MoviesService) {}
ngOnInit() {
        this.moviesService.getAllMovies().subscribe(data=> {
            this.movies = data.movies
        })
    }
}

```

但这会导致副作用，可能导致它触发多次，使`OnPush`策略完全无效。

哑组件应该构成应用程序的主体，因为它们是可优化的，因此有助于提高性能。使用太多智能组件会使应用程序变慢，因为它们不可优化。

## 单一责任:副作用

当应用程序状态从某个参考点改变时，会产生副作用。对性能有什么影响？

假设我们有这些函数:

```
let globalState = 9
function f1(i) {
    return i * 90
}
function f2(i) {
    return i * globalState
}
f1 can be optimized to stop running when the input is the same as prev, but f2 cannot be optimized because it is unpredictable, it depends on the globalState variable. It will store its prev value but the globalState might have been changed by an external factor it will make optimizing f2 hard. f1 is predictable because it doesn't depend on an outside variable outside its scope.

```

### 反应中的副作用

副作用会导致 React 中的数据过时或不准确。为了防止这种情况，React 提供了一个 [`useEffect`钩子](https://blog.logrocket.com/guide-to-react-useeffect-hook/),我们可以用它来执行回调中的副作用。

```
function SmartComponent() {
  const [token, setToken] = useState('')
  useEffect(() => {
    // side effects code here...
    const _token = localStorage.getItem("token")
    setToken(token)
  })
  return (
    <div>
      Token: {token}
    </div>
  )
}

```

这里，我们使用`localStorage`获取外部数据，这是一个副作用。这是在`useEffect`钩子内部完成的。每当组件安装/更新/卸载时，就会调用`useEffect`钩子中的回调函数。

我们可以通过传递名为依赖数组的第二个参数来优化`useEffect`钩子。变量是`useEffect`在每次更新时检查的内容，以确定是否跳过在 rerender 上运行。

### 角度的副作用

使用`OnPush`优化智能组件时，会导致数据不准确。

就拿我们的`MoviesComponent`来说吧。假设我们使用`OnPush`进行优化，并且有一个接收特定数据的输入。

```
@Component({
    template: `
        ...
        <button (click)="refresh">Refresh</button>
    `,
    changeDetection: ChangeDetectionStartegy.OnPush
})
export class MoviesComponent implements OnInit {
    @Input() data = 9
    this.movies = []
    constructor(private moviesService: MoviesService) {}
ngOnInit() {
        this.moviesService.getAllMovies().subscribe(data=> {
            this.movies = data.movies
        })
    }
refresh() {
        this.moviesService.getAllMovies().subscribe(data=> {
            this.movies = data.movies
        })        
    }
}

```

这个组件通过执行一个 [HTTP 请求](https://blog.logrocket.com/5-ways-to-make-http-requests-in-node-js/)产生了副作用。该请求更改了组件内部的电影数组中的数据，并需要呈现电影数组。我们的数据很有价值。当该组件重新呈现时，可能通过单击一个按钮来运行 refresh 方法，将出现一个 HTTP 请求来从网络获取一组新的电影，并且在该组件上运行一个`ChangeDetection`。如果这个组件的`@Input() data`没有从它的父组件改变，这个组件不会重新渲染，导致电影数组显示不准确。会显示以前的电影，但也会提取新电影。

现在你已经看到副作用的影响了。导致副作用的组件是不可预测的并且难以优化。

副作用包括:

*   HTTP 请求
*   全局状态更改(在 Redux 中)

### `ngrx`效果

[`ngrx`](https://github.com/ngrx) 是对有角的无功扩展的集合。正如我们已经看到的，我们的组件是基于服务的。组件注入服务来执行与网络请求不同的操作，以提供状态。这些服务还注入其他服务来工作，这将导致我们的组件具有不同的职责。

就像在我们的`MoviesComponent`中，它注入了`MoviesService`来对 movies API 执行 CRUD 操作。

该服务还注入 HTTP 服务类来帮助它执行网络请求。这使得我们的`MoviesComponents`依赖于`MoviesService`类。如果`MoviesService`级做出突破性的改变，可能会影响到我们的`MoviesComponent`。想象一下，您的应用程序增长到数百个注入服务的组件；你会发现自己在搜索每一个注入服务的组件来重构它们。

许多基于商店的应用程序整合了由 [RxJS](https://rxjs-dev.firebaseapp.com/) 驱动的副作用模型。效果解除了我们组件的许多责任。

为了展示一个例子，让`MoviesComponent`使用效果并将电影数据移动到`Store`:

```
@Component({
    selector: 'movies',
    template: `
        <div>
            <div>
                <movies-list [movies]="movies | async"></movies-list>
            </div>
        </div>
    `
})
export class MoviesComponent implements OnInit {
    movies: Observable<Movies[]> = this.store.select(state => state.movies)
constructor(private store: Store) {}
ngOnInit() {
        this.store.dispatch({type: 'Load Movies'})
    }
}

```

再也没有`MoviesService`；它已被委托给`MoviesEffects`类:

```
class MoviesEffects {
    loadMovies$ = this.actions.pipe(
        ofType('Load Movies'),
        switchMap(action =>
            this.moviesService.getMovies()
            .map(res => ({ type: 'Load Movies Success',payload: res }))
            .catch(err => Observable.of({ type: 'Load Movies Failure', payload: err }))
            );
    )
constructor(private moviesService: MoviesService, private actions: Actions) {}
}

```

服务`MoviesService`不再是`MoviesComponent`的责任。对`MoviesService`的改动不会影响`MoviesComponent`。

## 容器和表示组件

容器组件是自包含的组件，可以生成和呈现自己的数据。容器组件关心它的内部操作如何在它自己的沙箱边界内工作。

根据[柳文欢·法希](https://books.google.com.ng/books?id=fzckDwAAQBAJ&pg=PA54&lpg=PA54&dq=Oren+Farhi,+a+container+component+is+smart+enough+to+perform+a+few+operations+and+make+some+decisions&source=bl&ots=jtBvG0PgP6&sig=ACfU3U1AJ14FmOGq9rqxBbtFrgj7tONa7g&hl=en&sa=X&ved=2ahUKEwjK2OycmtHuAhUhxYUKHTcNDTkQ6AEwAXoECAMQAg#v=onepage&q=Oren%20Farhi%2C%20a%20container%20component%20is%20smart%20enough%20to%20perform%20a%20few%20operations%20and%20make%20some%20decisions&f=false)的说法，一个容器组件足够智能，可以执行一些操作并做出一些决定:

1.  它通常负责获取可能显示的数据
2.  它可能由几个其他组件组成
3.  它是“有状态的”，这意味着它可以管理特定的状态
4.  它处理内部组件的事件和异步操作

容器组件也称为智能组件。

表示组件从它们的父组件获取数据。如果他们没有从父节点得到输入，他们将不会显示任何数据。它们是哑的，因为它们不能生成自己的数据；这取决于父节点。

## 结论

我们深入研究如何使 React/Angular 中的组件可重用。不仅仅是写代码或者知道如何编码，而是知道如何编码好。

不要从建造复杂的东西开始；由小组件组成。单一责任原则有助于确保我们编写干净且可重用的代码。

## 通过理解上下文，更容易地调试 JavaScript 错误

调试代码总是一项单调乏味的任务。但是你越了解自己的错误，就越容易改正。

LogRocket 让你以新的独特的方式理解这些错误。我们的前端监控解决方案跟踪用户与您的 JavaScript 前端的互动，让您能够准确找出导致错误的用户行为。

[![LogRocket Dashboard Free Trial Banner](img/cbfed9be3defcb505e662574769a7636.png)](https://lp.logrocket.com/blg/javascript-signup)

LogRocket 记录控制台日志、页面加载时间、堆栈跟踪、慢速网络请求/响应(带有标题+正文)、浏览器元数据和自定义日志。理解您的 JavaScript 代码的影响从来没有这么简单过！

[Try it for free](https://lp.logrocket.com/blg/javascript-signup)

.