## ObjectMapper序列化的学习 ##

###jar包###

导入jar包：

	import com.fasterxml.jackson.annotation.JsonInclude;
	import com.fasterxml.jackson.annotation.JsonProperty;
	import com.fasterxml.jackson.core.JsonParseException;
	import com.fasterxml.jackson.databind.JsonMappingException;
	import com.fasterxml.jackson.databind.ObjectMapper;

Maven依赖：

	<dependency>
		<groupId>com.fasterxml.jackson.core</groupId>
		<artifactId>jackson-databind</artifactId>
		<version>2.9.2</version>
	</dependency>

注解方式实现序列化和反序列化代码如下：
	
	/**
	 * 
	 */
	package com.idbk.edims.webmonitor.test;
	
	import java.io.IOException;
	
	import com.fasterxml.jackson.annotation.JsonInclude;
	import com.fasterxml.jackson.annotation.JsonProperty;
	import com.fasterxml.jackson.core.JsonParseException;
	import com.fasterxml.jackson.databind.JsonMappingException;
	import com.fasterxml.jackson.databind.ObjectMapper;
	
	/**
	 * Description 
	 * @author lijiaxi 
	 * @date 2018-4-12
	 */
	public class JacksonTester {
		   public static void main(String args[]){
		      ObjectMapper mapper = new ObjectMapper();
		      String jsonString = "{\"Name\":\"Mahesh\", \"age\":21}";
	
		      //map json to student
		      try {
		         //Student student = mapper.readValue(jsonString, Student.class);
		         //System.out.println(student);
		    	  Student student = new Student();
		    	  student.setAge(16);
		    	  student.setMe("zhangsan");
		    	  student.setAddr("中华人民共和国");
		         //mapper.enable(SerializationConfig.Feature.INDENT_OUTPUT);
		         //mapper.enable(SerializationConfig.)
		         mapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);
		         //mapper.setConfig(SerializationConfig.DEFAULT_MAPPER_FEATURES);
		         jsonString = mapper.writeValueAsString(student);
		         System.out.println(jsonString);
	
		      } catch (JsonParseException e) {
		         e.printStackTrace();
		      } catch (JsonMappingException e) {
		         e.printStackTrace();
		      } catch (IOException e) {
		         e.printStackTrace();
		      }
		   }
		}
	
	class Student {
		
		@JsonProperty("Name")   
		private String me;
		@JsonProperty("Age")
		private int age;
		@JsonProperty("Address")
		private String addr;
		public Student(){}
		public String getMe() {
	      return me;
	   }
		public void setMe(String Name) {
	      this.me = Name;
	   }
		public int getAge() {
	      return age;
	   }
		public String getAddr(){
			return addr;
		}
		public void setAddr(String addr){
			this.addr = addr;
		}
		public void setAge(int age) {
	      this.age = age;
	   }
		public String toString(){
	      return "Student [ name: "+me+", age: "+ age+ " ]";
	   }	
	}

  **在一般的注解解析中序列化主要的是看get方法，反序列化主要的是看set方法**