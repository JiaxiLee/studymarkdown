##  使用springmvc上传文件类 ##

#### 方式一 ####

### **jsp**页面部分代码 ###

使用commonsmultipartresolver 方式进行提交。源码可以通过maven进行查看。

spring-servlet.xml配置如下：

	<!-- 多部分文件上传 -->
	<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
	     <property name="maxUploadSize" value="104857600" />
	     <property name="maxInMemorySize" value="4096" />
	     <property name="defaultEncoding" value="UTF-8"></property>
	</bean>

1.form表单结构

	<form name="Form2" action="<%=path%>/sys/dataManage/test.do" method="post"  enctype="multipart/form-data">
		
		<div class="form-group">
			<label for="version">版本</label> 
			<input type="textarea" name=version
				class="form-control " id="version" placeholder="当前软件版本2.2.8">
		</div>
		<div class="form-group">
			<label for="versionInfo">版本信息</label>
			<textarea class="form-control" rows="3" id="versionInfo" name="versionInfo"
				placeholder="1.修复了部分错误；&#10;2.优化了显示的内容及逻辑；"></textarea>
		</div>
		<div class="form-group">
			<label for="apkUpload">APK上传</label> 
			<input type="file"
				name="file" id="apkUpload">
			<p class="help-block">Example block-level help text here.</p>
		</div>
	
		<input type="submit" class="btn btn-success" value="submit"/>
	</form>

**action** 对应后台spring的controller部分的路径

2.java后台部分源码

**controller**部分：
	
	@RequestMapping("/uploadAppPkg")
	public Result uploadAppPkg(@RequestParam("file") CommonsMultipartFile file，
		@RequestParam("version") String version, 
		@RequestParam("versionInto") String versionInfo
	){
		System.out.println(file.getName());
		return Result.ok(1000);
		//return dataService.uploadAppPkg();
	}

**service**部分：

	public Result uploadApk( CommonsMultipartFile file，String version,String versionInfo){
		//获取源文件名称
		String fileName = file.getOriginalFilename();
		//指定新文件存储路径
		String path = "";
		//创建新文件
		File newFile = new File(path+fileName);
		//内容复制
		file.transferTo(newFile);
	} 

#### 方式二 ####

使用**FormData**格式封装jsp页面部分，使用**MultipartFile**接收。

jsp页面部分如下：
	
	<div name="Form2">
		<div class="form-group">
			<label for="apkAddr">APK地址</label> 
			<input type="text"
				class="form-control" id="apkAddr" name="apkAddr"
				placeholder="http://imtt.dd.qq.com/16891/1125B8F2856CCD9074386EBC107570CC.apk?fsname=com.idbk.solarassist_2.2.8(User)_220.apk&csr=1bbd">
		</div>
		<div class="form-group">
			<label for="apkUpload">APK上传</label> 
			<input type="file"
				name="file" id="file">
			<p class="help-block">Example block-level help text here.</p>
		</div>
		<button type="button" class="btn btn-success" id="uppPkg" />submit</button>
	</div>

js封装参数部分：

	var formData = new FormData();
	formData.append("apkAddr",$("#apkAddr").value);
	formData.append("file", $('#file')[0].files[0]);
	$("#uppPkg").on('click',function(){
		$.ajax({
			url:'<%=path%>/test/testForm.do',
			type:"POST",
			dataType:"json",
			data:formData,
			cache: false,
	        processData: false,
	        contentType: false,
			success:function(data){
					console.log(data);
				},
			error:function(e){
				console.log(e)
				}
		});  
	})

Java接收部分：

	@RequestMapping("/test/testForm")
	public Result test(
	@RequestParam(value = "file", required = false) MultipartFile file，
	@RequestParam("apkAddr") String apkAddr){
		System.out.println(file.getOriginalFilename());
		System.out.println(apkAddr);
		return Result.ok();
	}