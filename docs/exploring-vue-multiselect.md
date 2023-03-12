# 探索 Vue-Multiselect 

> 原文：<https://blog.logrocket.com/exploring-vue-multiselect/>

创建下拉菜单总是很困难，尤其是当我们需要对它应用自定义样式时——`select`元素的功能非常有限。因此，如果我们使用 Vue 来构建我们的应用程序，我们可以使用一些组件来帮助我们的生活变得更加轻松。

在本文中，我们将看看 Vue-Multiselect 库如何帮助改进我们的下拉菜单。

## 入门指南

首先，我们可以通过运行以下命令来安装 Vue-Multiselect:

```
npm install vue-multiselect --save
```

我们还可以通过脚本标签添加库，并添加与包相关联的 CSS:

```
<script src="https://unpkg.com/[email protected]"></script>
<link rel="stylesheet" href="https://unpkg.com/vue-multiselec[email protected]/dist/vue-multiselect.min.css">
```

然后，在我们的组件中，我们可以编写:

```
<template>
  <div>
    <multiselect v-model="value" :options="options"></multiselect>
    <p>{{value}}</p>
  </div>
</template>

<script>
import Multiselect from "vue-multiselect";

export default {
  components: { Multiselect },
  data() {
    return {
      value: null,
      options: ["foo", "baz", "baz"]
    };
  }
};
</script>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
```

`Multiselect`组件被注册到组件中。我们将带有`v-model`的`multiselect`组件绑定到`value`状态。`options`道具被设置为`options`，它有一个字符串数组。

因此，显示给用户的值将与选择的值相同；我们可以从下拉列表下面的`<p>`元素中看到这一点。还要注意，我们从带有`style`标签的包中添加了样式。

## 对象的单一选择

如果我们想向用户显示项目，并保持显示的值不同，那么我们需要一个选项对象数组。

例如，我们写道:

```
<template>
  <div>
    <multiselect track-by="name" label="name" v-model="value" :options="options"></multiselect>
    <p>{{value}}</p>
  </div>
</template>

<script>
import Multiselect from "vue-multiselect";

export default {
  components: { Multiselect },
  data() {
    return {
      value: null,
      options: [
        { name: "Orange", value: "orange" },
        { name: "Apple", value: "apple" },
        { name: "Grape", value: "grape" }
      ]
    };
  }
};
</script>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
```

我们在`name`属性中显示值，因为我们将`label`设置为`name`。现在，当我们选择一个值时，我们选择了整个对象，并且`value`被设置为当我们选择一个项目时选择的对象。

## 添加搜索

默认情况下搜索可用，因为默认情况下`searchable`属性设置为`true`。我们可以使用`custom-label`属性显示下拉条目的自定义文本，我们将该属性设置为一个函数。

例如，我们可以写:

```
<template>
  <div>
    <multiselect
      track-by="name"
      label="name"
      :custom-label="nameFormatter"
      v-model="value"
      :options="options"
    ></multiselect>
    <p>{{value}}</p>
  </div>
</template>

<script>
import Multiselect from "vue-multiselect";

export default {
  components: { Multiselect },
  data() {
    return {
      value: null,
      options: [
        { name: "Orange", color: "orange", value: "orange" },
        { name: "Apple", color: "red", value: "apple" },
        { name: "Grape", color: "purple", value: "grape" }
      ]
    };
  },
  methods: {
    nameFormatter({ name, color }) {
      return `${name} - ${color}`;
    }
  }
};
</script>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
```

现在我们得到了为每个条目显示的`nameFormatter`中返回的内容。

## 多重选择

Vue-Multiselect 也支持多重选择。例如，我们可以写:

```
<template>
  <div>
    <multiselect track-by="name" label="name" v-model="value" :options="options" multiple></multiselect>
    <p>{{value}}</p>
  </div>
</template>

<script>
import Multiselect from "vue-multiselect";

export default {
  components: { Multiselect },
  data() {
    return {
      value: null,
      options: [
        { name: "Orange", value: "orange" },
        { name: "Apple", value: "apple" },
        { name: "Grape", value: "grape" }
      ]
    };
  }
};
</script>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
```

我们在`multiselect`中添加了`multiple`来支持多选。我们可以通过填充`selection`槽来添加文本，以便在选择某样东西时显示，就像这样:

```
<template>
  <div>
    <multiselect track-by="name" label="name" v-model="value" :options="options" multiple>
      <template slot="selection" slot-scope="{ values, search, isOpen }">
        <span v-if="values.length">{{ values.length }} options selected</span>
      </template>
    </multiselect>
    <p>{{value}}</p>
  </div>
</template>

<script>
import Multiselect from "vue-multiselect";

export default {
  components: { Multiselect },
  data() {
    return {
      value: null,
      options: [
        { name: "Orange", value: "orange" },
        { name: "Apple", value: "apple" },
        { name: "Grape", value: "grape" }
      ]
    };
  }
};
</script>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
```

`selection`槽具有带有所选值的`values`属性。`isOpen`表示菜单是否打开，`search`有搜索词。

## 允许标签输入

我们还可以让用户用 Vue-Multiselect 添加标签。

为了让用户添加标签，我们可以编写以下代码:

```
<template>
  <div>
    <multiselect v-model="values" taggable @tag="addTag" :options="options" multiple></multiselect>
    <p>{{values}}</p>
  </div>
</template>

<script>
import Multiselect from "vue-multiselect";

export default {
  components: { Multiselect },
  data() {
    return {
      values: [],
      options: ["orange", "apple", "grape"]
    };
  },
  methods: {
    addTag(newTag) {
      this.options.push(newTag);
      this.values.push(newTag);
    }
  }
};
</script>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
```

我们添加了`taggable`道具让用户输入他们自己的标签，我们通过运行`addTag`方法来监听由`multiselect`发出的`tag`事件。它接受`newTag`参数，这个参数有一个标签名。

在该方法中，我们添加了`this.values`和`this.options`，这样新的标签就可以添加到选项列表和所选值列表中。

## 自定义选项模板

Vue-Multiselect 的一个很棒的特性是下拉菜单可以包含文本和图片。

我们可以写:

```
<template>
  <div>
    <multiselect v-model="values" :options="options">
      <template slot="singleLabel" slot-scope="props">
        <img class="option-image" :src="props.option.img">
        <div>
          <span>{{ props.option.title }}</span>
        </div>
      </template>
      <template slot="option" slot-scope="props">
        <img class="option-image" :src="props.option.img">
        <div>
          <span>{{ props.option.title }}</span>
        </div>
      </template>
    </multiselect>
    <p>{{values}}</p>
  </div>
</template>

<script>
import Multiselect from "vue-multiselect";

export default {
  components: { Multiselect },
  data() {
    return {
      values: [],
      options: [
        {
          title: "orange",
          img:
            "https://secure.webtoolhub.com/static/resources/icons/set114/5cfa0390.png"
        },
        {
          title: "apple",
          img:
            "https://images.squarespace-cdn.com/content/v1/56ed6e3b1bbee05366b9f7a5/1464743651591-TJG1VO66UK1GI9LJ5WDO/ke17ZwdGBToddI8pDm48kHhlTY0to_qtyxq77jLiHTtZw-zPPgdn4jUwVcJE1ZvWhcwhEtWJXoshNdA9f1qD7T-j82ScS_xjTqFYGqFrT72qZ_E0ELtHpOZiWcSG1QwIMeEVreGuQ8F95X5MZTW1Jw/lodi-apple.png?format=300w"
        },
        {
          title: "grape",
          img:
            "https://icons.iconarchive.com/icons/martin-berube/food/256/grapes-icon.png"
        }
      ]
    };
  }
};
</script>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>

<style>
.option-image {
  width: 100px;
}
</style>
```

我们用下拉条目的图像和文本填充`singleLabel`槽。填充`option`槽的方式与填充下拉选项的方式相同。

## 选项组

可以对选项进行分组，而不是将所有选项都放在顶层，如下所示:

```
<template>
  <div>
    <multiselect
      group-values="items"
      group-label="type"
      group-select
      v-model="value"
      :options="options"
      label="name"
    ></multiselect>
    <p>{{value}}</p>
  </div>
</template>

<script>
import Multiselect from "vue-multiselect";

export default {
  components: { Multiselect },
  data() {
    return {
      value: undefined,
      options: [
        {
          type: "fruit",
          items: [{ name: "apple" }, { name: "orange" }]
        },
        {
          type: "drink",
          items: [{ name: "beer" }, { name: "wine" }]
        }
      ]
    };
  }
};
</script>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
```

`options`是具有组标签属性的对象数组，在我们的示例中，组标签是`type`。`items`有下拉组中的项目。

`group-values`被设置为`items`属性以将它们用作组项目，`group-label`被设置为`type`以显示为组标题。`label`被设置为`name`属性来显示给用户。

## Vuex 集成

Vue-Multiselect 与 Vuex 集成，让我们可以将选择存储在 Vuex 存储中，而不是组件中。

* * *

### 更多来自 LogRocket 的精彩文章:

* * *

例如，我们写道:

`main.js`

```
import Vue from "vue";
import App from "./App.vue";
import Vuex from "vuex";

Vue.use(Vuex);

const store = new Vuex.Store({
  state: {
    value: "apple",
    options: ["apple", "orange", "grape"]
  },
  mutations: {
    updateValue(state, value) {
      state.value = value;
    }
  },
  actions: {
    updateValueAction({ commit }, value) {
      commit("updateValue", value);
    }
  }
});

Vue.config.productionTip = false;

new Vue({
  store,
  render: h => h(App)
}).$mount("#app");
```

`App.vue`

```
<template>
  <div>
    <multiselect :value="value" @input="updateValueAction" :options="options"></multiselect>
    <p>{{value}}</p>
  </div>
</template>

<script>
import Multiselect from "vue-multiselect";
import Vuex from "vuex";

const { mapActions, mapState } = Vuex;

export default {
  components: {
    Multiselect
  },
  computed: {
    ...mapState(["value", "options"])
  },
  methods: {
    ...mapActions(["updateValueAction"])
  }
};
</script>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
```

在`main.js`中，我们使用`Vuex.Store`构造函数来创建带有`value`和`options`状态的商店。我们有一个突变来更新值，`updateValueAction`用它来更新`value`状态，然后我们将`store`放入传递给`Vue`构造函数的对象中。

在`App.vue`中，我们没有用`v-model`绑定下拉列表的选择值，而是用`mapState`映射州来从商店中获取州。我们有`mapActions`来映射来自商店的`updateValueAction`来更新值。

我们通过监听`input`事件来获取商品，并调用`updateValueAction`来更新 Vuex 存储中的`value`状态。此外，我们从商店设置`value`道具的值。`input`事件和`value`道具替换`v-model`。

`options`也是通过`mapState`方法从 Vuex 存储的`options`状态设置的。

## 结论

Vue-Multiselect 是一个非常灵活的下拉组件，可以让我们创建包含图片和带格式的下拉项目的下拉列表。

我们还可以对下拉选项进行分组，并启用多重选择和标记。它与 Vuex 集成，允许我们从商店获取和设置选项和价值。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。