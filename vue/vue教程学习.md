# Vue教程学习

> Author：huziang<br>Date：2018年7月19日 星期四 下午8:15

- PS：教程来源于菜鸟教程

## Vue模板

- \<template\>：template内绑定的属性不用写this。

## Vue中的属性

- el：用于对应id的DOM子树。

- data：对应el中的数据。data中的数据运行时创建请使用Vue.set()。

- method：对应el中的方法。

- camputed：里面有setter和getter，和method的区别是有缓存，不会刷新重新载入。

- watch：用于监听属性

- component：组件，用于html添加节点。

- directives: 用来注册局部指令。

- props：组件中通过prop传递参数。

  例如：

  ```vue
  // ---
  Vue.component('blog-post', {
    props: ['title'],
    template: '<h3>{{ title }}</h3>'
  })
  
  // ---
  <blog-post title="My journey with Vue"></blog-post>
  <blog-post title="Blogging with Vue"></blog-post>
  <blog-post title="Why Vue is so fun"></blog-post>
  <blog-post title="title"></blog-post>
  ```

- render：用来渲染页面。

## Vue中自带属性

Vue中自带属性通过$来表示。

- $refs：通过定义template组件中的ref，可以通过this.$refs.XXX直接访问DOM。
- $watch：实例方法，用来添加回调函数。（可有一个参数或两个参数，函数可以叠加，不会覆盖）
- $emit：类似于eval执行对象中的函数，**但是和eval完全不同，大概原理是通过map执行string对应的函数，并不是执行代码**。见：https://cn.vuejs.org/v2/api/#vm-emit。

## v-指令

v-指令后面可以跟着.属性。（属性且取值）。

- v-if：if

- v-else：else

- v-else-if：elseif

- v-bind：单项绑定

- v-model：双向绑定

- v-on：事件绑定。

  PS：事件绑定已有事件和自定义事件不同，绑定已有事件类似于eval，即可以写表达式；绑定自定义事件使用emit，不可以写表达式，必须要写函数名（不带参数）。