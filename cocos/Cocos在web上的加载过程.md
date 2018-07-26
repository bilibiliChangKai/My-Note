# Cocos在web上的加载过程

> Author：huziang<br>Date：2018年7月19日 星期四 下午8:15

## 加载过程

1. 加载index.html
2. 加载setting.js，用来配置window.\_CCSettings的一些信息。并且导入资源信息。
3. 加载modular.js，用来配置插件信息。实际上是声明map数组，对应函数。
4. 加载boot.js，用来启动项目。