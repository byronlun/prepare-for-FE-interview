# YY面试总结

标签： 面试

---

第一次面试，而且是在过年半个月没学习的前提下，所以纯粹是当做试水的面试了，面试过程发现这个部门主要是偏交互的部门，面试完技术面，面试官也说到是偏交互，问面试官说评价或者建议的时候，面试官提到一点：**我们招人更多的是看本部门需要哪一类技术（比如偏交互，偏js）去面试**。所以我觉得，如果在面试像YY这样没有细分重构和JS逻辑的公司的话，可以实现了解一下面试部门的业务和技术栈。

下面对这次面试过程的问题总结：

1. HTML5的了解，有哪些新标签，兼容性如何，如何处理兼容性问题
    这个可以看我整理的 [HTML5知识点的笔记](https://github.com/byronlun/prepare-for-FE-interview/blob/master/html/HTML5.md)。
 
    标签兼容性的话，HTML5 新标签在 **IE6/IE7/IE8** 上并不能识别， 但是 IE8/IE7/IE6 支持通过 document.createElement 方法产生的标签，可以利用这一特性让这些浏览器支持 HTML5 新标签，浏览器支持新标签后，还需要添加标签默认的样式。

    ```html
    <script> 
        (function() {
            var e = "abbr, article, aside, audio, canvas, datalist, details, dialog, eventsource, figure, footer, header, hgroup, mark, menu, meter, nav, output, progress, section, time, video".split(', ');
            var i= e.length;
            while (i--){
                document.createElement(e[i])
            } 
        })() 
    </script>
    <style>  
        article, aside, canvas, details, figcaption, figure, footer, header, hgroup, menu, nav, section, summary {  
            display: block;  
        }  
    </style>
    ```
    
    当然最好的方式是直接使用成熟的框架、使用最多的是 html5shiv 框架：
 
    ```html
    <!--[if lt IE 9]> 
    <script> src="http://html5shiv.googlecode.com/svn/trunk/html5.js"</script> 
    <![endif]--> 
    ```
    
    `<!--if lt IE9>` 是判断是否小于 IE9 以下浏览器，如果是就执行这段 JS 代码，如果不是，就忽略掉。

2. CSS 的掌握程度，有没有做过偏 css 的项目或者比较好的 demo（到这里我就开始慌了）
    这个肯定就是根据个人项目经验了，主要是面试官想考察 `css` 的知识到什么水平吧。于是我还是机智的说会常见的布局（毕竟集中实现三栏布局的方式还是会的嘛），然后 flex 布局，以及 css3 的动画。

3. （因为简历有提到canvas，所以问到）有没有用canvas实现过动画（GG），没有的话思考一下怎么实现？

4. webpack的优点，针对性问道webpack的路径配置问题，比如产品发布的时候，路径是如何配置的（开发的时候，路径一般都是相对路径）

5. 跨域问题，主要问jsonp的实现，还有其他什么方法可以实现跨域

6. 性能优化问题

7. 浏览器地址栏输入之后到页面渲染出来，整个过程经历了那些过程

8. http状态码

9. 缓存设置，前端如何设置缓存




