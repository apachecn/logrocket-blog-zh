# Vue.js carousel 库的最佳选择

> 原文：<https://blog.logrocket.com/top-picks-vue-js-carousel-libraries/>

要创建幻灯片，我们必须动态创建显示图像，并添加导航按钮在它们之间导航。我们可能还需要自动播放功能。

这些都需要时间来建立，而且很有可能我们可以更好地利用这些时间做些别的事情。在这篇文章中，我们将看看 Vue.js 的一些 carousel 库，让我们的生活变得更轻松，并还给我们一些宝贵的时间。

## vista easy slider(轻松滑动器)

[vue-easy-slider](https://github.com/shhdgit/vue-easy-slider) 是一个简单的 vue 滑块组件，可以用于鼠标和触摸屏。它是可定制的，带有动画效果。

要使用它，我们通过运行以下命令来安装它:

```
npm i -S vue-easy-slider
```

然后我们可以通过写来使用它:

```
//main.js

import Vue from "vue";
import App from "./App.vue";
import EasySlider from "vue-easy-slider";

Vue.use(EasySlider);
Vue.config.productionTip = false;

new Vue({
  render: h => h(App)
}).$mount("#app");
```

```
//App.vue

<template>
  <div id="app">
    <slider animation="fade">
      <slider-item v-for="(i, index) in list" :key="index">
        <img :src="i">
      </slider-item>
    </slider>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      index: 0,
      list: Array(5)
        .fill()
        .map((_, i) => `http://lorempixel.com/400/200/sports/${i}/`)
    };
  }
};
</script>
```

我们使用`slider`组件添加旋转木马，`animation`道具设置动画效果。顾名思义，`slider-item`持有滑块项目。

我们也可以改变风格。例如，我们可以通过编写以下内容来更改宽度和高度:

```
<template>
  <div id="app">
    <slider animation="fade" width="400px" height="250px">
      <slider-item v-for="(i, index) in list" :key="index">
        <img :src="i">
      </slider-item>
    </slider>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      index: 0,
      list: Array(5)
        .fill()
        .map((_, i) => `http://lorempixel.com/400/200/sports/${i}/`)
    };
  }
};
</script>
```

我们还可以启用或禁用触摸控制，并改变幻灯片的变化速度。它还发出一些事件，如`change`，当改变幻灯片时触发。当显示下一张或上一张幻灯片时，`next`和`previous`触发。

它还为项目占位符提供了一些插槽，以便在加载幻灯片时显示一些内容。

## 视图-awesome-swiper

vue-awesome-swiper 是 vue 应用程序的另一个易于使用的轮播组件。它基于 [Swiper](https://swiperjs.com/) ，所以我们必须将它安装在软件包旁边:

```
npm install swiper vue-awesome-swiper --save
```

然后我们可以通过写来使用它:

```
//app.js

import Vue from "vue";
import App from "./App.vue";
import VueAwesomeSwiper from "vue-awesome-swiper";
import "swiper/css/swiper.css";

Vue.use(VueAwesomeSwiper);
Vue.config.productionTip = false;

new Vue({
  render: h => h(App)
}).$mount("#app");

```

```
//App.vue

<template>
  <div id="app">
    <swiper ref="mySwiper" :options="swiperOptions">
      <swiper-slide v-for="(i, index) in list" :key="index">
        <img :src="i">
      </swiper-slide>
      <div class="swiper-pagination" slot="pagination"></div>
    </swiper>
  </div>
</template>

<script>
export default {
  data() {
    return {
      list: Array(5)
        .fill()
        .map((_, i) => `http://lorempixel.com/400/200/sports/${i}/`),
      swiperOptions: {
        pagination: {
          el: ".swiper-pagination"
        }
      }
    };
  }
};
</script>
```

这里，我们使用带有一些选项的`swiper`组件。`swiper-slide`组件保存幻灯片，所以我们把图像放在里面。我们还把`swiperOptions`传到了`options`道具里。

我们用`pagination`槽填充内容，并为分页设置一个类，这样我们就可以对其进行样式化。

我们也可以以编程方式转到幻灯片。这就是为什么我们给滑块分配一个 ref 的原因——来切换到我们想要的幻灯片。例如，我们可以写:

```
//App.js

<template>
  <div id="app">
    <swiper ref="mySwiper" :options="swiperOptions">
      <swiper-slide v-for="(i, index) in list" :key="index">
        <img :src="i">
      </swiper-slide>
      <div class="swiper-pagination" slot="pagination"></div>
    </swiper>
  </div>
</template>

<script>
export default {
  data() {
    return {
      list: Array(5)
        .fill()
        .map((_, i) => `http://lorempixel.com/400/200/sports/${i}/`),
      swiperOptions: {
        pagination: {
          el: ".swiper-pagination"
        }
      }
    };
  },
  computed: {
    swiper() {
      return this.$refs.mySwiper.$swiper;
    }
  },
  mounted() {
    this.swiper.slideTo(2, 1000, false);
  }
};
</script>

```

我们用分配给我们的`slider`的`mySwiper` ref 创建了一个计算属性，然后我们调用`slideTo`方法切换到具有给定索引的幻灯片。索引在第一个参数中，第二个参数是延迟，第三个参数指示我们是否要运行回调。如果我们将它设置为`true`，它将在转换期间发出事件。

像 vue-easy-slider 一样，这个转盘库也有动画效果。

## Vue.js 的 Slick

Vue.js 库的 [Slick 为我们提供了另一个滑块。jQuery 是一个依赖项，所以我们也必须安装它。要安装依赖项，请运行:](https://github.com/staskjs/vue-slick)

```
npm install jquery vue-slick --save
```

接下来，我们可以通过书写来使用它:

```
//main.js

import Vue from "vue";
import App from "./App.vue";
import "slick-carousel/slick/slick.css";

Vue.config.productionTip = false;

new Vue({
  render: h => h(App)
}).$mount("#app");

```

```
//App.vue

<template>
  <slick ref="slick" :options="slickOptions">
    <div v-for="(i, index) in list" :key="index">
      <img :src="i">
    </div>
  </slick>
</template>

<script>
import Slick from "vue-slick";

export default {
  components: { Slick },
  data() {
    return {
      slickOptions: {
        slidesToShow: 1
      },
      list: Array(5)
        .fill()
        .map((_, i) => `http://lorempixel.com/400/200/sports/${i}/`)
    };
  },
  methods: {}
};
</script>
```

我们注册了插件`main.js`并导入了 CSS，然后我们使用`slick`组件来创建我们的 carousel。我们传入属性设置为`1`的`slickOptions`对象，只显示一张幻灯片。通过使用`v-for`渲染图像来创建幻灯片。

它发出许多事件:

*   我们可以监听`afterChange`事件，它是在幻灯片发生变化时发出的
*   在幻灯片更改前发出`beforeChange`
*   滑块卸载时发出`destroy`
*   执行滑动动作时会触发`swipe`
*   还有更多

如果我们更改图像列表，我们还可以调用`this.$refs.slick.reSlick();`用最新的图像更新我们的幻灯片。

## 视图闪烁

接下来是 [vue-flickity](https://github.com/drewjbartlett/vue-flickity) ，它为我们提供了许多定制选项。要安装它，我们可以运行:

```
npm install vue-flickity --save
```

让我们来看看如何使用它:

```
//App.vue

<template>
  <div>
    <flickity ref="flickity" :options="flickityOptions">
     <div class="carousel-cell" v-for="(i, index) in list" :key="index">
      <img :src="i">
    </div>
    </flickity>

    <button @click="previous()">Previous</button>
    <button @click="next()">Next</button>
  </div>
</template>

<script>
import Flickity from "vue-flickity";

export default {
  components: {
    Flickity
  },

  data() {
    return {
      flickityOptions: {
        initialIndex: 3,
        prevNextButtons: false,
        pageDots: false,
        wrapAround: true
      },
      list: Array(5)
        .fill()
        .map((_, i) => `http://lorempixel.com/400/200/sports/${i}/`)
    };
  },

  methods: {
    next() {
      this.$refs.flickity.next();
    },

    previous() {
      this.$refs.flickity.previous();
    }
  }
};
</script>
```

我们使用`flickity`组件来添加我们的滑块，它可以通过`options` prop 定制。我们用它通过`initialIndex`属性来设置初始幻灯片。

`prevNextButton`让我们显示或隐藏内置的导航按钮，我们可以选择是否要用`pageDots`显示导航点。`wrapAround`表示当我们到达转盘的终点或起点时，分别返回到第一张或最后一张幻灯片。

我们还为导航添加了一些自定义按钮。我们创建了一个用于导航到上一张幻灯片，一个用于导航到下一张幻灯片。`ref`获取 ref 并让我们调用`next`或`previous`方法来分别移动到下一张或上一张幻灯片。

我们可以用 CSS 来设计旋转木马和幻灯片。它将 carousel 添加到了`carousel-cell`类中，这样我们就可以对它进行样式化。内置的导航按钮和点也有它们的类，这意味着我们可以很容易地设计它们的样式。

## 三维胡萝卜视图

Vue Carousel 3D 是一个有趣的滑块。它以 3D 幻灯片显示幻灯片，而不是 2D。当我们浏览幻灯片时，正在显示的幻灯片在其他幻灯片之前创建，当我们翻阅幻灯片时，它会显示一种旋转效果。

要安装它，我们运行:

```
npm install -S vue-carousel-3d
```

为了使用它，我们写:

```
//main.js

import Vue from "vue";
import App from "./App.vue";
import Carousel3d from "vue-carousel-3d";

Vue.use(Carousel3d);
Vue.config.productionTip = false;

new Vue({
  render: h => h(App)
}).$mount("#app");

```

```
//App.vue

<template>
  <div>
    <carousel-3d>
      <slide v-for="(i, index) in list" :key="index" :index="index">
        <img :src="i">
      </slide>
    </carousel-3d>
  </div>
</template>

<script>
export default {
  data() {
    return {
      list: Array(5)
        .fill()
        .map((_, i) => `http://lorempixel.com/400/200/sports/${i}/`)
    };
  }
};
</script>
```

我们使用`carousel-3d`组件添加转盘，然后使用`slide`组件添加幻灯片。我们传入`index`，以便载玻片被正确放置。3D 样式是内置的。

除此之外，它还有一些定制选项。我们可以:

*   使用`autoplay`按钮启用或禁用自动播放
*   改变偏向，当我们有偶数张幻灯片时，偏向较大的一边
*   将转盘设置为无限循环
*   调整宽度和高度
*   自定义滑动距离并控制宽度和高度

## 裁决和结论

大多数库都非常相似，Vue Carousel 3D 无疑是最独特的。其他都是 2D 与类似的定制选项。

它们的性能相似，而且都相当容易使用。都可以在触摸屏上使用，都有动画效果。

Slick for Vue.js 会发出很多事件，所以如果我们需要在幻灯片改变或类似的情况下做一些事情，这可能是我们应该选择的库。如果我们希望每页显示多张幻灯片，Slick for Vue.js 也很有用。

## 像用户一样体验您的 Vue 应用

调试 Vue.js 应用程序可能会很困难，尤其是当用户会话期间有几十个(如果不是几百个)突变时。如果您对监视和跟踪生产中所有用户的 Vue 突变感兴趣，

[try LogRocket](https://lp.logrocket.com/blg/vue-signup)

.

[![LogRocket Dashboard Free Trial Banner](img/0d269845910c723dd7df26adab9289cb.png)](https://lp.logrocket.com/blg/vue-signup)[https://logrocket.com/signup/](https://lp.logrocket.com/blg/vue-signup)

LogRocket 就像是网络和移动应用程序的 DVR，记录你的 Vue 应用程序中发生的一切，包括网络请求、JavaScript 错误、性能问题等等。您可以汇总并报告问题发生时应用程序的状态，而不是猜测问题发生的原因。

LogRocket Vuex 插件将 Vuex 突变记录到 LogRocket 控制台，为您提供导致错误的环境，以及出现问题时应用程序的状态。

现代化您调试 Vue 应用的方式- [开始免费监控](https://lp.logrocket.com/blg/vue-signup)。