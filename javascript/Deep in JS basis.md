# Deep in JS basis

标签： js

---

## 执行上下文（Execution Context）

在执行JavaScript代码的时候，代码执行上下文是非常重要的，可以将**可执行代码**分为下面三种类型。

- **全局代码**——当代码第一次执行的所进入的默认环境
- **函数代码**——在函数体执行的时候
- **Eval代码**——在eval函数中执行的时候

```js
// 全局上下文

var hello = 'hello';

function person() {
	// 执行（函数）上下文
	var firstname = 'fang';
	var lastname = 'byronlun';

	function getFirstname() {
		// 执行（函数）上下文
		return firstname;
	}
	
	function getLastname () {
		// 执行（函数）上下文
		return lastname;
	}

	alert(hello + ' ' + getFirstname() + ' ' + getLastname())
}
```

下面用栈的形式来表示执行上下文，当进入一个函数（哪怕是递归调用），都会发生进栈操作。对于eval函数也是如此。

![](http://ww3.sinaimg.cn/large/005JoIL8gw1fbmk3utpprj30kx09v0t6.jpg)

### 全局代码和函数代码
下面举个简单例子演示全局代码和函数代码执行上下文的出入栈：

```js
// 全局上下文
var a = 1;

(function foo(x) {
	// 函数上下文
	console.log(x)
	if(x < 2) {
		foo(++x)
	} else {
		return;
	}
	
}) (a);
```

在初始化的时候（程序开始），执行上下文栈数组ECStack的内容（伪代码）如下：

```js
ECStack = [
    gloableContext: {
        a: 1
    }
];
```

进入这段代码的IIFE中，初始化函数上下文的时候，ECStack情况如下：

```js
ECStack = [
    EC1: {
        x: 1
    }，
    gloableContext: {
        a: 1
    }
];
```

递归调用自身之后，，ECStack情况如下：

```js
ECStack = [
    EC2: {
        x: 2
    }，
    EC1: {
        x: 1
    }，
    gloableContext: {
        a: 1
    }
];
```

最后按照出栈的顺序，依次出栈，ECStack只剩下全局上下文：

```js
ECStack = [
    gloableContext: {
        a: 1
    }
];
```

### eval代码

eval代码要提到一个叫做调用上下文的概念，比如：调用eval函数时候的上下文，就是一个调用上下文，eval函数中执行的动作（例如：变量声明或者函数声明）会影响整个调用上下文：

```js
eval('var x = 10');
(function foo(){
    eval('var y = 20');
})();
alert(x); // 10
alert(y); // ”y” is not defined
```

ECStack会被修改为：

```js
ECStack = [
    globalContext
];
//eval('var x = 10');
ECStack.push(
    evalContext,
    callingContext: globalContext
);

// eval exited context
ECStack.pop();

//foo function call
ECStack.push( functionContext);

//eval('var y = 20');
ECStack.push(
    evalContext,
    callingContext:  functionContext
);

//return from eval
ECStack.pop();

//return from foo
ECStack.pop();
```

## 变量对象（Variable Object）

 变量对象与执行上下文是密切相关的，可将变量对象进行抽象的划分：主要是全局上下文的变量对象和函数上下文的变量对象。
 
### 全局上下文中的变量对象

首先要了解一下全局对象：

>全局对象是一个在进入任何执行上下文前就创建出来的对象；此对象以单例形式存在；它的属性在程序任何地方都可以直接访问，其生命周期随着程序的结束而终止。

全局对象在创建的时候，诸如Math,String,Date,parseInt等等属性也会被初始化,全局对象可以表示为：

```js
global = {
  Math: <...>,
  String: <...>
  ...
  ...
  window: global
};
```

全局上下文的变量对象就是全局对象：

```js
VO(globalContext) === global;
```

准确地理解这个事实是非常必要的：正是由于这个原因，当在全局上下文中声明一个变量时，可以通过全局对象上的属性来间地引用该变量。

### 函数上下文中的变量对象

函数上下文的变量对象是不能直接访问的，也叫做活跃对象（activation object）（简称： AO）

```js
VO(functionContext) === AO;
```

**活跃对象（AO）在进入函数上下文的时候创建出来**，包括函数的形参（Argument对象），函数声明，变量声明。

### 执行上下文阶段

结合变量对象再来解读执行上下文。在JavaScript解析器中，处理执行上下文分为两个阶段：
1. 进入执行上下文
2. 执行代码

这两个阶段与变量对象的修改密切相关，一般情况下，进入执行上下文会初始化当前上下文中的变量，执行代码的时候是对变量对象属性值的修改。

看下面例子：

```js
function test(a, b) {
  var c = 10;
  function d() {}
  var e = function _e() {};
  (function x() {});
}
 
test(10); // call
```

当以10为参数进入“test”函数上下文的时候，对应的AO如下所示：

```js
AO(test) = {
  a: 10,
  b: undefined,
  c: undefined,
  d: 
  e: undefined
};
```

可见，在进入函数执行上下文的时候，AO/VO的大部的属性都已经初始化了，只是刚开始的值是undefined而已。接下来，到了执行代码阶段，AO就会修改为如下形式：

```js
AO['c'] = 10;
AO['e'] = <指向函数表达式"_e">;
```

再次注意到，这里函数表达式“_e”仍在内存中，这是因为它被保存在声明的变量“e”中，而同样是函数表达式的“x”却不在AO/VO中： 如果尝试在定义前或者定义后调用“x”函数，这时会发生“x未定义”的错误。未保存的函数表达式只有在定义或者递归时才能调用。

因此：**执行上下文的数据是以变量对象的属性的形式进行存储的。每次进入执行上下文的时候，就会创建变量对象，并且赋予其属性初始值，随后在执行代码阶段会对属性值进行更新**。

## 作用域链（Scope Chain）

>作用域链是一条变量对象的链，它和执行上下文有关，用于在处理标识符时候进行变量查询。

函数上下文的作用域链在函数调用的时候创建出来，它包含了活跃对象和该函数的内部[[Scope]]属性。

```js
activeExecutionContext = {
    VO: {...}, // 或者 AO
    this: thisValue,
    Scope: [ // 所用域链
      // 所有变量对象的列表
      // 用于标识符查询
    ]
};
```

其实作用域链可以表示为如下形式：

>Scope = AO + [[Scope]]

或者

>Scope = VO + All Parent VOs

两个形式其实是等效的，下面通过函数的生命周期来说明这个问题。
函数的生命周期分为**创建阶段**和**调用阶段**。

### 函数的创建

来看一个例子

```js
var x = 1;
function foo (y) {
    console.log(x + y);
}
foo(1);
```

很明显，结果是输出2。在进入函数上下文的时候，活跃对象AO里面没有包括“x”，但是却可以访问到x的值。那么“foo”函数是怎么访问到变量“x”的呢？

这是因为函数的内部属性[[Scope]]来实现。

>[[Scope]]是一个包含了所有上层变量对象的分层链，它属于当前函数上下文，并在函数创建的时候，保存在函数中。

从这里我可以知道，[[Scope]]就等价于上层的所用变量对象（VO），因此

>Scope = VO + All Parent VOs

这里要注意的很重要的一点是：[[Scope]]是在函数创建的时候保存起来的——静态的（不变的），只有一次并且一直都存在——直到函数销毁。 比方说，哪怕函数永远都不能被调用到，[[Scope]]属性也已经保存在函数对象上了。

另外要注意的一点是：[[Scope]]与Scope(作用域链)是不同的，前者是函数的属性，后者是上下文的属性。比如上面的例子，“foo”函数的[[Scope]]如下所示：

```js
foo.[[Scope]] = [
  globalContext.VO // === Global
];
```

之后，有了函数调用，就会进入函数上下文，这个时候会创建活跃对象并且this的值和Scope（作用域链）都会确定。下面请看详细分解。

### 函数的调用

在进入上下文，创建AO之后，上下文的Scope属性(作用域链，用于变量查询)会定义如下，因为活跃对象是Scope数组的第一个元素，所以添加在作用域链的最前面。

>Scope = [AO].concat([[Scope]]);

下面是一个相对比较复杂的例子：

```js
var x = 10;
 
function foo() {
 
  var y = 20;
 
  function bar() {
    var z = 30;
    alert(x +  y + z);
  }
 
  bar();
}
 
foo(); // 60
```

针对上述代码，对应了如下的变量/活跃对象，函数的[[Scope]]属性以及上下文的作用域链：

全局上下文的变量对象如下所示：

```js
globalContext.VO === Global = {
  x: 10
  foo: 
};
```

在“foo”函数创建的时候，其[[Scope]]属性如下所示：

```js
foo.[[Scope]] = [
  globalContext.VO
];
```

在“foo”函数激活的时候（进入上下文时），“foo”函数上下文的活跃对象如下所示：

```js
fooContext.AO = {
  y: 20,
  bar: 
};
```

同时，“foo”函数上下文的作用域链如下所示：

```js
fooContext.Scope = fooContext.AO + foo.[[Scope]]
 
fooContext.Scope = [
  fooContext.AO,
  globalContext.VO
];
```

在内部“bar”函数创建的时候，其[[Scope]]属性如下所示：

```js
bar.[[Scope]] = [
  fooContext.AO,
  globalContext.VO
];
```

在“bar”函数激活的时候，其对应的活跃对象如下所示：

```js
barContext.AO = {
  z: 30
};
```

同时，“bar”函数上下文的作用域链如下所示：

```js
barContext.Scope = barContext.AO + bar.[[Scope]] // i.e.:
 
barContext.Scope = [
  barContext.AO,
  fooContext.AO,
  globalContext.VO
];
```

如下是“x”，“y”和“z”标识符的查询过程：

```js
- "x"
-- barContext.AO // not found
-- fooContext.AO // not found
-- globalContext.VO // found - 10
```

```js
- "y"
-- barContext.AO // not found
-- fooContext.AO // found - 20
```

```js
- "z"
-- barContext.AO // found - 30
```

补充一点：**当函数通过Function构造器来创建的时候，其[[Scope]]属性永远都只包含全局对象**。 哪怕在上层上下文中（非全局上下文）创建一个闭包都是无济于事的。

至此，综合以上知识总结：

> 创建函数 →→ 确定[[Scope]]
调用函数 →→ 确定Scope =  AO + [[Scope]]
**调用函数过程发生： 包括进入执行上下文（创建Scope Chain，创建AO，确定this），执行代码**。

## 详细解读执行上下文

从上面内容可以知道，当函数被调用的时候，JavaScript解析器会把执行上下文分为两个阶段：

1. Creation Stage[当函数被调用，但是还没开始执行代码时]
- 创建作用域链
- 创建声明的变量，函数，和arguments对象
- 确定`this`的值
2. Activation / Code Execution Stage:
- 赋值,引用函数和解释/执行代码。

因此，`execution context`执行上下文从概念上来说，可以表示为包含下面三个属性的对象：

```js
executionContextObj = {
    'scopeChain': { /* VO + 所有上层的V */ },
    'variableObject': { /* arguments / parameters, inner variable and function declarations */ },
    'this': {}
}
```

更加具体的过程可以参照下图（摘自参考文章[What is the Execution Context & Stack in JavaScript?](http://davidshariff.com/blog/what-is-the-execution-context-in-javascript/)），但是其实上面内容已有涉及，所以不重复了

![](http://ww2.sinaimg.cn/large/005JoIL8gw1fbmwxdzwszj30hb0mldlh.jpg)

## this

联系前面内容，`this`是执行上下文的一个属性：

```js
activeExecutionContext = {
  VO: {...},
  this: thisValue
};
```

This与上下文的可执行代码类型有关，其值在进入上下文阶段就确定了，并且在执行代码阶段是不能改变的。

### 全局代码中this的值

这种情况下最为简单，this的值就是全局对象本身。也可以借此，来间接地获取引用：

```js
// 显式定义全局对象的属性
this.a = 10; // global.a = 10
alert(a); // 10
 
// 通过赋值给不受限的标识符来进行隐式定义
b = 20;
alert(this.b); // 20
 
// 通过变量声明来进行隐式定义
// 因为全局上下文中的变量对象就是全局对象本身
var c = 30;
alert(this.c); // 30
```

### 函数代码中this的值

函数代码中this值的第一个特性（同时也是最主要的特性）就是：它并非静态的绑定在函数上。

正如此前提到的，this的值是在进入上下文的阶段确定的，并且在函数代码中的话，其值每次都会大不相同。然而，一旦进入执行代码阶段，其值就不能改变了。
比方说，要想给this赋一个新的值是不可能的，因为this根本就不是变量：

```js
var foo = {x: 10};
 
var bar = {
  x: 20,
  test: function () {
 	this = foo  // 不能给this赋值
    alert(this.x);
};
 
bar.test(); 
```

首先，在一般的函数调用中，**this的值是由激活上下文代码的调用者决定的，也可以说取决于函数是如何调用的**。比如说，调用函数的外层上下文。this的值是由调用表达式的形式决定的。

影响调用上下文中的this的值的只有可能是调用表达式的形式，也就是调用函数的方式。 正如此前大家看到的，纵然是全局函数，this的值也会随着函数调用方式的不同而不同：

```js
function foo() {
  alert(this);
}
 
foo(); // global
 
alert(foo === foo.prototype.constructor); // true
 
// 然而，同样的函数，以另外一种调用方式的话，this的值就不同了
 
foo.prototype.constructor(); // foo.prototype
```

那么，究竟调用表达式的方式是如何影响this的值的呢？为了完全搞明白这其中的奥妙，首先，这里有必要先介绍一种内部类型——引用类型（the Reference type）。

### 引用类型

引用类型的值可以用伪代码表示为一个拥有两个属性的对象——base属性（属性所属的对象）以及该base对象中的propertyName属性：

```js
var valueOfReferenceType = {
  base: ,
  propertyName: 
};
```

引用类型的值只有可能是以下两种情况：

1. 当处理一个标识符的时候
2. 或者进行属性访问的时候

标识符其实就是变量名，函数名，函数参数名以及全局对象的未受限的属性。如下所示：

```js
var foo = 10;
function bar() {}
```

中间过程中，对应的引用类型的值如下所示：

```js
var fooReference = {
  base: global,
  propertyName: 'foo'
};
 
var barReference = {
  base: global,
  propertyName: 'bar'
};
```

要从引用类型的值中获取一个对象实际的值需要GetValue方法，该方法用伪代码可以描述成如下形式：

```js
function GetValue(value) {
 
  if (Type(value) != Reference) {
    return value;
  }
 
  var base = GetBase(value);
 
  if (base === null) {
    throw new ReferenceError;
  }
 
  return base.[[Get]](GetPropertyName(value));
 
}
```

上述代码中的[[Get]]方法返回了对象属性实际的值，包括从原型链中继承的属性：

```js
GetValue(fooReference); // 10
GetValue(barReference); // function object "bar"
```

对于属性访问来说，有两种方式： 点符号（这时属性名是正确的标识符并且提前已经知道了）或者中括号符号：

```js
foo.bar();
foo['bar']();
```

中间过程中，得到如下的引用类型的值：

```js
var fooBarReference = {
  base: foo,
  propertyName: 'bar'
};
```

问题又来了，引用类型的值又是如何影响函数上下文中this的值的呢？

总的来说，决定函数上下文中this的值的规则如下所示：

>函数上下文中this的值是函数调用者提供并且由当前调用表达式的形式而定的。 如果在调用括号()的左边，有引用类型的值，那么this的值就会设置为该引用类型值的base对象。 所有其他情况下（非引用类型），this的值总是null。然而，由于null对于this来说没有任何意义，因此会隐式转换为全局对象。

如下所示：

```js
function foo() {
  return this;
}
 
foo(); // global
```

上述代码中，调用括号的左侧是引用类型的值（因为foo是标识符）：

```js
var fooReference = {
  base: global,
  propertyName: 'foo'
};
```

相应的，this的值会设置为引用类型值的base对象，这里就是全局对象。

属性访问也是类似的：

```js
var foo = {
  bar: function () {
    return this;
  }
};
 
foo.bar(); // foo
```

同样的，也是引用类型的值，它的base对象是foo对象，激活bar函数的时候，this的值就设置为foo对象了：

```js
var fooBarReference = {
  base: foo,
  propertyName: 'bar'
};
```

然而，同样的函数以不同的激活方式的话，this的值就完全不同了：

```js
var test = foo.bar;
test(); // global
```

因为test也是标识符，这样就产生了另外的引用类型的值，其中base对象（全局对象）就是this的值：

```js
var testReference = {
  base: global,
  propertyName: 'test'
};
```

### 函数调用以及非引用类型

正如此前提到过的，当调用括号左侧为非引用类型的时候，this的值会设置为null，并最终变成全局对象。

我们来考虑下如下表达式：

```js
(function () {
  alert(this); // null => global
})();
```

上述例子中，有函数对象，但非引用类型对象（因为它不既不是标识符也不属于属性访问），因此，this的值最终设置为全局对象。
看下面这个复杂的例子：

```js
var foo = {
  bar: function () {
    alert(this);
  }
};
 
foo.bar(); // Reference, OK => foo
(foo.bar)(); // Reference, OK => foo
 
(foo.bar = foo.bar)(); // global?
(false || foo.bar)(); // global?
(foo.bar, foo.bar)(); // global?
```

这里主要疑问在最后三个表达式，这三个表达式添加了特定的操作之后，调用括号左侧就不再是引用类型的值了。

第一种情况——非常明确，是引用类型，最终this的值设置为base对象，foo。

第二种情况有一个组操作符（grouping operator），该操作符不会触发调用获取引用类型实际值的方法，比如：GetValue方法。 相应的，处理组操作符中间过程中——获得的仍然是一个引用类型的值，这也就解释了为什么this的值设置成了base对象，foo。

第三种情况是一个赋值操作符（assignment operator）,与组操作符不同的是，它会触发调用GetValue方法。等号右边调用GetValue方法，然后值传递给等号左边。 最后返回的时候就是一个函数对象了（而不是引用类型的值了），这就意味着this的值会设置为null，最终会变成全局对象。

第四和第五种情况也是类似的——逗号操作符和OR逻辑表达式都会触发调用GetValue方法，于是相应地就会丢失原先的引用类型值，变成了函数类型，this的值就变成了全局对象了。

### 引用类型以及null（this的值）

有这么一种情况下，当调用表达式左侧是引用类型的值，但是this的值却是null，最终变为全局对象。 发生这种情况的条件是当引用类型值的base对象恰好为活跃对象。如下例子：

```js
function foo() {
  function bar() {
    alert(this); // global
  }
  bar(); // 和AO.bar()是一样的
}
```

活跃对象总是会返回this值为——null（用伪代码来表示，AO.bar()就相当于null.bar()）。然后，如此前描述的，this的值最终会由null变为全局对象。

### 函数作为构造器被调用时this的值

这里要介绍的是函数上下文中关于this值的另外一种情况——当函数作为构造器被调用的时候：

```js
function A() {
  alert(this); // newly created object, below - "a" object
  this.x = 10;
}
 
var a = new A();
alert(a.x); // 10
```

在这种情况下，new操作符会调用“A”函数的内部[[Construct]]。 在对象创建之后，会调用内部的[[Call]]函数，然后所有“A”函数中this的值会设置为新创建的对象。

new操作发生的事情：
1. 新建个一个对象
2. 对象继承构造函数
3. 把this指向这个新建的对象

### 手动设置函数调用时this的值（apply，call）

Function.prototype上定义了两个方法，允许手动指定函数调用时this的值。这两个方法是：.apply和.call； 它们都接受第一个参数作为调用上下文中this的值。而它们的不同点其实无关紧要：对于.apply来说，第二个参数接受数组类型（或者是类数组的对象，比如arguments）, 而.call方法接受任意多的参数。这两个方法只有第一个参数是必要的——this的值。

如下所示：

```js
var b = 10;
 
function a(c) {
  alert(this.b);
  alert(c);
}
 
a(20); // this === global, this.b == 10, c == 20
 
a.call({b: 20}, 30); // this === {b: 20}, this.b == 20, c == 30
a.apply({b: 30}, [40]) // this === {b: 30}, this.b == 30, c == 40
```

## 函数

下面是补充一些之前遗漏的知识点：

函数声明后面的()会被当组操作符来处理，而非函数调用的()。因此如果有如下代码：

```js
// "foo" 是函数声明 并且是在进入上下文的时候创建的

alert(foo); // function
 
function foo(x) {
  alert(x);
}(1); // 这里只是组操作符，并非调用!
 
foo(10); // 这里就是调用了, 10
```

那究竟要怎么写才能达到创建一个函数后立马就进行调用的目的呢？ 答案是很明显的。它必须要是个函数表达式，而不能是函数声明。而创建表达式最简单的方式就是使用上述提到的组操作符。因为在组操作符中只可能是表达式。 这样一来解释器也不会纠结了，会果断将其以FE的方式来处理。这样的函数将在执行阶段创建出来，然后立马执行，随后被移除（如果有没有对其的引用的话）：

```js
(function foo(x) {
  alert(x);
})(1); // 好了，这样就是函数调用了，而不再是组操作符了，1
```

