## 关于学习Vue的一点心得 ##

###Vue框架的标准模式###

		new Vue({
			el:,
			/*vue监听范围*/
			data:{},
			/*vue数据模型*/
			filters:{},
			/*局部过滤器*/
			mounted:function (){},
			/*生命周期调用*/
			methods:{}
			/*所有事件的绑定*/
		});
		
		/*Vue.filter() 定义全局过滤器*/


### Vue组件学习 ###

v-if
dom层级的显示与否，使用v-if则在dom层不会出现

而hidden则是html层级的显示与否


属性绑定：v-bind

	<div v-bind:class="{ active: isActive }"></div>
	active存在与否是由isActive决定的，
	isActive是在new Vue中的data定义的

数据双向绑定：v-model


废话不说先上代码

	<body>
		<div id="test">
			<my-email v-bind:email="email"></my-email>
		</div>
	</body>
	<script>
		Vue.component('my-email',{
			template:'<div class="letter" id="letter"><h4 class="title">{{email.title}}</h4><p>Dear <span v-for="item in email.to">{{item}} </span>,</p><p class="cont">{{email.content}}</p><ul class="sender"><li v-for="(value,key) in email.from">{{key}}:{{value}}</li></ul><p class="date">{{email.date}}</p></div>',
			props:['email']
		});
		
		new Vue({
			el:'#test',
			data: {
				email: ''
			},
			mounted:function (){
				this.$http.get("data/data.json").then(function (resp){
					this.email = resp.body;
				});
			}
		
		});
	</script>


mounted函数解析：该函数表示在vue实例创建时调用的函数。
如果要保证完全在vue实例创建完成后调用则必须加上

	mounted: function () {
	  this.$nextTick(function () {
	    // Code that will run only after the
	    // entire view has been rendered
	  })
	}

prop属性：把Vue实例中定义的data属性指向组件中的data属性。


###Vue过滤器###

filter过滤器：全局过滤器

	Vue.filter('filterName',function (){});//前面是过滤器的名称，后面是回调的函数

局部过滤器：

	new Vue({
		el:"",
		data:{},
		filters:{
			filterA:function(value1){},
			filterB:function(value1,value2){}
		}

	})

过滤器的使用

	{{value | filterA}}
	{{value1 | filterB(value2)}}  过滤器默认参数为 | 前的值


使用规范：

###Vue参数值得传递###

当通过props把组件data指向实例data时，需要在自定义组件中进行数据绑定；

例如：

	Vue.component("my-comp",{
		template:"<div></div>",
		props:['email']
	});

实例中：

	new Vue({
		el:"#app",
		data:{
			email:''
		}
	});

页面中：

	<my-comp v-bind:email="email"></my-comp>

###http集成Vue ###
页面中vue-resource的使用：

	this.$http.get("data/cartData.json",{"id":123}).then(function (res){
    					
			_this.productList = res.body.result.list;
			console.log(res.body.result)
			console.log(_this.productList)
			_this.totalMoney = res.body.result.totalMoney;
			console.log(_this.totalMoney)
		});
	解析：data/cartData.json 表示请求的路径---》url
	{"id":123}表示请求的请求参数，发送到服务器的
	then表示的回调，res表示从服务器返回的对象

在vue与后台交互过程中出现了参数获取不到的问题：解决方案：
	
	this.$http.post("${URL4ADDTEM}",this.rowEditTem,{emulateJSON:true}).then(
		function (res){
		}
	);
`this.rowEditTem 表示的是传递到后台的参数对象`


### Vue中组件调用实例方法###

在Vue组件中调用vue实例中的方法：

	Vue.component('my-component',{
		template:'',
		props:['data'],
		methods:{
			f1:function (){
				this.$emit('fn',paras);
			}

		}
	});

	Vue实例
	new Vue({
		el:'#app',
		data:{
			data:{}
		},
		methods:{
			defineMethod:function (){}
		}

	});

	页面
	<div id="app">
		<my-component v-on:fn="defineMethod"></my-component>
	</div>

通过以上的demo可以实现在组件中调用实例的方法
	

