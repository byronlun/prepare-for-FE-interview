# position

标签： css

---

css API中 position 属性的相关定义：

- static：无特殊定位，对象遵循正常文档流。top，right，bottom，left等属性不会被应用
- relative：对象遵循正常文档流，但将依据top，right，bottom，left等属性在正常文档流中偏移位置。而其层叠通过z-index属性定义。
- absolute：对象脱离正常文档流，使用top，right，bottom，left等属性进行绝对定位。而其层叠通过z-index属性定义。
- fixed：对象脱离正常文档流，使用top，right，bottom，left等属性以窗口为参考点进行定位，当出现滚动条时，对象不会随着滚动。而其层叠通过z-index属性定义。

### 当position属性值为relative时 

对象原来占有的位置保留，其后面的对象按原来文档流仍然保持原来的位置。

- top的值表示**对象相对原位置**向下偏移的距离 
bottom的值表示**对象相对原位置**向上偏移的距离 
两者同时存在时，只有top起作用。
- left的值与right的值的效果和top与bottom类似
两者同时存在时，只有left起作用。

### 当position属性值为absolute时 

对象从文档流中抽取出来，原占有的位置被后面的对象顶替上来

- top的值表示对象上边框与**浏览器窗口**顶部的距离 
bottom的值表示对象下边框与**浏览器窗口**底部的距离 
两者同时存在时，只有top起作用；如果两者都未指定，则其顶端将与原文档流位置一致，即垂直保持位置不变。 
- left的值与right的值和top与bottom类似。
两者同时存在时，只有left起作用；如果两者都未指定，则其左边将与原文档流位置一致，即水平保持位置不变。 
- 在position属性值为absolute的同时，**如果有一级父对象**（无论是父对象还是祖父对象，或者再高的辈分，一样）的position属性值为relative时，则上述的相对浏览器窗口定位将会变成相对父对象定位，这对精确定位是很有帮助的。

### 当position属性值为fixed时

对象从文档流中抽取出来，原占有的位置被后面的对象顶替上来

- top,bottom,left,right都是相对于**浏览器窗口**,无论父元素有没有设置position属性值为relative，都与父元素无关。

值得注意的是，MDN文档上对于`postion: fixed`这个属性有如下描述：

> 当元素祖先的 `transform`  属性非 `none` 时，容器由视口改为该祖先。

所以当`position: fixed`的元素有 `transform`  属性非 `none` 的父元素时，将会相对于该父元素进行定位。

----------

[**one nice blog**](http://blog.csdn.net/chen_zw/article/details/8741365)（demo里面含图，易理解）

## 相对定位

### (1) 初始状态
```html
/******初始*********/  
<style type="text/css">  
    #first { 
      width: 200px;
      height: 100px;
      border: 1px solid red;
    }  
    #second{
      width: 200px;
      height: 100px;
      border: 1px solid blue;
    }  
</style>  
<body>  
   <div id="first"> first</div>  
   <div id="second">second</div>  
</body> 
```
初始原图：
![](http://ww2.sinaimg.cn/large/005JoIL8gw1f40fg4t8qqj30cp08qweg.jpg)

### (2) 我们修改first元素的position属性：
```html
<style type="text/css">  
    #first { 
      width: 200px;
      height: 100px;
      border: 1px solid red;
      position: relative;
      top: 20px;
      left: 20px; 
    }  
    #second{
      width: 200px;
      height: 100px;
      border: 1px solid blue;
    }  
</style> 
```
相对偏移20px后：
![](http://ww4.sinaimg.cn/large/005JoIL8gw1f40fmkpz32j30cx084mxe.jpg)
这里可以看出：相对定位相对的是它原本在文档流中的位置而进行的偏移，而relative定位也是遵循正常的文档流，它没有脱离文档流，但是它的top/left/right/bottom属性是生效的，最重要的是它还占有文档空间，而且占据的文档空间不会随 top / right / left / bottom 等属性的偏移而发生变动，也就是说它后面的元素是依据虚线位置( top / left / right / bottom 等属性生效之前)进行的定位，这点一定要理解。

### (3) 添加margin属性
```html
<style type="text/css">  
    #first { 
      width: 200px;
      height: 100px;
      border: 1px solid red;
      position: relative;
      top: 20px;
      left: 20px;
      margin: 20px;
    }  
    #second{
      width: 200px;
      height: 100px;
      border: 1px solid blue;
    }  
</style>
```
设置margin：20px后：
![](http://ww1.sinaimg.cn/large/005JoIL8gw1f40g02z30oj30cr07m0u3.jpg)
  对比一下，就很清晰了，先将first元素外边距设为20px，那么second元素就得向下偏移40px，所以margin是占据文档空间！同理，可以自己动手测下padding的效果！

## 绝对定位

使用absoult定位的元素脱离文档流后，就只能根据祖先类元素(父类以上)进行定位，而这个祖先类还必须是以postion非static方式定位的， 举个例子，a元素使用absoulte定位，它会从父类开始找起，寻找以position非static方式定位的祖先类元素(注意，一定要是直系祖先才算哦~），直到`<html>`标签为止，这里还需要注意的是***，relative和static方式在最外层时是以`<body>`标签为定位原点的，而absoulte方式在无父级是position非static定位时是以<html>作为原点定位***。

### (4) 添加absoulte属性：

```html
<html>  
<style type="text/css">  
    html{
      border:1px dashed green;
    }  
    body{
      border:1px dashed  purple;
    }  
    #first{
      width: 200px;
      height: 100px;
      border: 1px solid red;
      position: relative;
    }  
    #second{
      width: 200px;
      height: 100px;
      border: 1px solid blue;
      position: absolute;
      top: 0;
      left: 0;
    }  
</style>  
<body>  
  <div id="first">relative</div>  
  <div id="second">absoult</div>  
</body>  
</html>  
```

效果图：

![](http://ww3.sinaimg.cn/large/005JoIL8gw1f40gkfmy13j30an055t9i.jpg)

代码里面为什么absoulte定位要加 top:0; left:0; 属性，这不是多此一举呢？
其实加上这两个属性是完全必要的，**因为我们如果使用absoulte或fixed定位的话，必须指定 left、right、 top、 bottom 属性中的至少一个，否则left/right/top/bottom属性会使用它们的默认值 auto ，这将导致对象遵从正常的HTML布局规则，在前一个对象之后立即被呈递，简单讲就是都变成relative，会占用文档空间**，这点非常重要，很多人使用absolute定位后发现没有脱离文档流就是这个原因，这里要特别注意~~~

### (5) 在absoulte定位中添加margin / padding属性：

```html
<style type="text/css">  
    html{
      border:1px dashed green;
    }  
    body{
      border:1px dashed  purple;
    }  
    #first{
      width: 200px;
      height: 100px;
      border: 1px solid red;
      position: relative;
      margin:40px;
      padding:40px;
    }  
    #second{
      width: 200px;
      height: 100px;
      border: 1px solid blue;
      position: absolute;
      top:20px;
      left:20px;
    }  
</style>  
<body>  
  <div id="first">first  
    <div id="second">second</div>  
</div> 
```

效果图![](http://ww1.sinaimg.cn/large/005JoIL8gw1f40h9j4ikxj30db08j3yk.jpg)![](http://ww3.sinaimg.cn/large/005JoIL8gw1f40ha5jsl6j30dx08q75x.jpg)

可以看出：祖先类的margin会让子类的absoulte跟着偏移，而padding却不会让子类的absoulte发生偏移。总结一下，就是absoulte是根据祖先类的border进行的定位。

***Note : 绝对(absolute)定位对象在可视区域之外会导致滚动条出现。而放置相对(relative)定位对象在可视区域之外，滚动条不会出现。***

##z-index属性

z-index，又称为对象的层叠顺序，它用一个整数来定义堆叠的层次，整数值越大，则被层叠在越上面，当然这是指同级元素间的堆叠，如果两个对象的此属性具有同样的值，那么将依据它们在HTML文档中流的顺序层叠，写在后面的将会覆盖前面的。需要注意的是，父子关系是无法用 z-index 来设定上下关系的，一定是子级在上父级在下。
    
***Note：使用 static 定位或无 position 定位的元素 z-index 属性是无效的。***





