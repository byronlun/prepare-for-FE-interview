# webpack性能优化之首页空白问题的处理

标签： webpack 

---

今天面试被问到webpack，其中问道：很多情况下，webpack会将所有的 JS 代码打包成一个 JS 文件，但是如果这个 JS 文件过大，就会出现首屏加载时间过长的问题，出现白屏，怎么解决？

首先，将 JS 代码打包成一个 JS 文件是有原因的，因为这样子可**以减少了请求数**，但是也同样带来了体积太大的问题。

我想到的解决方案：

- 利用webpack的压缩插件进行代码压缩
- 按需加载，按照模块打包成多个 JS 文件


## 压缩代码

借助 webpack 自带的压缩插件 `UglifyJsPlugin` 来实现代码的压缩：

```js
plugins: [
    // ...其他配置
    // 压缩代码
    new webpack.optimize.UglifyJsPlugin({
      compress: {
        warnings: false
      }
    })
]
```

如果某些变量不想进行压缩的话，可以借助 `mangel` 这个属性，例如：

```js
new webpack.optimize.UglifyJsPlugin({
    compress: {
      warnings: false
    },
    mangle: {
      except: ['$', 'exports', 'require']
    }
})
```

更多配置参考[官网](https://webpack.github.io/docs/using-plugins.html#uglifyjsplugin)

一般情况下，开发阶段的配置可以不压缩代码，发布上线时就需要压缩代码。

## 代码分割，按需加载

Webpack 有个特性可以把代码分离成 Chunk，在需要的时候才加载他们，就实现了按需加载。

有两种方式实现代码分割的方式：一种是通过在应用程序代码中定义分离点（split point）；另一种就是在 webpack 配置中引入 `CommonsChunkPlugin` 插件。

### split point

比如 CommonJs 规范的 split point 的语法是 `require.ensure(dependencies, callback)`， 第一个参数是依赖列表，第二个参数是回调函数。

webpack 可以根据 split points 来将多个文件自动打包成一个代码块（chunk），并在运行时（比如浏览器上）自动加载依赖的代码块。

```js
require.ensure(["module-a", "module-b"], function(require) {
    var a = require("module-a");
    var b = require("module-b");
    var c = require('module-c');
});
```
注意： `require.ensure` 只加载，不执行。
上面代码会把 module-a，module-b，module-c 打包到一个代码块，虽然 module-c 没有在依赖列表里，但是在回调里调用了，一样会被打包进来。

### CommonsChunkPlugin

另一种方式是在 webpack 的 config 文件中配置，CommonsChunkPlugin 的参数是一个 option 对象，可以在里面设置 `name(s)` 表示 `chunk(s)` 名， `filename` 为生成的文件名，以及其他的一些[参数](https://webpack.github.io/docs/using-plugins.html#commonschunkplugin)

举个例子，假设我们项目中，用到了 `react`, `react-dom` 这样的第三方库，那么我们可以将这些第三方库单独打包：

```js
//在entry中添加入口
entry: {
    index: './index',
    vendor: ['react', 'react-dom', 'react-redux', 'redux', 'react-router']
},

//在plugins中配置
plugins: [
    new webpack.optimize.CommonsChunkPlugin({
      name: 'vendor',
      filename: 'vendor.bundle.js'
    }),
]
```

这样就将vendor配置的模块（react, react-dom, react-redux, redux, react-router）等打包到 vendor.bundle.js 中，将 index 中存在的第三方模块从文件中移除，让 index 中只留下纯净的业务代码。

这样就可以简单实现代码的分割， `CommonsChunkPlugin` 还可以实现自动抽取出代码块中相同的部分，更多功能参考[官网](https://webpack.github.io/docs/using-plugins.html#commonschunkplugin)

## 其他解决方案

- 图片压缩： 配置 url-loader, 把尺寸小的图片转换成base64，减少网络请求。


## 参考链接

- [在Webpack中使用Code Splitting实现按需加载](http://www.alloyteam.com/2016/02/code-split-by-routes/)
- [使用webpack分割代码的思路](https://blog.oyyd.net/post/how_to_split_your_code_in_webpack)