# localForage

**改进的离线存储**

```js
// 通过 localStorage 设置值
localStorage.setItem('key', JSON.stringify('value'));
doSomethingElse();

// 通过 localForage 完成同样功能
localforage.setItem('key', 'value').then(doSomethingElse);

// localForage 同样支持回调函数
localforage.setItem('key', 'value', doSomethingElse);
```

localForage 是一个 JavaScript 库，通过简单类似 `localStorage` API 的异步数据存储来改进你的 Web 应用程序的离线体验。它能存储多种类型的数据，而不仅仅是字符串。

localForage 有一个优雅降级策略，若浏览器不支持 IndexedDB 或 WebSQL，则使用 localStorage。在所有主流浏览器中都可用：Chrome，Firefox，IE 和 Safari（包括 Safari Mobile）。

**localForage 提供回调 API 同时也支持 [ES6 Promises API][]**，因此你可以自行选择。

[下载 localforage.min.js][download]

[download]: https://raw.githubusercontent.com/mozilla/localForage/master/dist/localforage.min.js
[ES6 Promises API]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise

# 安装

```bash
# 通过 npm 安装：
npm install localforage

# 或通过 bower：
bower install localforage
```

```html
<script src="localforage.js"></script>
<script>console.log('localforage is: ', localforage);</script>
```

使用 localForage，请 [下载最新版本](https://github.com/mozilla/localForage/releases) 或使用 [npm](https://www.npmjs.org/)（`npm install localforage`）或 [bower](http://bower.io/)（`bower install localforage`）进行安装。

然后，只需包含 JS 文件即可使用 localForage：`<script src="localforage.js"></script>`。你不需要运行任何初始化方法或等待 `onready` 事件。

# 数据 API

在离线存储时，通过这些 API 获取和设置数据。

## getItem

```js
localforage.getItem('somekey').then(function(value) {
    // 当离线存储的值被载入时，此处代码运行
    console.log(value);
}).catch(function(err) {
    // 当出错时，此处代码运行
    console.log(err);
});

// 回调版本：
localforage.getItem('somekey', function(err, value) {
    // 当离线存储的值被载入时，此处代码运行
    console.log(value);
});
```

`getItem(key, successCallback)`

从存储中获取 key 对应的值并将结果提供给回调函数。如果 key 不存在，`getItem()` 将返回 `null`。

<aside class="notice">
  当存储 `undefined` 时， `getItem()` 也会返回 `null`。由于 [localStorage 限制](https://github.com/mozilla/localForage/pull/42)，同时出于兼容性的原因 localForage 无法存储 `undefined`。
</aside>

## setItem

```js
localforage.setItem('somekey', 'some value').then(function (value) {
    // 当值被存储后，可执行其他操作
    console.log(value);
}).catch(function(err) {
    // 当出错时，此处代码运行
    console.log(err);
});

// 不同于 localStorage，你可以存储非字符串类型
localforage.setItem('my array', [1, 2, 'three']).then(function(value) {
    // 如下输出 `1`
    console.log(value[0]);
}).catch(function(err) {
    // 当出错时，此处代码运行
    console.log(err);
});

// 你甚至可以存储 AJAX 响应返回的二进制数据
req = new XMLHttpRequest();
req.open('GET', '/photo.jpg', true);
req.responseType = 'arraybuffer';

req.addEventListener('readystatechange', function() {
    if (req.readyState === 4) { // readyState 完成
        localforage.setItem('photo', req.response).then(function(image) {
            // 如下为一个合法的 <img> 标签的 blob URI
            var blob = new Blob([image]);
            var imageURI = window.URL.createObjectURL(blob);
        }).catch(function(err) {
          // 当出错时，此处代码运行
          console.log(err);
        });
    }
});
```

`setItem(key, value, successCallback)`

将数据保存到离线存储。你可以存储如下类型的 JavaScript 对象：

* **`Array`**
* **`ArrayBuffer`**
* **`Blob`**
* **`Float32Array`**
* **`Float64Array`**
* **`Int8Array`**
* **`Int16Array`**
* **`Int32Array`**
* **`Number`**
* **`Object`**
* **`Uint8Array`**
* **`Uint8ClampedArray`**
* **`Uint16Array`**
* **`Uint32Array`**
* **`String`**

<aside class="notice">
  当使用 localStorage 和 WebSQL 作为后端时，二进制数据在保存（和检索）之前会被序列化。在保存二进制数据时，序列化会导致大小增大。
</aside>

<a href="http://jsfiddle.net/ryfo1jk4/161/">示例</a>

## removeItem

```js
localforage.removeItem('somekey').then(function() {
    // 当值被移除后，此处代码运行
    console.log('Key is cleared!');
}).catch(function(err) {
    // 当出错时，此处代码运行
    console.log(err);
});
```

`removeItem(key, successCallback)`

从离线存储中删除 key 对应的值。

<a href="http://jsfiddle.net/y1Ly0hk1/37/">示例</a>

## clear

```js
localforage.clear().then(function() {
    // 当数据库被全部删除后，此处代码运行
    console.log('Database is now empty.');
}).catch(function(err) {
    // 当出错时，此处代码运行
    console.log(err);
});
```

`clear(successCallback)`

Removes every key from the database, returning it to a blank slate.
从数据库中删除所有的 key，将数据库清空。

<aside class="warning">
  `localforage.clear()` 将会删除**离线存储中的所有值**。谨慎使用此方法。
</aside>

## length

```js
localforage.length().then(function(numberOfKeys) {
    // 输出数据库的大小
    console.log(numberOfKeys);
}).catch(function(err) {
    // 当出错时，此处代码运行
    console.log(err);
});
```

`length(successCallback)`

获取离线存储中的 key 的数量（即数据库的“大小”）。

## key

```js
localforage.key(2).then(function(keyName) {
    // key 名
    console.log(keyName);
}).catch(function(err) {
    // 当出错时，此处代码运行
    console.log(err);
});
```

`key(keyIndex, successCallback)`

根据 key 的索引获取其名

<aside class="notice">
  虽然是从 localStorage API 延续而来的，但此方法被认为有点怪异。
</aside>

## keys

```js
localforage.keys().then(function(keys) {
    // 包含所有 key 名的数组
    console.log(keys);
}).catch(function(err) {
    // 当出错时，此处代码运行
    console.log(err);
});
```

`keys(successCallback)`

获取数据仓库中所有的 key。

## iterate

```js
// 同样的代码，但使用 ES6 Promises
localforage.iterate(function(value, key, iterationNumber) {
    // 此回调函数将对所有 key/value 键值对运行
    console.log([key, value]);
}).then(function() {
    console.log('Iteration has completed');
}).catch(function(err) {
    // 当出错时，此处代码运行
    console.log(err);
});

// 提前退出迭代：
localforage.iterate(function(value, key, iterationNumber) {
    if (iterationNumber < 3) {
        console.log([key, value]);
    } else {
        return [key, value];
    }
}).then(function(result) {
    console.log('Iteration has completed, last iterated pair:');
    console.log(result);
}).catch(function(err) {
    // 当出错时，此处代码运行
    console.log(err);
});
```

`iterate(iteratorCallback, successCallback)`

迭代数据仓库中的所有 value/key 键值对。

`iteratorCallback` is called once for each pair, with the following arguments:
`iteratorCallback` 在每一个键值对上都会调用一次，其参数如下：
1. value 为值
2. key 为键名
3. iterationNumber 为迭代索引 - 数字

<aside class="notice">
  通过在 `iteratorCallback` 回调函数中返回一个非 `undefined` 的值，能提前退出 <code>iterate</code>。`iteratorCallback` 的返回值即作为整个迭代的结果，将被传入 `successCallback`。
    <br><br>
  这意味着如果你使用的是 CoffeeScript，那么你需要手动执行一个不带内容的 `return` 语句才能继续迭代所有的 key/value 键值对。
</aside>

# 设置 API

These methods allow driver selection and database configuration. These methods should generally be called before the first _data_ API call to localForage (i.e. before you call `getItem()` or `length()`, etc.)

通过这些方法可选择驱动和配置数据库。这些方法通常应该在第一个 `数据 API` 调用之前调用（即在你调用 `getItem()` 或 `length()` 之前）

## setDriver

```js
// 强制设置 localStorage 为后端的驱动
localforage.setDriver(localforage.LOCALSTORAGE);

// 列出可选的驱动，以优先级排序
localforage.setDriver([localforage.WEBSQL, localforage.INDEXEDDB]);
```

`setDriver(driverName)`<br>
`setDriver([driverName, nextDriverName])`

若可用，强制设置特定的驱动。

默认情况下，localForage 按照以下顺序选择数据仓库的后端驱动：

1. IndexedDB
2. WebSQL
3. localStorage

如果你想强制使用特定的驱动，可以使用 `setDriver()`，参数为以下的某一个或多个：

* `localforage.INDEXEDDB`
* `localforage.WEBSQL`
* `localforage.LOCALSTORAGE`

<aside class="notice">
  如果你尝试加载的后端驱动在浏览器上不可用，localForage 将使用以前使用的后端驱动中的一个。这意味着如果你试图强制 Gecko 浏览器使用 WebSQL，则会失败并继续使用 IndexedDB。
</aside>

## config

```js
// 将数据库从 “localforage” 重命名为 “Hipster PDA App”
localforage.config({
    name: 'Hipster PDA App'
});

// 将强制使用 localStorage 作为存储驱动，即使其他驱动可用。
// 可用配置代替 `setDriver()`。
localforage.config({
    driver: localforage.LOCALSTORAGE,
    name: 'I-heart-localStorage'
});

// 配置不同的驱动优先级
localforage.config({
    driver: [localforage.WEBSQL,
             localforage.INDEXEDDB,
             localforage.LOCALSTORAGE],
    name: 'WebSQL-Rox'
});
```

`config(options)`

设置 localForage 选项。在调用 localForage *前*必先调用它，但可以在 localForage 被加载后调用。使用此方法设置的任何配置值都将保留，即使在驱动更改后，所以你也可以先调用 `config()` 再次调用 `setDriver()`。以下配置值可以设置：

<dl>
  <dt>driver</dt>
  <dd>
    要使用的首选驱动。与上面的 <a href="#settings-api-setdriver"><code>setDriver</code></a> 的值格式相同。<br>
    默认值：<code>[localforage.INDEXEDDB, localforage.WEBSQL, localforage.LOCALSTORAGE]</code>
  </dd>
  <dt>name</dt>
  <dd>
    数据库的名称。可能会在在数据库的提示中会出现。一般使用你的应用程序的名字。在 localStorage 中，它作为存储在 localStorage 中的所有 key 的前缀。<br>
    默认值：<code>'localforage'</code>
  </dd>
  <dt>size</dt>
  <dd>
    数据库的大小（以字节为单位）。现在只用于WebSQL。
    默认值：<code>4980736</code>
  </dd>
  <dt>storeName</dt>
  <dd>
    数据仓库的名称。在 IndexedDB 中为 <code>dataStore</code>，在 WebSQL 中为数据库 key/value 键值表的名称。<strong>仅含字母和数字和下划线。</strong>任何非字母和数字字符都将转换为下划线。<br>
    默认值：<code>'keyvaluepairs'</code>
  </dd>
  <dt>version</dt>
  <dd>
    数据库的版本。将来可用于升级; 目前未使用。<br>
    默认值：<code>1.0</code>
  </dd>
  <dt>description</dt>
  <dd>
    数据库的描述，一般是提供给开发者的。<br>
    默认值：<code>''</code>
  </dd>
</dl>

<aside class="notice">
  与大多数 localForage API 不同，该 <code>config</code> 方法是同步的。
</aside>

# 驱动 API

从**1.1版**开始，就可以为 localForage 自定义驱动了。

## defineDriver

```js
// 此处为驱动的实现
var myCustomDriver = {
    _driver: 'customDriverUniqueName',
    _initStorage: function(options) {
        // 在此处自定义实现...
    },
    clear: function(callback) {
        // 在此处自定义实现...
    },
    getItem: function(key, callback) {
        // 在此处自定义实现...
    },
    key: function(n, callback) {
        // 在此处自定义实现...
    },
    keys: function(callback) {
        // 在此处自定义实现...
    },
    length: function(callback) {
        // 在此处自定义实现...
    },
    removeItem: function(key, callback) {
        // 在此处自定义实现...
    },
    setItem: function(key, value, callback) {
        // 在此处自定义实现...
    }
}

// 为 localForage 添加驱动。
localforage.defineDriver(myCustomDriver);
```

你需要确保接受一个 `callback` 参数，并且将同样的几个参数传递给回调函数，类似默认驱动那样。你还需要处理 resolve 或 reject promises。查看 [默认驱动][default drivers]，了解如何实现自定义的驱动。

自定义实现可包含一个 `_support` 属性，该属性为布尔值（`true` / `false`） ，或者返回一个 `Promise`,该 `Promise` 的结果为布尔值。如果省略 `_support`，则默认值是 `true` 。你用它来标识当前的浏览器支持你自定义的驱动。

<aside class="notice">
  你在任何一个 localForage 实例上添加驱动实现后，则该驱动可用于页面内的所有 localForage 实例。
</aside>

[default drivers]: https://github.com/mozilla/localForage/tree/master/src/drivers

## driver

```js
localforage.driver();
// "asyncStorage"
```

`driver()`

返回正在使用的驱动的名称，在异步的驱动初始化过程中（详情参阅 <a href="#driver-api-ready"><code>ready</code></a>）为 `null`，若初始化未能找到可用的驱动也为 `null`。

<aside class="notice">
  如果驱动在初始化过程中或之后出错，localForage 将试着使用下一个驱动。由加载 localForage 时的默认驱动顺序或传递给 `setDriver()` 的驱动顺序决定。
</aside>

## ready

```js
localforage.ready().then(function() {
    // 当 localforage 将指定驱动初始化完成时，此处代码运行
    console.log(localforage.driver()); // LocalStorage
}).catch(function (e) {
    console.log(e); // `No available storage method found.`
    // 当没有可用的驱动时，`ready()` 将会失败
});
```

`ready()` 提供了一种方法来确定异步驱动程序初始化过程是否已完成，localForage 会对所有数据 API 方法的调用进行缓冲排序。当我们需要知道 localForage 当前正在使用的是哪一个驱动时，此方法会非常有用。

## supports

```js
localforage.supports(localforage.INDEXEDDB);
// true
```

`supports(driverName)`

返回一个布尔值，表示浏览器是否支持 `driverName`。

默认驱动名称可参阅 <a href="#settings-api-setdriver"><code>setDriver</code></a>。

# 多实例


你可以创建多个 localForage 实例，且能指向不同数据仓库。所有 [config](#api-config) 中的配置选项都可用。

## createInstance

```js
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

创建并返回一个 localForage 的新实例。每个实例对象都有独立的数据库，而不会影响到其他实例。

## dropInstance

```js
localforage.dropInstance().then(function() {
  console.log('Dropped the store of the current instance').
});

localforage.dropInstance({
  name: "otherName",
  storeName: "otherStore"
}).then(function() {
  console.log('Dropped otherStore').
});

localforage.dropInstance({
  name: "otherName"
}).then(function() {
  console.log('Dropped otherName database').
});
```

调用时，若不传参，将删除当前实例的 “数据仓库” 。
调用时，若参数为一个指定了 `name` 和 `storeName` 属性的对象，会删除指定的 “数据仓库”。
调用时，若参数为一个仅指定了 `name` 属性的对象，将删除指定的 “数据库”（及其所有数据仓库）。