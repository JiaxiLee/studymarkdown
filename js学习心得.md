## 关于学习js的一点心得 ##


### 巧用js对象做表格呈现 ###

当后台返回一组对象数据，需要在前台用表格呈现时，replyData数据如下：

	Array[7]
	0:Object
	1:Object
	2:Object
	3:Object
	4:Object
	5:Object
	6:Object

对应的数据内容之一例子如下：

	code:"CO2"
	expression:"sum(nullif($27336,0))*0.997"
	id:43
	name:"C02减排"
	order:40
	signal:"27336"
	tempId:3
	type:2
	unit:"Weight"
这里我们可以巧用数组对象去构建表格，该例如下：

	var data = [
		{field:'code',title:'编码'},
		{field:'expression',title:'表达式'},{field:'id',title:'Id'},
		{field:'name',title:'名称'},
		{field:'order',title:'序号'},
		{field:'tempId',title:'模板id'},
		{field:'type',title:'类型'},
		{field:'unit',title:'单位'}
		]

然后利用data数据和返回replyData 进行双循环就可以构建表达;

***

### 页面加载执行函数 ###

当页面加载完成后执行函数，js写法如下：

	window.onload = function (){
		/*
		执行的函数体	
		*/	
	};

jQ写法如下：
	
	$(function (){
		/*
		执行的函数体	
		*/
	});
等效于：
	
	jQuery(function ($){
		/*
		执行的函数体	
		*/
	});

***

### js 对象的创建 ###

	var OneObject = function (){
		var obj = new Object();
		obj.Init = function (){
			/*
			执行的函数体	
			*/
		}
		return obj;
	}
调用的时候：
	var useObj = new OneObject();
	useObj.Init();
