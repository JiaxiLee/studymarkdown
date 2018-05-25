##freemarker 学习总结##


###常用指令###
1. 注释——》<#-- -->
2. 包含——》<#include "">
3. 取值——》${varible} (和el表达式类似)



###macro###
macro 用于实现自定义指令，通过使用自定义指令，可以将模板片段定义成一个用户指令；

**语法格式**：


	<#macro name param1 param2 ... paramN>
		<#nested loopvar1, loopvar2, ..., loopvarN>
		<#return>
	</#macro>
<#nested>指令会执行宏调用指令开始和结束标记之间的模板片断
	
eg(**定义**):
	
	<#-- 公共顶部 -->
	<#macro header title="默认名称" keywords="默认文字" description="默认文字" canonical="">
	<#include "/common/annotation.ftl">
	<!DOCTYPE HTML>
	<html lang="zh-CN">
	<head>
	    <meta charset="utf-8">
	    <meta name="viewport" content="width=device-width, initial-scale=1">
	    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1"/>
	    <title>${title}</title>
	    <meta name="author" content="${config.authorName}(${config.authorEmail})">
	    <meta name="keywords" content="${keywords}"/>
	    <meta name="description" content="${description}" id="meta_description">
	    <link rel="canonical" href="${config.siteUrl}${canonical}" />
	    <#include "/layout/quote.ftl">
	    <#--黑白界面
	    <style>
	        html {
	            filter: grayscale(100%);
	            -webkit-filter: grayscale(100%);
	            -moz-filter: grayscale(100%);
	            -ms-filter: grayscale(100%);
	            -o-filter: grayscale(100%);
	            filter: url("data:image/svg+xml;utf8,<svg xmlns="\'http://www.w3.org/2000/svg\'"><filter id="\'grayscale\'"><feColorMatrix type="\'matrix\'" values="\'0.3333" 0.3333="" 0="" 1="" 0\'=""></fecolormatrix></filter></svg>#grayscale");filter:progid:DXImageTransform.Microsoft.BasicImage(grayscale=1);-webkit-filter:grayscale(1);}
	    </style>-->
	    <#nested>
	</head>
	<body>
	    <#include "/layout/header.ftl"/>
	</#macro>
eg(**使用**):

	<#-- config对象是由后台传入到前台页面的对象 -->
	<@header title="${config.siteName} | 一个程序员的个人博客"
	    keywords="${config.homeKeywords?if_exists}"
	    description="${config.homeDesc?if_exists}"
	    canonical="/${url?if_exists}">
	</@header>


