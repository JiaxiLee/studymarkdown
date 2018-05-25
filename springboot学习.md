##Spring Boot 学习##

###自定义Application###

1. 实现接口 
    
	implements ApplicationRunner
	
2. 添加注解
	
	@SpringBootApplication——表示以springboot运行
	
	@Slf4j ——表示定义log实例

	@ServletComponentScan 扫包

	@EnableTransactionManagement
	
	
3. 写main方法

	main函数的类必须要再上一层目录下
	
	SpringApplication.run(自定义application.class, args);

4. 在集成mybatis的时候一直报了这个错误

------------------------------------------------------------------------------------------
	Field tagMapper in com.blog.lee.serviceImpl.TagService required a bean of type 'com.blog.lee.persistence.mapper.TagMapper' that could not be found.

	
	Action:
	
	Consider defining a bean of type 'com.blog.lee.persistence.mapper.TagMapper' in your configuration.

------------------------------------------------------------------------------------------
最后查看文档及网上搜索找到一种解决方式如下：

在Application接口中添加这个注解@MapperScan("com.blog.lee.persistence.mapper")