# Inheritance

标签： js

---

高程毕竟是高程，我第一次看高程里面继承部分的时候，简直一脸懵逼，时隔一年，第二次看了高程的继承部分之后，我把里面的知识点整理出来，渣渣，求勿喷：>

这篇文章主要总结了高程里面提到的6种继承，以及他们的特点，核心，优缺点。

---

### 继承之前——**prototype, [[Prototype]], constructor**

- prototype：每个函数都具有的一个属性，是一个指针，指向一个对象，而这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。

- constructor： 是原型对象自动获得的一个属性，这个属性包含一个指向prototype属性所在函数的指针。

- [[Prototype]]: 这是每个实例对象都具有的一个内部属性，也是一个指针，指向构造函数的原型对象。在浏览器下可以通过`__proto__`来访问。（其实就是每个对象都会有，因为原型链最后一环肯定是继承于Object）

```js
function Person() {}
Person.prototype.name = 'lun';
Person.prototype.sayName = function(){
    console.log(this.name);
};

var personOne = new Person();
personOne.sayName();
```

简单一个图说明：
![](http://ww2.sinaimg.cn/large/6f76e081gw1f65883a8b3j20o90a20ta.jpg)


### 原型链继承

原型链继承是实现继承的主要方法，核心就是：**让一个原型是另一个类型的实例**。

```js
function Super() {
    this.value = 'super';
    this.array = [1, 2];
}

function Sub() {
}

Sub.prototype = new Super();     //核心

var instance  = new Sub();
console.log(instance.value);    //super
console.log(instance.array);    //[1, 2]

instance.value = 'instance';
instance.array.push(3);
console.log(instance.value);    //instance
console.log(instance.array);    //[1, 2, 3]

var anotherInstance = new Sub();
console.log(anotherInstance.value);     //super
console.log(anotherInstance.array);     //[1, 2, 3]
```

- 优点
    1. 简单，容易实现
- 缺点
    1. 每个实例共享原型对象的引用属性(如上例中，instance和anotherInstance共享原型对象中的array属性)，某个实例对原型中的引用属性的改变会导致所有实例都相应改变。
    2. 创建子类实例时，无法向父类构造函数传参

因此，来到了借用构造函数的继承方法。

这里再补充一下原型链继承过程容易出现的问题：不能直接使用对象字面量创建原型方法，这样做相当于重写原型链。类似下面这样：

```js
sub.prototype = {       //这样子相当于直接重写了sub.prototype
    sayHi: function() {
        console.log('hi');
    }
    ...
}
```

### 借用构造函数

借用构造函数级继承方法（也叫做伪造继承和经典继承），解决了原型链继承的两个缺点。核心思想就是： 在子类构造函数的内部调用父类的构造函数。（借助apply或者call方法）

```js
function Super(value) {
    this.value = value;
    this.array = [1, 2];
    this.getValue = function(){console.log(this.value)};
}

Super.prototype.getArray = function(){console.log(this.array)};

function Sub(value) {
    //只继承了Super的属性，继承不了Super原型上的方法
    Super.call(this, value);    //核心
}

var instance  = new Sub('ins');
instance.array.push(3);
console.log(instance.value);    //ins
console.log(instance.array);    //[1, 2, 3]

var anotherInstance = new Sub('anotherIns');
console.log(anotherInstance.value);     //anotherIns
console.log(anotherInstance.array);     //[1, 2]

instance.getValue();    //ins
anotherInstance.getValue();     //anotherIns
console.log(instance.getValue === anotherInstance.getValue);    //false

instance.getArray();    //报错
```

- 优点
    1. 解决了子类实例共享父类引用属性的问题
    2. 创建子类实例时，可以传参
- 缺点
    1. 父类定义的方法子类无法复用，每个子类实例中各有一个新的父类方法（上例中倒数第二行代码可以看出）
    2. 父类原型定义的属性和方法，子类无法继承（上例中最后一行代码可以看出）

因此，就出现了结合了原型继承和借用构造函数继承的组合继承。
    
### 组合继承

组合继承，就是将原型链和借用构造函数的技术组合到一块。借用原型链实现对原型属性和方法的继承，再通过借用构造函数来实现对实例属性的继承。

javascript中**最常用的继承模式**

```js
function Super(value) {
    this.value = value;
    this.array = [1, 2];
}

Super.prototype.getValue = function(){console.log(this.value)};

function Sub(value, property) {
    Super.call(this, value);    //核心,继承属性

    this.property = property;
}

Sub.prototype = new Super();    //核心，继承了父类属性和原型链上的方法
Sub.prototype.constructor = Sub;
Sub.prototype.getProperty = function() {console.log(this.property);};

var instance  = new Sub('ins', 'property');
instance.array.push(3);
console.log(instance.array);    //[1, 2, 3]
instance.getValue();            //ins
instance.getProperty();         //property

var anotherInstance = new Sub('anotherIns', 'anotherProperty');    
console.log(anotherInstance.array);     //[1, 2]
anotherInstance.getValue();             //anotherIns
anotherInstance.getProperty();          //anotherProperty
```

- 优点
    1. 解决了实例共享父类引用属性的问题
    2. 创建子类实例时，可以传参
    3. 函数（方法）得到复用
- 缺点
    1. 基本已经接近完美了，但还是有一点瑕疵就是：调用了两次父类构造函数，第一次是在创建子类原型的时候，第二次是在子类型构造函数的内部。因此子类原型上有一份多余的父类实例属性，子类实例上的那一份屏蔽了子类原型上的。。。造成了内存浪费

### 寄生组合式继承

所谓寄生组合式继承，即通过借用构造函数来继承属性，通过原型链的混成形式来继承方法。

核心方法
```js
function inheritePrototype(subType, superType) {
    var prototype = Object(superType.prototype);    //核心，创建对象（创建一个包含父类原型中所用方法的对象）
    prototype.constructor = subType;                //核心，增强对象（将这个对象的constructor指向子类）
    subType.prototype = prototype;                  //核心，指定对象

}
```

```js
function Super(value) {
    this.value = value;
    this.array = [1, 2];
}

Super.prototype.getValue = function(){console.log(this.value)};

function Sub(value, property) {
    Super.call(this, value);    //核心,继承属性

    this.property = property;
}

inheritePrototype(Sub, Super);

Sub.prototype.getProperty = function() {console.log(this.property);};

var instance  = new Sub('ins', 'property');
instance.array.push(3);
console.log(instance.array);    //[1, 2, 3]
instance.getValue();            //ins
instance.getProperty();         //property

var anotherInstance = new Sub('anotherIns', 'anotherProperty');    
console.log(anotherInstance.array);     //[1, 2]
anotherInstance.getValue();             //anotherIns
anotherInstance.getProperty();          //anotherProperty
```

- 优点： 解决了上面出现的所有问题

>接下来补充一下另外的两种继承： 原型式继承和寄生式继承

### 原型式继承

主要实现的思路：
```js
function object(o){
    function F() {}
    F.prototype = o;
    return new F();
}
```

ECMAScript5新增`Object.create()`方法来规范化了原型式继承。这个方法接收两个参数，一个是作为新对象原型的对象和(可选的)一个为新对象定义额外属性的对象。

```js
var superObject = {
    name : 'super',
    array: [1, 2]  
};

var instance = Object.create(superObject);
instance.name = 'instance';
instance.array.push(3);
console.log(instance.name);     //instance
console.log(instance.array);    //[1, 2, 3]

var anotherInstance = Object.create(superObject);
anotherInstance.name = 'anotherInstance';
console.log(anotherInstance.name);  //anotherInstance
console.log(anotherInstance.array); //[1, 2, 3]
console.log(superObject.array);     //[1, 2, 3]
```

- 优点： 不用借助构造函数，直接通过`Object.create()`传入一个父类对象，即可让子类继承这个父类对象的属性和方法。
- 缺点： 和使用原型模式意义，也会共享父类对象的引用属性。

### 寄生式继承

其基本思路是： 创建一个仅用于封装继承过程的函数，该函数在内不以某种方式来增强对象，最后返回这个对象。

核心代码示例：
```
function createAnother(o) {
    function F() {};
    F.prototype = o;
    var clone = new F();        //创建一个对象(使用原型式继承)
    clone.sayName = function() {  //增强对象
        console.log(this.name);
    }
    return clone;               //返回对象
}
```

示例代码：

```
var person = {
    name: 'lun'
}; 

var instance = createAnother(person);
instance.sayName();     //lun
```

- 优点： 在上一种原型式继承的基础上，创建新的子类对象的时候，可以添加子类对象自定义的方法。
- 缺点： 也还是没有解决共享属性的问题。









        






