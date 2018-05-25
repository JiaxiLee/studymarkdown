## js对象的学习 ##

### 深入理解对象 ###

值类型的类型判断用typeof，引用类型的类型判断用instanceof；

js的数据类型主要包含：number、boolean、string、function、object、undefined。

在java中，对象是new 一个class出来的东东，里面包含了属性，方法，字段等。在js中，数组是对象，函数是对象，对象也是对象。在js对象中只有属性，没有方法，其方法用键值对表示。**切记：对象是属性的集合**

### 函数与对象的关系

先看下面的例子：

	eg1:
	var fn = function(){};
	console.log(typeof fn);//function
	console.log(fn instanceof Object);//true
	
	eg2:
	funciton Obj(){
		this.name = "张三";
		this.age = 30;
	}
	
	var obj = new Obj();
第一个例子可以看出来函数既是一个function，又可以是一个Object;
第二个例子可以看出对象可以是一个函数生成的。
但是对象可以是如下的形式：
	
	var obj = {
		name:"张三",
		age:32
	};
	var obj2 = [1,2,3];
实际上可以总结为**所有的对象都是函数生成的**

上例实际上可以剖析为：

	var obj = new Object();
	obj.name = "张三";
	obj.age = 32;

	var obj2 = new Array();
	obj2[0] = 1;
	obj2[1] = 2;
	obj2[2] = 3;
	
	console.log(typeof Object);// function
	console.log(typeof Array);//funciton
	
**js原型——prototype**

对象是由函数生成的，函数本身是一个对象（属性的集合），每一个函数有一个默认的属性prototype,这个属性值是一个对象（属性的集合），prototype 属性值有个默认的属性即为constructor指向函数本身。

见下例：
	
	var fn = function(){};
	console.log(fn.prototype);//Object{}
	console.log(fn.prototype.constructor);//function(){}

eg:
	
	function Fn(){}
	Fn.prototype.name = "wangwu";
	Fn.prototype.age = 32;
	var fn = new Fn();//一切对象都是函数创建的
	console.log(fn.age);//32
	console.log(fn.name);//wangwu

每个对象有个隐性的属性_proto_，这个属性指向了创建这个对象的函数的prototype。_proto_称之为隐式原型。

**js隐式原型——\_proto_**

每个函数function都有一个原型属性prototype，每个对象都有一个_proto_，对象的_proto_指向创建该对象的函数的prototype；

所有的函数是由Function创建的，因此Object._proto_ === Function.prototype

Object.prototype._proto_ = null;

**instanceof解析**

typeof判断应用类型的时候，返回的只有两个值，Object和function

a instanceof A ，判断的时候沿着a._proto_ 和 A.prototype 两条线找，如果有交集则返回true，否则为false.

instanceof表示的是一种继承关系即原型链结构；

**js继承**

访问一个对象属性时，先在基本属性中找，然后沿着_proto_这条主线往上查找。

eg：

	var Foo = function (){};
	var foo = new Foo();
	foo.a = 10;
	Foo.prototype.a = 100;
	Foo.prototype.b = 200;

	console.log(foo.a);//10
	console.log(foo.b);//200

判断是否为基本属性则可以使用foo.hasOwnProperty(item);hasOwnProperty从Object.prototype继承而来的。

所有的函数都是继承Function.prototype,而Function.prototype继承Object.prototype

**js上下文**

js初始化时执行的准备工作：

1. 变量、函数表达式——变量声明，默认赋值为undefined；
2. this——赋值；
3. 函数声明——赋值；

js在执行代码之前，把所有的变量全部拿出来，有的直接赋值，有的用undefined占位符。 


**js中的this**

函数中的this如何取值是在函数调用的时候确定的，在函数定义的时候确定不了。

情形一：构造函数

	var Foo = new function(){
		this.name = "zhangsan";
		this.age = 32;
	}

	var foo = new Foo();

	其上this指向的是对象生成的实例。
情形二：函数作为对象的一个属性

	var obj = {
		fn:function(){
			console.log(this);
		}
	}

	obj.fn();
	此时的this指向的是该对象obj
情形三：函数使用call或者apply调用的时候

	var obj = {
		name:'zhangsan'
	}
	function aa(){
		console.log(this);
	}

	aa.call(obj);
	this的值就是传入对象的值
情形四：在全局或者普通函数调用时 this指向window ，在对象内部函数里面套函数，this指向的也是window

**js作用域**

js只有两个作用域，全局作用域，函数体作用域

作用域是在函数定义的时候就创建的，而作用域中的值是在执行函数的时候赋值的。

对于自由变量赋值，要到创建该作用域函数的地方取值。