# 如何在 TypeScript - LogRocket 博客中完整地设置和编写 Nuxt.js 应用程序

> 原文：<https://blog.logrocket.com/how-to-set-up-and-code-nuxt-js-apps-fully-in-typescript/>

用 TypeScript 编写 JavaScript 代码有助于减少错误和促进协作，还有其他好处。尽管 Nuxt 为在 TypeScript 中编写代码提供了内置支持，但您仍然需要依靠其他一些库来充分利用它的特性。

在本教程中，我们将演示如何在 TypeScript 中构建 Nuxt 应用程序。我们将创建一个新的 Nuxt.js 应用程序并安装几个包。我们开始吧！

## 装置

要安装 Nuxt.js，请输入以下代码行。

```
npx create-nuxt-app nuxt-ts-app

```

您将被要求选择名称、描述、框架等。选择`universal`应用程序，其余选择使用默认设置。

成功创建应用程序后，导航到应用程序目录并安装以下软件包。

```
cd nuxt-ts-app
npm install --save-dev @nuxt/typescript-build

```

现在我们已经加载了所有必需的包。不像 [Vue](https://blog.logrocket.com/how-to-write-a-vue-js-app-completely-in-typescript/) 自动生成配置文件，我们需要手动创建。

## 配置

将`@nuxt/typescript-build`添加到`nuxt.config.js`中的`buildModules`中。

```
// nuxt.config.js
export default {
  buildModules: ['@nuxt/typescript-build']
}

```

创建`tsconfig.json`文件并添加以下内容。

```
// tsconfig.json
{
  "compilerOptions": {
    "target": "es2018",
    "module": "esnext",
    "moduleResolution": "node",
    "lib": [
      "esnext",
      "esnext.asynciterable",
      "dom"
    ],
    "esModuleInterop": true,
    "allowJs": true,
    "sourceMap": true,
    "strict": true,
    "noEmit": true,
    "baseUrl": ".",
    "paths": {
      "~/*": [
        "./*"
      ],
      "@/*": [
        "./*"
      ]
    },
    "types": [
      "@types/node",
      "@nuxt/types"
    ]
  },
  "exclude": [
    "node_modules"
  ]
}

```

现在创建`vue-shim.d.ts`并添加以下内容。

```
declare module "*.vue" {
  import Vue from 'vue'
  export default Vue
}

```

也可以安装`eslint`进行 TypeScript。如果您在创建应用程序时已经选择了`eslist`，您可以先移除它。

```
npm remove @nuxtjs/eslint-config
npm i -D @nuxtjs/eslint-config-typescript

```

现在将 lint 脚本更新为:

```
"lint": "eslint --ext .ts,.js,.vue ."

```

我们可以走了！让我们编写一些打字稿代码来进行双重检查。您可以使用选项 API 样式(普通的)或基于类的组件样式。让我们从两方面来看这件事。

## 选项 API(普通)

这是一种简单的基本类型，我们不需要对 JavaScript 代码做太多改动就可以完成。

语法看起来非常类似于 JavaScript 代码。

```
<template>
  <div class="container">
    <p>FirstName: {{ firstName }}</p>
    <p>LastName: {{ lastName }}</p>
    <p>FullName: {{ fullName }}</p>
    <div>Calculate Age:</div>
    <input v-model="year" type="number" />
    {{ ageText }}
  </div>
</template>
<script lang="ts">
import Vue from 'vue'
export default Vue.extend({
  data() {
    return {
      firstName: 'John',
      lastName: 'Doe',
      year: null,
      ageText: 'Age'
    }
  },
  computed: {
    fullName(): string {
      return this.firstName + this.lastName
    }
  },
  watch: {
    year(newVal: number) {
      this.ageText = this.calculate(newVal)
    }
  },
  methods: {
    calculate(newVal: number): string {
      return 'Age:' + newVal
    }
  }
})
</script>

```

您可以进行基本的输入，比如返回类型`computed properties`和`methods`，传递给`watchers`和`methods`的参数。

## Vuex typing(香草)

[Vuex](https://blog.logrocket.com/whats-new-in-vuex-3-1-1-d796b0c791bf/) 支持开箱即用的基本打字功能。

```
import { GetterTree, ActionTree, MutationTree } from 'vuex'
export const state = () => ({
  count: 0 as number
})
export type RootState = ReturnType<typeof state>
export const getters: GetterTree<RootState, RootState> = {
  count: state => state.count
}
export const mutations: MutationTree<RootState> = {
  CHANGE_COUNT: (state, newVal: number) => (state.count = newVal)
}
export const actions: ActionTree<RootState, RootState> = {
  updateCount({ commit }, newVal) {
    // Some async code
    commit('CHANGE_COUNT', newVal)
  }
}

```

要将这些 Vuex 商店项目映射到您的组件，您仍然需要使用传统的`this.$store`或 Vuex 助手，如`mapState`、`mapMutations`等。

对于使用类和 decorators 语法的更高级的类型，我们使用了基于类的方法。

## 基于类的 API

在基于类的 API 风格中，我们将利用`nuxt-property-decorator`库，它在内部使用`vue-property-decorator`、`vue-class-component`和`vuex-class`，并添加更多特定于 Nuxt 的装饰器。

要安装库，请执行以下操作:

```
npm install --save nuxt-property-decorator

```

让我们看看如何在单文件 Vue 组件中初始化该类。我们在基于类的 API 方面所做的大部分事情与我们在 Vue 中使用基于类的 TypeScript 类似，因为它使用相同的库。然而，Nuxt 还有其他特定的装饰器，我们也会看到。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

### 初始化类

使用下面的代码初始化一个类。

```
//Typescript code
<script lang="ts">
import { Component, Vue } from 'nuxt-property-decorator'
@Component
export default class MyStore extends Vue {
}
</script>

```

相当于 JavaScript 的代码应该是:

```
<script>
export default {
name: 'MyStore'
}
</script>

```

要在 Vue SFC 文件中使用 TypeScript，类似于 Option API 方法，您需要将`script`标记的`lang`属性设置为`ts`。

## 导入组件

在其他组件中注册组件的代码写在`@Component`装饰器中。

```
<script lang="ts">
import Tile from '@/components/Tile.vue'
import { Vue, Component } from 'nuxt-property-decorator'
@Component({
  components: {
    Tile
  }
})
export default class MyStore extends Vue {}
</script>

```

相当于 JavaScript 的代码应该是:

```
<script>
import Tile from '@/components/Tile.vue'
export default {
  name: 'MyStore',
  components: {
    Tile
  }
}
</script>

```

## 使用数据、属性、计算属性、方法、观察器和发出

### 数据

要使用数据属性，只需将它们声明为类变量。

```
export default class MyStore extends Vue {
  title: string = 'Product Categories'
  categoryList: Array<object> = [
    {
      name: 'Phones',
      link: '/phones',
      image: 'iphone-11.png'
    },
    {
      name: 'Laptops',
      link: '/laptops',
      image: 'macbook.png'
    }
  ]
}

```

相当于 JavaScript 的代码如下所示:

```
export default {
  title: 'Product Categories'
  categoryList: [
    {
      name: 'Phones',
      link: '/phones',
      image: 'iphone-11.png'
    },
    {
      name: 'Laptops',
      link: '/laptops',
      image: 'macbook.png'
    }
  ]
}

```

### 小道具

我们可以使用`@Prop`装饰器在我们的 Vue 组件中使用道具。在 Vue 中，我们可以给出道具的附加细节，比如`required`、`default`和`type`。我们首先从`vue-property-decorator`导入`Prop`装饰器，并如下所示编写它。我们也可以使用`readonly`来避免操纵道具。

```
import { Component, Prop, Vue } from 'nuxt-property-decorator'
@Component
export default class Tile extends Vue {
  @Prop({ required: true }) readonly item!: object
  @Prop() quantity!: number
  @Prop({ default: 'Apple' }) brand!: string
  @Prop(String) readonly type!: string
  @Prop({ required: false, type: String, default: 'Available' })
  readonly stock!: string
}

```

相当于 JavaScript 的代码如下。

```
export default {
  props: {
   item: {
      required: true  
  },
   quantity,
   brand: {
      default: 'Apple',
    },
   type: {
      type: String
    },
   stock: {
      required: false,
      type: string,
      default: 'Available'
    }
  }
}

```

### 计算属性

计算属性用于编写简单的模板逻辑，如操作、追加或连接数据。在 TypeScript 中，普通的计算属性也以`get`关键字为前缀。

```
export default class Tile extends Vue {
  get buttonText(): string {
    if (this.quantity) {
      return 'Buy Now!'
    } else {
      return 'Coming Soon!'
    }
  }
}

```

下面是相当于 JavaScript 的代码:

```
export default {
  buttonText() {
   if (this.quantity) {
      return 'Buy Now!'
    } else {
      return 'Coming Soon!'
    }
  }
}

```

您可以在 TypeScript 中编写复杂的计算属性，其中既有`getter`又有`setter`，如下所示。

```
export default class MyStore extends Vue {
 get searchText() {
    return this.searchTextValue
  }
  set searchText(val) {
    this.searchTextValue = val
  }
}

```

相当于 JavaScript 的代码应该是:

```
searchText: {
  get: function () {
    return this.searchTextValue
  },
  set: function (val) {
    this.searchTextValue = val
  }
}

```

### 方法

与普通的类方法一样，TypeScript 中的方法也有一个可选的访问修饰符。

```
import { Vue, Component } from 'nuxt-property-decorator'
@Component
export default class Laptop extends Vue {
  laptopPrice: number = 1400
  quantity: number = 0
  calculateTotal(): number {
    return this.laptopPrice * this.quantity
  }
}

```

相当于 JavaScript 的代码是:

```
export default {
  data() {
    return {
      laptopPrice: 1400
        quantity: 0
    }
  }
  methods: {
    calculateTotal() {
      return this.laptopPrice * this.quantity
    }
  }
}

```

### 观察者

观察器编写方式不同于通常用 JavaScript 编写的方式。JavaScript 中最常用的观察器语法是:

```
watch: {
  total: function(newval) {
    //do something
  }
}

```

开发人员不经常使用处理程序语法。

```
watch: {
  total: {
    handler: 'totalChanged'
  }
}
methods: {
  totalChanged(newVal) {
    // do something
  }
}

```

但是，TypeScript 语法类似于第二种方法。在 TypeScript 中，使用`@Watch` decorator 并传递需要监视的变量的名称。

```
@Watch('name')
totalChanged(newVal: string) {
  if(newVal > 20000) {
    this.status = 'limit exceeded for user'
  }
}

```

我们还可以设置`immediate`和`deep`观察者。

```
@Watch('itemList', { 
  immediate: true, deep: true 
})
itemChanged(newVal: Product, oldVal: Product) {
  // do something
}

```

下面是 JS 等价的代码:

```
watch: {
  itemList: {
      handler: 'itemChanged',
      immediate: true,
      deep: true
    }
}
methods: {
  itemChanged(newVal, oldVal) {
    // do something
  }
}

```

### 发射

要从子组件向父组件发出方法，请使用 TypeScript 中的`@Emit` decorator。

```
@Emit()
addToCount(n: number) {
  this.count += n
}
@Emit('resetData')
resetCount() {
  this.count = 0
}

```

在第一个例子中，函数名`addToCount`被转换为`kebab-case`，类似于 Vue emit 的工作方式。

在第二个例子中，我们传递了方法的显式名称`resetData`,并使用了该名称。由于`addData`在`CamelCase`，所以再次转换为`kebab-case`。

```
<some-component add-to-count="someMethod" />
<some-component reset-data="someMethod" />

//Javascript Equivalent
 methods: {
    addToCount(n) {
      this.count += n
      this.$emit('add-to-count', n)
    },
    resetCount() {
      this.count = 0
      this.$emit('resetData')
    }
}

```

## 生命周期挂钩

一个 Vue 组件有八个生命周期钩子，包括`created`、`mounted`等。特定于 Nuxt 的钩子，比如`asyncData`和`fetch`，使用相同的 TypeScript 语法。这些被声明为普通的类方法。因为生命周期挂钩是自动调用的，它们既不接受参数也不返回任何数据，所以我们不需要访问修饰符、类型参数或返回类型。

```
export default class MyStore extends Vue {
  asyncData() {
    //do something
  }
  beforeUpdate() {
    // do something
  }
}

```

JavaScript 等效代码如下所示。

```
export default {
  asyncData() {
    //do something
  }
  beforeUpdate() {
    // do something
  }
}

```

## 混合蛋白

要在 TypeScript 中创建 mixin，首先创建一个 mixin 文件。这包含您要与其他组件共享的数据。

在`mixins`目录中创建一个名为`CartMixin.ts`的文件，并添加下面的 mixin，它共享项目名和一个更新项目名的方法。

```
/mixins/CartMixin.ts
import { Component, Vue } from 'nuxt-property-decorator'
@Component
class CartMixin extends Vue {
  public cartProducts: Array<object> = []
  public addToCart(newItem: object): void {
    this.cartProducts = { ...this.cartProducts, ...newItem }
  }
}
export default CartMixin

```

在 JavaScript 中，我们应该编写如下代码。

```
export default {
  data() {
    return {
      cartProducts: []
    }
  },
  methods: {
    addToCart(newItem) {
     this.cartProducts = { ...this.cartProducts, ...newItem }
    }
  }
}

```

要在你的 Vue 组件中使用上面的 mixin，从`nuxt-property-decorator`导入`Mixins`和 mixin 文件本身，并编写如下。

```
//pages/phone/index.vue
<template>
  <div class="phones">
    <div class="item">
      <img src="@/assets/images/iphone-11.png" />
      <div>iphone 11</div>
      <button @click="add">Add to Cart</button>
    </div>
    <div class="cart">
      <div v-for="(item, i) in cartProducts" :key="i" class="item">
        <div>Item: {{ item.name}}</div>
        <div>Quantity: {{ item.quantity }}</div>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { Vue, Component, mixins } from 'nuxt-property-decorator'
import CartMixin from '@/mixins/CartMixin'
@Component
export default class Phones extends mixins(CartMixin) {
  public add() {
    this.addToCart({ name: 'phone', quantity: 1 })
  }
}
</script>

```

我们使用 mixin 中的`cartProducts`列表和`addToCart`方法。

相当于 JavaScript 的代码应该是:

```
<template>
  <div class="phones">
    <div class="item">
      <img src="@/assets/images/iphone-11.png" />
      <div>iphone 11</div>
      <button @click="add">Add to Cart</button>
    </div>
    <div class="cart">
      <div v-for="(item, i) in cartProducts" :key="i" class="item">
        <div>Item: {{ item.name}}</div>
        <div>Quantity: {{ item.quantity }}</div>
      </div>
    </div>
  </div>
</template>
<script>
import CartMixin from '@/mixins/CartMixin'
export default {
  mixins: [ CartMixin],
  methods: {
     public add() {
      this.addToCart({ name: 'phone', quantity: 1 })
    }
  }
}
</script>

```

## 武契特

为了用 TypeScript decorators 创建 Vuex 商店，我们将使用一个流行的库`vuex-module-decorators`。您还需要一个名为`vuex-class`的库来在您的组件中使用这些模块。由于`nuxt-property-decorators`内部使用的是`vuex-class`，我们不需要重新安装。

安装`vuex-module-decorators`。

```
npm install -D vuex-module-decorators

```

在`store`文件夹中创建一个名为`users.ts`的新文件。这将是您的用户模块。

要使用 Nuxt 的库，必须显式地将`stateFactory`设置为`True`。

```
import { Module, VuexModule, Mutation } from 'vuex-module-decorators'
interface UserData {
  first: string
  last: string
  address1: string
  address2: string
  state: string
  country: string
  phone: number
}
@Module({
  name: 'user',
  stateFactory: true,
  namespaced: true
})
export default class User extends VuexModule {
  public info: UserData = {
    first: 'Preetish',
    last: 'HS',
    address1: '',
    address2: '',
    state: '',
    country: '',
    phone: 9000000009
  }
  get fullName(): string {
    return this.info.first + ' ' + this.info.last
  }
  @Mutation
  public updateUserInfo(data: UserData) {
    this.info = { ...this.info, ...data }
  }
}

```

`vuex-module-decorators`库为`Module`、`Mutation`和`Action`提供装饰者。像类变量一样，状态变量是直接声明的。

这里我们有一个`getter`来返回全名和突变来更新用户信息。下面是 JavaScript 等价代码。

```
export default {
  namespaced: true,
  state: {
    info: {
      first: 'Preetish',
      last: 'HS',
      address1: '',
      address2: '',
      state: '',
      country: '',
      phone: 9000000009
    }
  },
  getters: {
    fullName() {
      return this.info.first + ' ' + this.info.last
    }
  }
  mutations: {
    updateUserInfo(data) {
      this.info = { ...this.info, ...data }
    }
  }
}

```

### 在组件中使用 Vuex

要使用 Vuex，您将使用一个名为`vuex-class`的库。这个已经从`nuxt-property-decorator`导出了，不需要再安装了。这个库提供了 decorators 来绑定我们 Vue 组件中的`State`、`Getter`、`Mutation`和`Action`。

因为您正在使用命名空间 Vuex 模块，所以首先从`nuxt-property-decorator`导入`namespace`，然后传递模块的名称来访问该模块。

```
<template>
  <div class="user">
    <div class="title">Welcome {{ fullName }}</div>
    <div>
      First:
      <input type="text" v-model="localData.first" />
    </div>
    <button @click="update">Update Info</button>
  </div>
</template>
<script lang="ts">
import { Vue, Component, namespace } from 'nuxt-property-decorator'
const user = namespace('user')
@Component
export default class User extends Vue {
  public localData: object = {}
  @user.State
  public info!: object
  @user.Getter
  public fullName!: string
  @user.Mutation
  public updateUserInfo!: (data: object) => void
  mounted() {
    this.localData = { ...this.localData, ...this.info }
  }
  public update(): void {
    this.updateUserInfo(this.localData)
  }
}
</script>

```

JavaScript 等效代码:

```
<script>
import { mapState, mapGetters, mapMutations } from 'vuex'
export default {
  data() {
    return {
      localData: {}
    }
  },
  computed: {
    ...mapState('user', ['info']),
    ...mapGetters('user', ['fullName'])
  },
  mounted() {
    this.localData = { ...this.localData, ...this.info }
  },
  methods: {
    ...mapMutations('user', ['updateUserInfo']),
    update() {
      this.updateUserInfo(this.localData)
    }
  }
}
</script>

```

访问 [GitHub repo](https://github.com/preetishhs/nuxt-typescript-example) 查看本文中使用的代码片段。

## 结论

现在，您已经拥有了使用一些官方和第三方库完全在 TypeScript 中创建一个 Nuxt.js 应用程序所需的所有基本信息，可以充分利用类型和定制装饰器特性。起初，使用 TypeScript 可能看起来有点势不可挡，但是当您习惯使用它时，您的代码中的错误会少得多，并且在使用相同代码库的其他开发人员之间的代码协作会更加顺畅。

## [LogRocket](https://lp.logrocket.com/blg/typescript-signup) :全面了解您的网络和移动应用

[![LogRocket Dashboard Free Trial Banner](img/d6f5a5dd739296c1dd7aab3d5e77eeb9.png)](https://lp.logrocket.com/blg/typescript-signup)

LogRocket 是一个前端应用程序监控解决方案，可以让您回放问题，就像问题发生在您自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面和移动应用程序重新创建像素级完美视频。

[Try it for free](https://lp.logrocket.com/blg/typescript-signup)

.