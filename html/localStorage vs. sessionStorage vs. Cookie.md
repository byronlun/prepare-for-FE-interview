# localStorage vs. sessionStorage vs. Cookie

标签： html

---

在对比三者之间的关系之前，先了解一下 Cookie，Web Storage.

## Cookie

Cookie（也叫 Web cookie 或者浏览器 Cookie）是服务器发送到用户浏览器并保存在浏览器上的一块数据，它会在浏览器下一次发起请求时被携带并发送到服务器上。

Cookie可以分为：会话期 Cookie，持久 Cookie，安全和HttpOnly类型Cookie。

更多关于cookie的了解可以参考 [Cookie MDN]（https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies） 上面的讲解。

## Web Storage

Web Storage 就包含 sessionStorage 、localStorage 两种。

sessionStorage 为每一个给定的源维持一个独立的存储区域，该存储区域在页面会话期间可用（即只要浏览器处于打开状态，包括页面重新加载和恢复）。
localStorage 具有同样的功能，但是在浏览器关闭，然后重新打开后数据仍然存在。

>注意：两者的数据仅在客户端（即浏览器）中保存，不参与和服务器的通信。

sessionStorage 记录用户在当前 session 中访问页面的次数的例子：

```js
if (sessionStorage.pagecount){
  sessionStorage.pagecount = Number(sessionStorage.pagecount) + 1;
} else {
  sessionStorage.pagecount = 1;
}
console.log(sessionStorage.pagecount)
```

控制台输入以上代码（多次出入该会话窗口可以打印不同访问页面次数）

### Web Storage 的好处

- 临时存储数据： 对于某些常用数据，或者静态资源数据，可以临时存储。对于当前窗口(tab)的临时数据可以利用 sessionStorage 来保存，如果是长期保存，则使用 localStorage
- 减少网络流量： 数据保存在本地，避免重新向服务器请求数据，从而避免了不必要的数据请求，因此减少网络流量
- 快速显示数据： 由于数据临时保存在本地，从而不用发送请求去服务器端获取，这样可以快速的读取数据，提供了性能

## sessionStorage 、localStorage 和 cookie 之间的区别

同：都是保存在浏览器端，并且是在同源情况下。

异：

- **cookie 数据始终在同源的 http 请求中携带（即使不需要），即 cookie 在浏览器和服务器间来回传递。而 sessionStorage 和 localStorage 仅在客户端（浏览器）中保存，不会自动把数据发给服务器，仅在本地保存**。cookie 数据还有路径（path）的概念，可以限制 cookie 只属于某个路径下。
- **存储大小的限制不同**。cookie 数据不能超过 **4k**，sessionStorage 和 localStorage 可以达到 **5M** 左右。**因为每次 http 请求都会携带 cookie，所以 cookie 只适合保存很小的数据**，如会话标识。
- **数据有效期不同**。sessionStorage 仅在当前浏览器窗口（tab）关闭前有效，自然也就不可能持久保持；localStorage 始终有效，窗口或浏览器关闭也一直保存，因此用作持久数据；cookie 只有在设置的 cookie 过期时间之前一直有效，即使窗口关闭。
- **作用域不同**。sessionStorage 不在不同的浏览器窗口中共享，即使是同一个页面；localStorage 在所有同源窗口中都是共享的；cookie 也是在所有同源窗口中都是共享的。
- **位置不同**。sessionStorage 和 localStorage 是 **window** 全局对象下的属性，cookie 是 （window.）**document** 对象的属性。

## 参考链接

[Cookie MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)
[Web Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)
[浏览器本地数据（sessionStorage、localStorage、cookie）与server端数据  ](http://han.guokai.blog.163.com/blog/static/13671827120112694851799/)
