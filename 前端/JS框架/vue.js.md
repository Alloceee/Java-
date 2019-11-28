# Vue

官网：http://doc.vue-js.com

## 起步

### 什么是Vue

Vue.js（读音 /vjuː/, 类似于 **view**） 是一套构建用户界面的 **渐进式框架**。与其他重量级框架不同的是，Vue 采用自底向上增量开发的设计。Vue 的核心库只关注视图层，并且非常容易学习，非常容易与其它库或已有项目整合。另一方面，Vue 完全有能力驱动采用[单文件组件](http://doc.vue-js.com/v2/guide/single-file-components.html)和[Vue生态系统支持的库](http://github.com/vuejs/awesome-vue#libraries--plugins)开发的复杂单页应用。

Vue.js 的目标是通过尽可能简单的 API 实现**响应的数据绑定**和**组合的视图组件**。

### 引入

```html
<script src="https://unpkg.com/vue/dist/vue.js">
```

### 声明式渲染

 Vue.js 的核心是一个允许你采用简洁的模板语法来声明式的将数据渲染进 DOM 的系统： 

```html
<div id="app">
    {{ message }}
</div>
```

```javascript
var app = new vue({
    el:'#app',
    data:{
        message:'hello vue'
    }
})
```

