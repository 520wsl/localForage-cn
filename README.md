# localForage
[![Build Status](https://travis-ci.org/localForage/localForage.svg?branch=master)](http://travis-ci.org/localForage/localForage)
[![NPM version](https://badge.fury.io/js/localforage.svg)](http://badge.fury.io/js/localforage)
[![Dependency Status](https://img.shields.io/david/localForage/localForage.svg)](https://david-dm.org/localForage/localForage)
[![npm](https://img.shields.io/npm/dm/localforage.svg?maxAge=2592000)]()
[![jsDelivr Hits](https://data.jsdelivr.com/v1/package/npm/localforage/badge?style=rounded)](https://www.jsdelivr.com/package/npm/localforage)

localForage 是一个快速而简单的 JavaScript 存储库。通过使用异步存储（IndexedDB 或 WebSQL）和简单的类 localStorage 的 API ，localForage 能改善 Web 应用的离线体验。

在不支持 IndexedDB 或 WebSQL 的浏览器中，localForage 使用 localStorage。有关 [兼容性详情，请参阅 wiki][supported browsers]。

使用 localForage，只需将 JavaScript 文件放入页面即可：

```html
<script src="localforage/dist/localforage.js"></script>
<script>localforage.getItem('something', myCallback);</script>
```
试一试 [示例](http://codepen.io/thgreasi/pen/ojYKeE)。

Try the [live example](http://codepen.io/thgreasi/pen/ojYKeE).

下载 [GitHub 上最新的 localForage ](https://github.com/localForage/localForage/releases/latest)，或通过[npm](https://www.npmjs.com/) 安装：

```bash
npm install localforage
```

或通过 [bower](http://bower.io)：

```bash
bower install localforage
```

localForage 也兼容 [browserify](http://browserify.org/)。

[supported browsers]: https://github.com/localForage/localForage/wiki/Supported-Browsers-Platforms

## 支持

感到迷茫？需要帮助？试一试 [localForage API 文档](https://localforage.github.io/localForage)。

如果你正在使用本库，运行测试或想为 localForage 做出贡献，可访问 [irc.freenode.net](https://freenode.net/) 并在`#localforage` 频道咨询有关 localForage 的问题。

最佳咨询人员是 [**tofumatt**][tofumatt]，他的在线时间为一般 10 am - 8 pm GMT。

[tofumatt]: http://tofumatt.com/

## Safari 10.1+

从 Safari 10.1 开始，默认为 IndexedDB；请参阅 [CHANGELOG](https://github.com/localForage/localForage/blob/master/CHANGELOG.md) 了解更多。

# 如何使用 localForage

## 回调函数 vs Promises

由于 localForage 使用异步存储，因此 API 是异步的。在其他方面，与 [localStorage API](https://hacks.mozilla.org/2009/06/localstorage/) 完全相同。

localForage 支持两种 API，你可以使用回调函数形式或 Promise。如果你不确定哪一个更适合你，那么建议使用 Promise。

下面是一个回调函数形式的示例：

```js
localforage.setItem('key', 'value', function (err) {
  // 若 err 为非 null，则表示出错
  localforage.getItem('key', function (err, value) {
    // 若 err 为非 null，则表示出错，否则 value 为 key 对应的值
  });
});
```

Promise 形式：

```js
localforage.setItem('key', 'value').then(function () {
  return localforage.getItem('key');
}).then(function (value) {
  // 成功获取值
}).catch(function (err) {
  // 出错了
});
```

更多的示例，请访问 [API 文档](https://localforage.github.io/localForage)。

## 存储 Blobs，TypedArrays 或其他类型 JS 对象

你可以在 localForage 中存储任何类型; 不像 localStorage 只能存储字符串。即使后端的存储形式为 localStorage，当需要获取/设置值时，localForage 也会自动执行 `JSON.parse()` 和 `JSON.stringify()`。

只要是可以序列化为 JSON 的原生 JS 对象，localForage 都可以存储，包括 ArrayBuffers，Blob 和 TypedArrays。在 [API 文档][api] 可查看 localForage 支持所有类型。

所有的后端存储驱动支持所有类型在，但 localStorage 有存储限制，所以无法存储大型 Blob。

[api]: https://localforage.github.io/localForage/#data-api-setitem

## 配置

你可以通过 `config()` 方法设置数据库信息。可用的选项有`driver`，`name`，`storeName`，`version`，`size`，和 `description`。

示例：
```javascript
localforage.config({
    driver      : localforage.WEBSQL, // 使用 WebSQL；也可以使用 setDriver()
    name        : 'myApp',
    version     : 1.0,
    size        : 4980736, // 数据库的大小，单位为字节。现仅 WebSQL 可用
    storeName   : 'keyvaluepairs', // 仅接受字母，数字和下划线
    description : 'some description'
});
```

**注意：**在数据交互之前，你必须先调用 `config()`。即在使用 `getItem()`，`setItem()`，`removeItem()`，`clear()`，`key()`，`keys()` 或 `length()` 前要先调用 `config()`。

## 多实例

通过 `createInstance` 方法，你可以创建多个 localForage 实例，且能指向不同数据仓库。所有 [config](#api-config) 中的配置选项都可用。

``` javascript
var store = localforage.createInstance({
  name: "nameHere"
});

var otherStore = localforage.createInstance({
  name: "otherName"
});

// 设置某个数据仓库 key 的值不会影响到另一个数据仓库
store.setItem("key", "value");
otherStore.setItem("key", "value2");
```

## RequireJS

你可以通过 [RequireJS](http://requirejs.org/) 使用 localForage：

```javascript
define(['localforage'], function(localforage) {
    // 作为回调函数
    localforage.setItem('mykey', 'myvalue', console.log);

    // 使用 Promise
    localforage.setItem('mykey', 'myvalue').then(console.log);
});
```

## Browserify 和 Webpack

localForage 1.3+ 支持 Browserify 和 Webpack。如果你使用的是早期版本的 localForage，且在与 Browserify 或 Webpack 搭配使用时有问题，请升级至 1.3.0 或更高版本。

在预构建正常的 JavaScript 文件的时候，Webpack 可能会提示警告信息。如果你想删除警告，你可以使用下面的配置，在 Webpack 解析时忽略 `localforage`：

```javascript
module: {
  noParse: /node_modules\/localforage\/dist\/localforage.js/,
  loaders: [...],
```

## TypeScript

若你在 [tsconfig.json](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)（支持 TypeScript v1.8+）中将 [`allowSyntheticDefaultImports` 编译选项](https://www.typescriptlang.org/docs/handbook/compiler-options.html) 设置为 `true`，那么你应该这样使用：

```javascript
import localForage from "localforage";
```

否则，你应该使用以下方法中的一种：

```javascript
import * as localForage from "localforage";
// 若你用的 TypeScript 版本不支持 ES6 风格导入像 localForage 这样的 UMD 模块，则用如下方式导入：
import localForage = require("localforage");
```

## 框架支持

If you use a framework listed, there's a localForage storage driver for the
models in your framework so you can store data offline with localForage. We
have drivers for the following frameworks:

若你使用框架，为方便你直接使用 localForage 离线存储数据，localForage 为如下几种框架都提供了模块作为驱动，如下：

* [AngularJS](https://github.com/ocombe/angular-localForage)
* [Angular 4+](https://www.npmjs.com/package/ngforage)
* [Backbone](https://github.com/localForage/localForage-backbone)
* [Ember](https://github.com/genkgo/ember-localforage-adapter)
* [Vue](https://github.com/dmlzj/vlf)

如果你有其他驱动，同时也像将其加入此列表，请 [提出 issue](https://github.com/localForage/localForage/issues/new)。

## 自定义驱动

你可以创建你自己的驱动; 参阅 [`defineDriver`](https://localforage.github.io/localForage/#driver-api-definedriver) API 文档。

Wiki 中有 [自定义驱动列表][custom drivers]。

[custom drivers]: https://github.com/localForage/localForage/wiki/Custom-Drivers

# 修改 localForage

你需要有 [node/npm](http://nodejs.org/) 和 [bower](http://bower.io/#installing-bower)。

要使用 localForage，你需要先 [fork](https://github.com/localForage/localForage/fork) 并安装依赖。将 `USERNAME` 替换为你的 GitHub 用户名，并运行以下命令：

```bash
# 若你没有安装过 bower，则需要先全局安装 bower
npm install -g bower

# 将 USERNAME 替换为你的 GitHub 用户名：
git clone git@github.com:USERNAME/localForage.git
cd localForage
npm install
bower install
```

缺少 bower 依赖会导致测试失败！

## 执行测试

本地执行测试需要安装 PhantomJS。执行 `npm test`（或直接：`grunt test`）。你的代码必须通过 [linter](http://jshint.com/) 的检查。

localForage 仅能在浏览器中运行，因此执行测试需要一个浏览器环境。本地测试在无头 WebKit 浏览器上运行（使用 [PhantomJS](http://phantomjs.org)）。

localForage 通过 [Sauce Labs](https://saucelabs.com/) 支持 Travis CI，当你提交 Pull request 时，将在所有浏览器中自动执行测试。

# 许可协议

This program is free software; it is distributed under an
[Apache License](https://github.com/localForage/localForage/blob/master/LICENSE).

---

Copyright (c) 2013-2016 [Mozilla](https://mozilla.org)
([Contributors](https://github.com/localForage/localForage/graphs/contributors)).
