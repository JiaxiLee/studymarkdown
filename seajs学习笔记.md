## seajs学习笔记 ##

### 基础结构 ###

main.js 文件配置 config基本配置及各部分释义：

	seajs.config({
		alias:{
			'jquery':'lib/jquery-2.1.4.min.js',
			'vue':'lib/vue.js',
			'test':'test.js'
		},
		charset: 'utf-8',
	    preload: ['jquery','vue'],
	    timeout: 16000,
	    debug: true
	});
alias:各部分的js代码别名;

preload:预加载的js项，相当于在执行testjs中，已经加载了jquery

### 代码块书写模板 ###

标准代码块（model.js）如下：

	方式一
	define(function (require,exports,module){
		var fn = function (){};
		module.exports = fn;
	})
	
	方式二
	define(function (require,exports,module){
		module.exports = {
			init:function (){},
			event:function (){}
		}
	})
	
	方式三
	define({
		key:value
	})
	
	方式四
	define([1,2,3])


### 代码块调用 ###

在页面中调用seajs代码块如下：

	seajs.use('main',function(){

	});

	function相当于是一个回调函数，如果main有返回，则可以在这样写
	seajs.use('main',function(reback){
		reback();
	})
	reback作为main返回的别名



下例为表单检验工具：

	/**
	 * 检查表单
	 */
	define(function(require, exports, module) {
		// 输入对象父容器加上错误提醒样式
		var markErr = function(inputObj) {
			inputObj.one('focus', function() {
				$(this).parent().removeClass('has-error');
			}).parent().addClass('has-error');
		};
		
		// 通过验证样式
		var markSuc = function(inputObj) {
			inputObj.parent().addClass('has-success');
		}
		
		// 验证是否匹配正则表达式
		var testRegEx = function(inputObj, regEx) {
			if (regEx.test($.trim(inputObj.val()))) {
				markSuc(inputObj);
				return true;
			} else {
				markErr(inputObj);
				return false;
			}
		};
		
		// 检查是否为空
		var ckEmpty = function(inputObj) {
			if ($.trim(inputObj.val()) == '') {
				return false;
			} else {
				return true;
			}
		};
		
		// 是否为空
	    var isEmpty = function(inputObj) {
	    	if (ckEmpty(inputObj)) {
	    		markSuc(inputObj);
				return false;  // 不为空
			} else {
				markErr(inputObj);
				return true;  // 为空
			}
	    };
	    
	    // 检查位数
	    var ckLength = function(inputObj, min, max) {
	    	if (!ckEmpty(inputObj)) {
	    		return false;
	    	} else {
	    		var val = $.trim(inputObj.val());
	    		if (!isNaN(min)) {
	    			if (val.length >= min) {
	    				if (typeof max == 'undefined') {
	    					markSuc(inputObj);
	    					return true;
	    				} else if (!isNaN(max)) {
	    					if (val.length <= max) {
	    						markSuc(inputObj);
	    						return true;
	    					} else {
	    						markErr(inputObj);
	    						return false;
	    					}
	    				}
	    			} else {  // 长度小于min
	    				markErr(inputObj);
	    				return false;
	    			}
	    		}
	    	}
	    };
	    
	    // 是否是数字
	    var isNumber = function(inputObj) {
	    	if (!ckEmpty(inputObj)) {
	    		return false;
	    	} else {
	    		if (isNaN(inputObj.val())) {  // 非数字
	    			markErr(inputObj);
	    			return false;
	    		} else {
	    			markSuc(inputObj);
	    			return true;
	    		}
	    	}
	    };
	    
	    // 邮箱
	    var isMail = function(inputObj) {    	
	    	var regEx = /^([a-zA-Z0-9_\.\-])+\@(([a-zA-Z0-9\-])+\.)+([a-zA-Z0-9]{2,4})+$/;
	    	if (!ckEmpty(inputObj)) {
	    		return false;
	    	} else {
	    		return (testRegEx(inputObj, regEx));
	    	}
	    };
	    
	    // 11位国内手机号
	    var isMobile = function(inputObj) {    	
	    	var regEx = /^1[1-9][0-9]\d{8}$/;
	    	if (!ckEmpty(inputObj)) {
	    		return false;
	    	} else {    		
	    		return (testRegEx(inputObj, regEx));
	    	}
	    };
	    
	    // 国际手机号，eg: +86 138 8888 8888
	    var isIntlMobile = function(inputObj) {
	    	var regEx = /\+(9[976]\d|8[987530]\d|6[987]\d|5[90]\d|42\d|3[875]\d|2[98654321]\d|9[8543210]|8[6421]|6[6543210]|5[87654321]|4[987654310]|3[9643210]|2[70]|7|1)\s*\d{1,14}$/;
	    	if (!ckEmpty(inputObj)) {
	    		return false;
	    	} else {
	    		return (testRegEx(inputObj, regEx));
	    	}
	    };
	    
	    exports.isEmpty      = isEmpty;
	    exports.isNumber     = isNumber;
	    exports.isMail       = isMail;
	    exports.isMobile     = isMobile;
	    exports.isIntlMobile = isIntlMobile;
	    exports.testRegEx    = testRegEx;
	    exports.ckLength     = ckLength;
	    exports.markErr      = markErr;
	    exports.markSuc      = markSuc;
	});