# 跨域

标签（空格分隔）： js

---

## 跨域问题的解决方案

### 跨域是啥

**跨域(Cross-Origin)**是指在浏览器的[同源策略](https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)下,当前客户端无法访问与自身**不同协议( protocol),不同域名(domain),不同端口号(port)**的资源,三者出现其中一个不同,即跨域.

| url1           | url2                 | 是否跨域   |
|:--------------:|:--------------------:|:----------:|
| http://a.com   | https://a.com        | 是         |
| http://a.com   | http://b.com         | 是         |
| http://a.com   | http://b.com:3000    | 是         |

### **解决方案--JSONP**

JSONP(JSON with Padding)，就是异步请求跨域的服务器端时，不是直接返回数据，而是返回一个js方法，把数据作为参数传过来。**如果只是跨域传递数据那么这种方式是比较好的**。
它是利用`script`标签没有跨域限制的特性,通过在`src`的`url`的参数上附加回调函数的名字(页面事先声明回调函数),然后服务器接受回调函数名字并且返回一个包含数据的回调函数,供客户端调用。

看栗子: 
```html
<script type="text/javascript">
    function handler(json){
        console.log(json.name + ' ' + json.purpose); //byronlun test for jsonp
    }
</script>
<script type="text/javascript" src="http://localhost:3030/?callback=handler"></script>
```

```
//服务器端
var data  = {'name': 'byronlun', 'purpose': 'test for jsonp'};
//核心代码
var str = callback + '(' + JSON.stringify(data) + ')'; //callback是服务器从客户端的请求参数里面货渠道的callback的值(即handler函数)
res.end(str); //服务器端返回handler(data)
```
个人觉得jsonp在跨域获取数据是最简单粗暴的方式,所以可以说最为常用.但这样又是jsonp的问题所在: `jsonp`是从其他域中加载代码执行,如果其他域不安全,容易被攻击或者篡改,带来安全问题.还有一点就是: JSONP只支持GET请求.

再看一个JSONP的实例: **百度搜索联想词**,参考这篇[博客](http://www.cnblogs.com/twobin/p/3395086.html)里面的实例.

然后同样检查了一下chrome的搜索的联想词,不是使用JSONP,是ajax去请求,这样可能比较安全吧(个人认为,因为至少不会暴露出处理数据的方法在url中).再然后,渣渣的我想看看国内的出名的360搜索又是怎样实现的,结果也是使用jsonp实现的,360搜索联想词链接`https://sug.so.360.cn/suggest?callback=?&encodein=utf-8&encodeout=utf-8&format=json&fields=word,obdata&word=?`,我们可以通过传入`callback`的值和`word`的值,来获取并处理我们所有搜索的数据,例如:`https://sug.so.360.cn/suggest?callback=search360&encodein=utf-8&encodeout=utf-8&format=json&fields=word,obdata&word=json`,这是会返回:
```
search360({"query":"json","result":[{"word":"json在线解析"},{"word":"json在线格式化"},{"word":"json格式"},{"word":"json教程"},{"word":"json解析"},{"word":"json数组"},{"word":"jsonobject"},{"word":"jsonarray"},{"word":"jsonp"},{"word":"json格式化"}],"version":"3.2.2","rec":""});
```
然后客户端,通过调用`search360`就可以处理这些数据了.

### **解决方案--CORS**

CORS(Cross-Origin Resource Sharing):跨与资源共享,定义了在必须访问跨源资源时,浏览器与服务器如何交流.CORS背后的思想,其实就是使用自定义的HTTP头部,让浏览器与服务器进行沟通,从而决定请求和响应是应该成功,还是应该失败.

关键点就是: 客户端可以给服务器端发送一个`Origin`头部,例如:
 `Origin: http://www.example.com`
然后服务器端可以根据客户端发过去的这个请求,决定是否接受,如果接受,则需要在response里面,包含一个与客户端发送过来的`Origin`头部值一样的`Access-Control-Allow-Origin`头部(如果是公共资源,可以设置为"*"),例如:
`Access-Control-Allow-Origin: http://www.example.com` .

所以CORS的关键点在于: 服务器端有没有给客户端返回`Access-Control-Allow-Origin`头部信息,并且值是否和客户端发送的`Origin`一致,或者直接把值设置成为`*`.(刚开始高程没有仔细看,一致设置`Origin`,没有设置`Access-Control-Allow-Origin`结果一致无法实现跨越,后来搞懂了回去看高程,才知道书没有仔细看,渣渣).

下面看栗子:
```js
//浏览器端代码
//客户端ip地址http://127.0.0.1:54615请求服务器端http://127.0.0.1:3030
function after() {
	var xhr = new XMLHttpRequest();
	xhr.onreadystatechange = function() {
		if(xhr.readystate === 4) {
			if(xhr >= 200 && xhr.status <=304) {
				console.log(xhr.responeText);
				console.log('success');
			} else {
				console.log(xhr.status);
			}
		}
	}
	xhr.open('get', 'http://127.0.0.1:3030', true);
	//默认发送Origin就是本客户端的ip地址,不用设置,因为这是不允许的
	//xhr.setRequestHeader('Origin', 'http://127.0.0.1:54615')
	xhr.send(null);
}
//加载完页面两秒后请求http://127.0.0.1:3030
setTimeout(after, 2000);
```

服务器端代码:
```js
res.writeHead(200, {
	'Content-Type': 'text/html',
	'Access-Control-Allow-Origin': 'http://127.0.0.1:54615'//核心代码
});
```

于是乎,就可以实现跨域了.同样CORS最遗憾的一点就是IE8~10是不支持的,需要通过`XDomainRequest`来实现(`XDomainRequest`和`XHR`类似,但是实现的安全可靠地跨域通信).但是IE10之后就有了完成的实现了.


## 其他跨域方式

### **window.name** 

`window.name` 是一个当浏览器页面打开就存在的变量，值为 `''`。它的生命周期是一直维持到页面关闭，并且是共享的，所以我们可以用来传输一些数据。

### **document.domain**

在不同的子域和 `iframe` 交互的时候，获取到另外一个 `iframe` 的 `window` 对象是没有问题的，但是获取到的这个 `window` 的方法和属性大多数都是不能使用的。

这时就可以设置 `document.domain` 来解决

但是为什么是子域呢，那是因为 `document.domain` 的设置也是有限制的，它只能设置为页面本身或者更高一级的域名

## **location.hash**

这种方法靠的是把数据的变化放在 `url` 的 `hash` 里。

## **window.postMessage**

window.postMessage() 是 HTML5 新的 API，它允许和所有的域进行交流




