# Web Workers

## 概述

HTML5提出的Web Workers使代码在浏览器的UI线程之外执行，不占用浏览器UI线程的时间，给Web应用带来潜在的巨大性能提升，因为每个新的Workers都在自己的线程中运行代码。这意味着Worker运行代码不仅不会影响浏览器UI，也不会影响其他Worker中运行的代码。

## Worker运行环境

因为Web Worker没有绑定UI线程，这也意味着他们没有办法访问浏览器的许多资源。Ｗeb Workers 从外部线程中修改DOM或者非线程安全的组件都会导致出现错误，但是每个Web Worker都有自己的全局运行环境，其功能只是JavaScript特性的一个自己。

- 一个navigator对象: 包括四个属性：appName，appVersion，appVersion，userAgent
- 一个location对象(与window.location一样，不过所有的属性都是只读的)
- 一个self对象，指向全局的worker对象
- 一个importScripts()方法，用来加在Worker所用道德外部JavaScript文件
- 所有的ECMAScript对象，例如： Object， Array等
- XMLHttpRequest构造器
- setTimeOut和setInterval方法
- 一个close()方法，用来立刻停止Worker运行

要创建Web Worker线程，需要单独创建一个完全独立的JS文件，通过传入这个JS文件的URL来创建：`var worker = new Worker('code.js')`。
当需要加载外部JavaScript文件时，通过importScripts()方法，可以接收一个或者多个文件，URL作为参数，其调用方式是阻塞式的，直到所有文件加载完才继续执行脚本（但这并不会影响UI线程）， 例如：
```
importScripts('a.js', 'b.js');
self.onmessage = function(event) {
    self.postMessage('Hi,' + event.data);
};
```

## 应用Worker通信

Web Workers最佳应用就是用于处理纯数据或者与浏览器UI无关的长时间运行脚本。比如说，要解析一个很大的JSON字符串，数据量太大会影响客户端运行JavaScript的事件，干扰用户体验，使用Worker就成为理想的解决方法。

```
var myWorker = new Worker('task.js')

//传入大量带解析的JSON字符串
worker.postMessage(jsonText);

//数据到位之后，调用事件处理器
myWorker.onmessage = function(event) {
	//回传回来的JSON结构
	var jsonData = event.data;
	
	//使用JSON结构
	UseData(jsonData);
}
//可以通过terminate()关闭线程
//myWorker.terminate();
```
下面看task.js
```
//当JSON传过来时，就会触发这个事件处理器
self.onmessage = function(event) {
	//JSON 字符串由event.data传入
	var jsonText = event.data;
	
	//解析
	var jsonData = JSON.parse(jsonText);
	
	//回传结果
	self.postMessage(jsonData);
}
//这里也可以调用自己的close()方法来关闭自己
//self.close();
```
通过这样，`JSON.parse`解析数据的时候就不会影响其他代码的执行，直到解析完代码都不会干扰用户体验。

### Web Worker适合的场景：

- 编码/解码大字符串
- 复杂数学运算，包括图像或视频处理
- 大数组排序

### Web Worker的缺点

- 无法修改DOM
- 无法跨域
- 各浏览器实现Web Worker不一致，可能出现差别问题





