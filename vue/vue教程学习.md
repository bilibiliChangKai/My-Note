# Vue教程学习

> Author：huziang<br>Date：2018年7月19日 星期四 下午8:15

- PS：教程来源于菜鸟教程

## Vue中的属性

- el：用于对应id的DOM子树。
- data：对应el中的数据。data中的数据运行时创建请使用Vue.set()。
- method：对应el中的方法。
- camputed：里面有setter和getter，和method的区别是有缓存，不会刷新重新载入。
- watch：用于监听属性
- component：组件，用于html添加节点。
- directives: 用来注册局部指令。

## Vue中自带属性

Vue中自带属性通过$来表示。

- $watch：实例方法，用来添加回调函数。（可有一个参数或两个参数，函数可以叠加，不会覆盖）

## v-指令

v-指令后面可以跟着.属性。（属性且取值）。

- v-if：if
- v-else：else
- v-else-if：elseif
- v-bind：单项绑定
- v-model：双向绑定
- v-on：事件绑定