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

## Cookie 和 Session 的区别

同： 都是用来跟踪浏览器用户身份的回话方式

异： cookie 数据保存在客户端， session 数据保存在服务器端

两者之间的联系可以描述如下：

当你登录一个网站的时候

如果 web 服务器端使用的是 session，那么所有的数据都保存在服务器上，客户端每次请求服务器的时候会发送当前会话的 sessionid，服务器根据当前 sessionid 判断相应的用户数据标志，以确定用户是否登录或具有某种权限。由于数据是存储在服务器上面，所以你不能伪造，但是如果你能够获取某个登录用户的 sessionid，用特殊的浏览器伪造该用户的请求也是能够成功的。sessionid 是服务器和客户端链接时候随机分配的，一般来说是不会有重复，但如果有大量的并发请求，也不是没有重复的可能性。
 
如果浏览器使用的是 cookie，那么所有的数据都保存在浏览器端，比如你登录以后，服务器设置了 cookie 用户名，那么当你再次请求服务器的时候，浏览器会将用户名一块发送给服务器，这些变量有一定的特殊标记。服务器会解释为 cookie 变量，所以只要不关闭浏览器，那么 cookie 变量一直是有效的，所以能够保证长时间不掉线。如果你能够截获某个用户的 cookie 变量，然后伪造一个数据包发送过去，那么服务器还是认为你是合法的。所以，使用 cookie 被攻击的可能性比较大。如果设置了的有效时间，那么它会将 cookie 保存在客户端的硬盘上，下次再访问该网站的时候，浏览器先检查有没有 cookie，如果有的话，就读取该 cookie，然后发送给服务器。如果你在机器上面保存了某个论坛 cookie，有效期是一年，如果有人入侵你的机器，将你的  cookie 拷走，然后放在他的浏览器的目录下面，那么他登录该网站的时候就是用你的的身份登录的。所以 cookie 是可以伪造的。当然，伪造的时候需要主意，直接 copy cookie 文件到 cookie 目录，浏览器是不认的，他有一个 index.dat 文件，存储了 cookie 文件的建立时间，以及是否有修改，所以你必须先要有该网站的 cookie 文件，并且要从保证时间上骗过浏览器。




## 参考链接

- [Cookie MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)
- [Web Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)
- [浏览器本地数据（sessionStorage、localStorage、cookie）与server端数据  ](http://han.guokai.blog.163.com/blog/static/13671827120112694851799/)
- [Cookies 和 Session 的区别](http://blog.csdn.net/axin66ok/article/details/6175522)
