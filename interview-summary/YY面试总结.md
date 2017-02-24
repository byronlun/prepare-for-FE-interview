# YY面试总结

标签： 面试

---

第一次面试，而且是在过年半个月没学习的前提下，所以纯粹是当做试水的面试了，面试过程发现这个部门主要是偏交互的部门，面试完技术面，面试官也说到是偏交互，问面试官说评价或者建议的时候，面试官提到一点：**我们招人更多的是看本部门需要哪一类技术（比如偏交互，偏js）去面试**。所以我觉得，如果在面试像YY这样没有细分重构和JS逻辑的公司的话，可以实现了解一下面试部门的业务和技术栈。

下面对这次面试过程的问题总结：

1. HTML5的了解，有哪些新标签，兼容性如何，如何处理兼容性问题

 这个可以看我整理的 [HTML5知识点的笔记](https://github.com/byronlun/prepare-for-FE-interview/blob/master/html/HTML5.md)。
 
 标签兼容性的话，HTML5 新标签在**IE6/IE7/IE8**上并不能识别，需要通过 JavaScript 处理。
 
 ```js
 <script> 
    (function() {
        var e = "abbr, article, aside, audio, canvas, datalist, details, dialog, eventsource, figure, footer, header, hgroup, mark, menu, meter, nav, output, progress, section, time, video".split(', ');
        var i= e.length;
        while (i--){
            document.createElement(e[i])
        } 
    })() 
 </script>
 ```

2. CSS的掌握程度，有没有做过偏css的项目或者比较好的demo（到这里我就开始慌了）

3. （果不其然）CSS3的了解，以及做过什么相关的开发

4. （因为简历有提到canvas，所以问到）有没有用canvas实现过动画（GG），没有的话思考一下怎么实现？

5. webpack的优点，针对性问道webpack的路径配置问题，比如产品发布的时候，路径是如何配置的（开发的时候，路径一般都是相对路径）

6. 跨域问题，主要问jsonp的实现，还有其他什么方法可以实现跨域

7. 性能优化问题

8. 浏览器地址栏输入之后到页面渲染出来，整个过程经历了那些过程

9. http状态码

10. 缓存设置，前端如何设置缓存

11. jQ使用程度



