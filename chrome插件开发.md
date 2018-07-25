# chrome插件开发

> Author: huzi<br>
> Date: 18-7-25 22:24

## 前言

本文参考于：

 - https://github.com/sxei/chrome-plugin-demo
 - http://www.lupaworld.com/article-228139-1.html

使用Demo来源如下：
 
 - https://github.com/sxei/chrome-plugin-demo/tree/master/demo

## Demo介绍

Demo的文件夹结构如下，和一般的开发差距不大：

~~~bash
.
├── _locales
│   ├── en
│   │   └── messages.json
│   └── zh_CN
│       └── messages.json
├── background.html
├── css
│   └── custom.css
├── devtools.html
├── img
│   ├── icon.png
│   └── sds.png
├── js
│   ├── background.js
│   ├── content-script.js
│   ├── devtools.js
│   ├── inject.js
│   ├── jquery-1.8.3.js
│   ├── mypanel.js
│   ├── options.js
│   ├── popup.js
│   └── show-image-content-size.js
├── manifest.json
├── mypanel.html
├── newtab.html
├── options.html
├── popup.html
└── sidebar.html
~~~

 - _locales：用于适配不同语言的chrome，一般用不到。
 - manifest.json：这个文件是核心入口文件，其他文件都是由这个文件为基础。
 - css/img/js：存放资源，脚本。
 - 其他html：manifest.json中的扩展。
 
 总结：chrome开发没有严格的项目结构要求，只需要本目录有manifest.json即可。

## chrome开发核心

### manifest.json

这是chrome插件的配置文件，里面有所有相关插件的配置属性，其中，**manifest_version**、**name**、**version**必须。
文件如下：

~~~json
{
	// 清单文件的版本，这个必须写，而且必须是2
	"manifest_version": 2,
	// 插件的名称
	"name": "demo",
	// 插件的版本
	"version": "1.0.0",
	// 插件描述
	"description": "__MSG_pluginDesc__",
	// 图标，一般偷懒全部用一个尺寸的也没问题
	"icons":
	{
		"16": "img/icon.png",
		"48": "img/icon.png",
		"128": "img/icon.png"
	},
	// 会一直常驻的后台JS或后台页面
	"background":
	{
		// 2种指定方式，如果指定JS，那么会自动生成一个背景页
		"page": "background.html"
		//"scripts": ["js/background.js"]
	},
	// 浏览器右上角图标设置，browser_action、page_action、app必须三选一
	"browser_action": 
	{
		"default_icon": "img/icon.png",
		// 图标悬停时的标题，可选
		"default_title": "这是一个示例Chrome插件",
		"default_popup": "popup.html"
	},
	// 当某些特定页面打开才显示的图标
	/*"page_action":
	{
		"default_icon": "img/icon.png",
		"default_title": "我是pageAction",
		"default_popup": "popup.html"
	},*/
	// 需要直接注入页面的JS
	"content_scripts": 
	[
		{
            // 匹配地址的规则
			// "<all_urls>" 表示匹配所有地址
			"matches": ["<all_urls>"],
			// 多个JS按顺序注入
			"js": ["js/jquery-1.8.3.js", "js/content-script.js"],
			// JS的注入可以随便一点，但是CSS的注意就要千万小心了，因为一不小心就可能影响全局样式
			"css": ["css/custom.css"],
			// 代码注入的时间，可选值： "document_start", "document_end", or "document_idle"，最后一个表示页面空闲时，默认document_idle
			"run_at": "document_start"
		},
		// 这里仅仅是为了演示content-script可以配置多个规则
		{
			"matches": ["*://*/*.png", "*://*/*.jpg", "*://*/*.gif", "*://*/*.bmp"],
			"js": ["js/show-image-content-size.js"]
		}
	],
	// 权限申请
	"permissions":
	[
        // 以下是chrome插件，申请后可以通过chrome.tabs.XXXX使用其功能
		"contextMenus", // 右键菜单
		"tabs", // 标签
		"notifications", // 通知
		"webRequest", // web请求
		"webRequestBlocking", // 阻塞式web请求
		"storage", // 插件本地存储

        // 以下是注入脚本能访问的网站
		"http://*/*", // 可以通过executeScript或者insertCSS访问的网站
		"https://*/*" // 可以通过executeScript或者insertCSS访问的网站
	],
	// 普通页面能够直接访问的插件资源列表，如果不设置是无法直接访问的
	"web_accessible_resources": ["js/inject.js"],
	// 插件主页，这个很重要，不要浪费了这个免费广告位
	"homepage_url": "https://www.baidu.com",
	// 覆盖浏览器默认页面
	"chrome_url_overrides":
	{
		// 覆盖浏览器默认的新标签页
		"newtab": "newtab.html"
	},
	// Chrome40以前的插件配置页写法
	"options_page": "options.html",
	// Chrome40以后的插件配置页写法，如果2个都写，新版Chrome只认后面这一个
	"options_ui":
	{
		"page": "options.html",
		// 添加一些默认的样式，推荐使用
		"chrome_style": true
	},
	// 向地址栏注册一个关键字以提供搜索建议，只能设置一个关键字，不重要
	"omnibox": { "keyword" : "go" },
	// 默认语言
	"default_locale": "zh_CN",
	// devtools页面（即开发工具栏）入口，注意只能指向一个HTML文件，不能是JS文件
	"devtools_page": "devtools.html"
}
~~~

### context-scripts

chrome插件向页面注入的脚本的一种形式。可以向指定页面注入JS和CSS文件。

示例：

~~~json
{
	"content_scripts": 
	[
		{
            // 匹配地址的规则
			// "<all_urls>" 表示匹配所有地址
			"matches": ["<all_urls>"],
			// 多个JS按顺序注入
			"js": ["js/jquery-1.8.3.js", "js/content-script.js"],
			// JS的注入可以随便一点，但是CSS的注意就要千万小心了，因为一不小心就可能影响全局样式
			"css": ["css/custom.css"],
			// 代码注入的时间，可选值： "document_start", "document_end", or "document_idle"，最后一个表示页面空闲时，默认document_idle
			"run_at": "document_start"
		}
	]
}
~~~

对所有地址的页面，chrome插件都会在页面开始加载前，按顺序注入"js/jquery-1.8.3.js"，"js/content-script.js"和"css/custom.css"三个文件。可以通过这三个文件修改原页面。

### background

常驻在后台的脚本，它随着浏览器的打开而打开，随着浏览器的关闭而关闭。它可以无限制跨域。一般用于资源初始化，全局变量设置。

示例：

~~~json
{
	// 会一直常驻的后台JS或后台页面
	"background":
	{
		// 2种指定方式，如果指定JS，那么会自动生成一个背景页
		"page": "background.html"
		//"scripts": ["js/background.js"]
	}
}
~~~