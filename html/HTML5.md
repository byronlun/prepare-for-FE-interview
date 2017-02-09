# HTML5

## HTML5包含什么

先来看看html5标准相对于以前的技术究竟有哪些变化：

- 语义化：增加了诸多的标签，让开发者更容易表达html的语义。比如`<section>` `<aside>` `<header>`等；引入的一些新的表单控件，这些控件具有更为精准的UI呈现。
- 通信：引入WebSockets，使得网页也具有了与服务器进行双工通信的能力
- 离线&缓存：支持一些简单的客户端存储和数据库，实现客户端存储，以前通常用cookie；离线应用功能可以使得应用可以离线运行
- 多媒体呈现：音视频呈现能力增强，引入`<audio>` `<video>`，可以嵌入字幕
- 2D/3D绘图&效果：Canvas绘图、WebGL的3D渲染支持、SVG
- 性能提升：XMLHttpRequest的增强，Web异步任务能力，历史接口(History API)，拖拽的原生支持
- 设备访问：访问相机、地理位置、设备角度等

## 语义化

随着 HTML（内容） 和 CSS（展示） 的逐渐分离，我们编写 HTML 的时候越来越注重语义化，那么为什么要引入语义化标签

1. 改进搜索引擎的优化（SEO）
2. 让代码更好维护，与CSS3的关系更和谐
3. 可以提升可访问性和互操作性

在HTML5中，新增了很多更有含义的标签（主要如下）：

- 在结构方面：
 - `<header>` 表示头部
 - `<footer>` 表示底部
 - `<main>` 表示主体（后来提出的，IE不支持）
 - `<section>` 表示一个区域
 - `<aside>` 表示侧边栏
 - `<nav>` 表示导航

- 在内容方面：
 - `<article>` 表示文章
 - `<canvas>` 表示画布
 - `<audio>` 表示音频
 - `<video>` 表示视频
 - `<time>` 表示日期和时间值

- 在表单方面（type属性增加了一些新值）：
 - type="email"
 - type="date"
 - type="color"
 - type="month"
 - type="number"
 - type="range" 范围控件
 - required，max 表单的验证
 - autofocus， autocomplete等
 

- 其他
 - `<figure>` 表示一个和文档有关的图例
 - `<figcaption>` 表示一个图例的说明
 ```html
    <figure>
        <img src="Mars.jpg" alt="">
        <figcaption>火星</figcaption>
    </figure>
 ```

同时也删除了一些标签，比如很多关于样式的标签： `<big>`， `<center>`， `<font>` 等

## 通信

**Web Sockets**：允许在页面和服务器之间建立持久连接并通过这种方法来交换非 HTML 数据。

很多平台都利用其实现用于通信的工具，比如`Node.js`平台下的`Socket.io`， `WebSocket`等

## 离线&缓存

HTML5 提供两种web存储方法，localStorage 与 sessionStorage

- 解决了存储大小的问题
- 解决了请求头臃肿的问题
- 跨浏览器
- 键值对的形式进行存储

localStorage 与 sessionStorage 区别：

- 过期时间：
  >localStorage没有过期时间，只要不clear或remove，数据会一直保存。
  sessionStorage 针对一个session进行数据存储，生命周期与session相同，当用户关闭浏览器后，数据将被删除

- 大小：
  >localStorage标准大小限制为5M

Service Workers 可以带来丰富的离线体验，本地缓存，定期同步，推送通知等。这样就可以实现在前端设置缓存，从而给用户带来更好的离线体验，典型的例子有：Chrome离线下的恐龙小游戏。

## 多媒体呈现

利用 `<video>` 和 `<audio>`来嵌入视频和音频，并支持新的多媒体内容的操作和不同浏览器对多媒体文件的兼容

## 2D/3D绘图&效果

2D绘图就是 `<canvas>` 画布了，我们可以使用 JavaScript 动态在在这个区域绘制图形动画，那是因为 `<canvas>` 给我们提供的 2D 对象，我们可以通过 `canvas.getContext('2d')` 来获取

3D方面就是使用 `WebGL` 在 `<canvas>` 元素中的 API 给 Web 带来了 3D 图像功能

SVG 则是一个基于 XML 的可以直接嵌入到 HTML 中的矢量图像格式。

## 性能提升

### XMLHttpRequest的增强

允许异步读取页面的某些部分，允许其显示动态内容，根据时间和用户行为而有所不同。这是在 Ajax背后的技术。

### 历史接口(History API)

允许对浏览器历史记录进行操作。这对于那些交互地加载新信息的页面尤其有用。

HTML5 引进了 `history.pushState()` 方法和 `history.replaceState()` 方法，允许逐条地添加和修改历史记录条目

### 拖拽的原生支持

HTML5 提供了原生的拖放 API，对于被拖动元素，会有以下三个事件发生

- dragstart 按下鼠并开始移动鼠标时触发
- drag 拖动时持续触发
- dragend 拖动结束将得到dragend事件对象，不管操作成功与否

而对于放置目标

- dragenter 拖动元素第一次进入放置目标时触发
- dragover 在放置目标内移动
- dragleave 拖出了放置目标时触发
- drop 放置在目标范围内

只要重写一个元素的 dragenter 和 dragover 事件，就可以使它变成一个有效的放置目标

## 设备访问

**使用 Camera API**： 可以使用手机的摄像头拍照,然后把拍到的照片发送给当前网页。

这些操作主要是通过一个input元素来实现的,其中该元素的type属性必须为"file",accept属性要允许图片格式,代码如下:

```html
<input type="file" id="take-picture" accept="image/*">
```

通过File API,可以获取到用户所拍摄的照片或者所选择的图片文件的引用:

```js
var takePicture = document.querySelector("#take-picture");
takePicture.onchange = function (event) {
    // 获得图片文件的引用
    var files = event.target.files,
        file;
    if (files && files.length > 0) {
        file = files[0];
    }
};
```

进而就可以对图片进行展示，或者做相应的处理。
例如展示图片，如果获取到了那张照片的引用(也就是File对象),就可以使用 `window.URL.createObjectURL()` 方法创建一个指向那个照片的URL,然后把得到的URL赋给img元素的src属性:

```js
// 获取到img元素
var showPicture = document.querySelector("#show-picture");

// 获取到window.URL对象
var URL = window.URL || window.webkitURL;

// 创建一个对象URL字符串
var imgURL = URL.createObjectURL(file);

// 设置img元素的src属性为那个URL
showPicture.src = imgURL;

// 释放那个对象URL,提高性能
URL.revokeObjectURL(imgURL);
```

如果浏览器不支持createObjectURL()方法,还可以使用FileReader来实现:

```js
// 如果createObjectURL方法不可用
var fileReader = new FileReader();
fileReader.onload = function (event) {
    showPicture.src = event.target.result;
};
fileReader.readAsDataURL(file);
```

**使用地理位置定位**： 让浏览器使用地理位置服务定位用户的位置。

HTML5 提供的地理位置 API 即是 geolocation 对象， 其位于 navigator，通过 navigator.geolocation 调用。

- 获取当前位置
最常用的方法就是 `getCurrentPosition()` 了，通过调用这个函数获取用户当前定位位置。这个方法接受两个参数：第一个是成功的处理函数，第二个则是出现错误的处理函数（可选）。

 ```js
 navigator.geolocation.getCurrentPosition(success[, error]);
 ```

- 监视定位
可以通过 `watchPosition()` 函数实现该功能。它与 `getCurrentPosition()` 接受相同的参数，但回调函数会被调用多次。

 ```js
 var watchID = navigator.geolocation.watchPosition(success[, error]);
 ```
 如果成功，则回调函数会传入一个 position 参数：包含两个属性 `coords` 和 `timestamp`。
 其中 `coords` 包含
 - latitude 纬度
 - longitude 经度
 - altitude 海拔
 - heading 度(顺时针，以正北为基准)
 - speed 米/秒

  `watchPosition()` 函数会返回一个 ID，唯一地标记该位置监视器。您可以将这个 ID 传给 `clearWatch()` 函数来停止监视用户位置。

 ```js
 navigator.geolocation.clearWatch(watchID);
 ```
 
**触控事件**： 对用户按下触控屏的事件做出反应的处理程序(用来支持移动端页面需要。例如canvas画图)。
 
## 多线程处理

HTML5 提供了 Web Workers 来实现 javascript 的多线程，给Web应用带来潜在的巨大性能的提升，每个新的 Worker 都在自己的线程中运行代码，不会影响浏览器 UI，也不会影响其他 Worker 中运行的代码。

Web Worker的基本原理就是在当前 javascript 的主线程中，使用 Worker 类加载一个 javascript 文件来开辟一个新的线程，起到互不阻塞执行的效果，并且提供主线程和新线程之间数据交换的接口：`postMessage` 和 `onmessage` 。

```js
var worker = new Worker('code.js');
worker.onmessage = function (event) {
    console.log(event.data);
};
worker.postMessage('hello');
```

实际应用，使用 Web Workers 加在大量的JSON数据：

```js
var worker = new Worker('jsonparser.js');

// 数据处理完毕，回传回来
worker.onmessage = function (event) {
    // 打印处理好的数据
    console.log(event.data);
};

// 传入要解析的大量 JSON 数据
worker.postMessage(jsontext);
```

jsonparser.js：

```js
self.onmessage = function (event) {
    // 打印处理好的数据
    var jsonText = event.data;
    // 解析
    var jsonData = JSON.parse(jsonText);
    // 回传结果
    self.postMessage(jsonData);
};
```

web worker 可以做什么：

- 可以加载一个JS进行大量的复杂计算而不挂起主进程，并通过postMessage，onmessage进行通信
- 可以编码/解码大字符串数据，进行大数组排序等。
- 可以在worker中通过importScripts(url)加载另外的脚本文件
- 可以使用 setTimeout(), clearTimeout(), setInterval(), and clearInterval()
- 可以使用XMLHttpRequest来发送请求
- 可以访问navigator的部分属性

有那些局限性：

- 不能跨域加载JS
- worker内代码不能访问DOM
- 各个浏览器对Worker的实现不大一致，例如FF里允许worker中创建新的worker,而Chrome中就不行
- 不是每个浏览器都支持这个新特性
