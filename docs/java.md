[TOC]

# java知识体系
  
## 基础知识

* 基础元素
	* package
	* interface|\[abstract\]class
	* method
	* property
* 基本关系
	* is-a 继承()
	* has-a 聚合(=有关联,a中使用了b)
	* uses-a 依赖(主从关系)
	* 类与类
	* 类与对象
	* 对象与对象
* 面向对象
	* 多态
	* 封装
	* 继承
* 六大原则
	* 
* 数据类型
  * 基础类型与包装类型
	  * 精度
		  * float、double、BigDecimal
	  * 零补齐
	  * 类型转换
		  * parseInt
		  * valueOf
	  * 日期类型
		  * 千年虫
    * 比较
	    * hashCode
	    * equals
  * 字符串
  * 引用类型vs值类型
    * 类(Object,枚举,畸形类)
    * 接口
  * 泛型
* 集合
  * 线程安全集合
* 异常
  * Throwable
    * Error
    * Exception
      * RuntimeException(Unchecked Exception)
        * NullPointerException
        * IndexOutOfBoundsException
        * ClassCastException
        * OutofMemoryException(xms xmx)
      * Checked Exception(编译报错)	
        * ClassNotFoundException
        * IOException


### 语言应用
	- 常用的工具类
		- [Apache Common](https://commons.apache.org/)
			- Commons BeanUtils
			- Commons Codec
			- Commons Collections
			- Commons Compress
			- Commons FileUpload
			- Commons IO
			- Commons Lang3
				- java.lang.*的加强包
-
- 项目实践
	- Tomcat与arthas
		- 利用Archas的aop能力排查tomcat容器中的服务慢问题;
		- https://blog.csdn.net/Armour__r/article/details/109733038
	- @Deprecated
		```java
			@Deprecated
			public void method1() {

			}
		```
	- hprof是什么文件？
		- 是java进程的内存镜像文件，里面包含了内存堆详细的使用信息。
		- 在 java 中，hprof 文件有2部分组成，一部分是 hprof head，一部分是 hprof body。其中 head 比较简单，由版本号，IDSize和时间组成。
	- [word转pdf异常之小括号](https://blog.csdn.net/u012998306/article/details/124803061)
	- [java call cmd](https://www.cnblogs.com/youmiyou/p/15779230.html)

## 动态代理

```java
public interface Person {
	void rentHouse();
}

public class Renter implements Person {
	@Override
	public void rentHouse() {
		System.out.println("rent success");
	}
}

public class RenterInvocationHandle<T> implements InvocationHandler {
	
	private T target;

	public RenterInvocationHandle(T target) {
		this.target = target;
	}

	/**
	 *
	 * 1. 代表动态代理对象
	 * 2. 要执行的方法
	 * 3. 入参
	 **/ 
	@Overrider
	public Object invoke(Object proxy, Method method, Object[] args) 
		throws Throwable {

			Object result = method.invoke(target, args);

			return result;
	}
}

public class Main {

	public void static main(String[] args) {
		Person person = new Renter();
		InvocationHandler renterHandler = new InvocationHandler<Person>(person);

		InvocationHandler invoke = (Person)InvocationHandler.newProxyInstance(Person.class.getClassLoader(),new Class<?>[]{Person.class}, renterHandler); 

		Class<?>

	}

}

```