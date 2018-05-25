##myeclipse 构建maven父子依赖项目##


###创建父项目###
1. new —— maven project
2. 勾选create a simple project(skip .....) 和 use default w....
3. 填写Group Id —— Artifact Id ——Packaging(**父项目一定选择pom**)——finish
4. 点击bloglee pom.xml ——overview 右侧的create创建子项目

###项目中的层级关系###

	在子项目中增加
	<dependency>
		<groupId>bloglee</groupId> 父项目
		<artifactId>bloglee</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</dependency>
