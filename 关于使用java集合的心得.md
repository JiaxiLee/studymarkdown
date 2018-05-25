## 关于使用java 集合的一点心得 ##


### Collection ###

集合是我们开发中必不可少的一个数据容器，在开发过程中我们会经常用到判断集合中是否包含某一元素.
判断包含的的方法为“contains(Object obj)”.API中关于这个方法的说明如下：

>**contains**
>
boolean contains(Object o)

>如果此 collection 包含指定的元素，则返回 true。更确切地讲，当且仅当此 collection 至少包含一个满足 (o==null ? e==null : o.equals(e)) 的元素 e 时，返回 true。 
>
**参数：**

>o - 测试在此 collection 中是否存在的元素。 
>
>**返回：**
如果此 collection 包含指定的元素，则返回 true 

>**抛出：**
 
>ClassCastException - 如果指定元素的类型与此 collection 不兼容（可选）。 
NullPointerException - 如果指定的元素为 null，并且此 collection 不允许 null 元素（可选）。

查看方法可知比较的时候是调用**equals(e)**方法进行的判断，因此在自定义类中使用该方法，则必须覆写从Object 继承来的equals方法；

例：判断集合中Student 集合中是否有包含某一Student,以Student的年纪作为标识。
Student类如下：

	public class Student implements Comparable<Student>{
		private int age;
		private String name ;
		public int getAge() {
			return age;
		}
		public String getName() {
			return name;
		}
		public void setAge(int age) {
			this.age = age;
		}
		public void setName(String name) {
			this.name = name;
		}
		
		public Student(String name,int age){
			this.name = name;
			this.age = age;
		}
		@Override
		public int compareTo(Student s) {
			// TODO Auto-generated method stub
			return s.age - this.age;
		}
		
		@Override
		public boolean equals(Object obj){  
	        //首先判断是否为空  
	        if(obj != null){  
	            //自己和自己比较时,直接返回true  
	            if(obj == this){  
	                return true;  
	            }  
	            //判断是否是同类型的对象进行比较  
	            if(obj instanceof Student){  
	            	Student book = (Student)obj;  
	                if(book.age == this.age){  
	                    return true;  
	                }  
	            }  
	        }  
	        return false;  
	    }  
	}

测试类如下：

	public class TestCo {
		public static void main(String[] args) {
			Student a = new Student("zhangsan",30);
			Student b = new Student("wangwu", 20);
			Student c = new Student("zhaoer", 20);
			Collection<Student> collection = new ArrayList<Student>();
			collection.add(a);
			collection.add(b);
			System.out.println(collection.size());
			System.out.println(collection.contains(c));
			 
		}
	}

这样就实现了包含方法的覆写；***注： **一定要覆写equals方法**