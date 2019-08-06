# Java 的异常机制

2019-07-30 by 陈光

## What Is Exception
程序运行时，发生的不被期望的事件，阻止了程序按预期正常执行，这就是异常。

### 异常处理机制
在异常发生时，异常处理机制能让程序按照代码预先设定的异常处理逻辑执行。
针对性地处理异常，让程序尽最大可能恢复正常并继续执行，且保持代码的清晰。
 
### Java 异常层级结构
![Exception-Hierarchy-Diagram.png](_pic/Exception-Hierarchy-Diagram.png) 

## Throwable类
是Java异常类型的顶层父类。
一个对象只有是 Throwable 类的（直接或者间接）实例，才能被异常处理机制识别。

### getCause方法
获取当前异常发生的原因，Cause自身也是由Cause引起的，依此类推，会形成异常链。
当我们捕获一个异常，重新抛出一个新的异常时，一般会要求在新的异常中需要设置 Cause。
通常情况下，我们无需打印Casue信息来定位问题，但不排除有些特殊情况下需要。

### getStackTrace() 
通常一些未知异常发生时，我们需要打印程序的调用栈信息来快速定位问题，如：
```java
Arrays.stream(ex.getStackTrace()).forEach(r -> Log.warn(r.toString()));
```

## Error类
Error 表示主要由运行应用程序的环境引起的错误。
对于这类错误导致的应用程序中断，仅靠程序本身无法恢复和和预防。
例如，当JVM内存不足时发生OutOfMemoryError，或者当堆栈溢出时发生StackOverflowError。

## Exception类
Exception类 表示主要由应用程序本身引起的异常。
遇到这类异常，应该尽可能处理，使程序恢复运行，而不应该随意终止异常。
例如：
当应用程序尝试访问null对象时发生NullPointerException，
或者当应用程序尝试转换不兼容的类型时发生ClassCastException。

## Checked vs. Unchecked
Unchecked Exception 发生在运行时，所以不能被编译器感知，Error 和 RuntimeException 以及他们的子类
属于 Unchecked Exception 异常。
这类异常发生的原因多半是代码写的有问题，对于这些异常，应该修正代码，而不是通过异常处理器处理 。

Checked Exception 必须在编译时被捕获处理，否则编译不会通过，除了RuntimeException以外的Exception类，都属于 Checked Exception。

## 自定义异常
一般情况下，通过扩展Exception类实现，这样自定义的异常都属于检查异常（Checked Exception）。
如果要自定义非检查异常，则扩展 RuntimeException类。

### 构造函数
按照惯例，自定义异常应该总是包含如下的构造函数：
一个无参构造函数；
一个带有String参数的构造函数，并传递给父类的构造函数；
一个带有String参数和Throwable参数，并都传递给父类构造函数；
一个带有Throwable 参数的构造函数，并传递给父类的构造函数。
如：
```java
public class MyBusinessException extends Exception {
  
	private static final long serialVersionUID = 7718828512143293558L;
	
	private final ErrorCode code;

	public MyBusinessException(ErrorCode code) {
		super();
		this.code = code;
	}

	public MyBusinessException(String message, Throwable cause, ErrorCode code) {
		super(message, cause);
		this.code = code;
	}

	public MyBusinessException(String message, ErrorCode code) {
		super(message);
		this.code = code;
	}

	public MyBusinessException(Throwable cause, ErrorCode code) {
		super(cause);
		this.code = code;
	}
	
	public ErrorCode getCode() {
		return this.code;
	}
}
```
注意：以上的代码增加了ErrorCode，去掉ErrorCode的设置，是符合上述规范的。

## Bad Case
1. 禁止在构造函数中抛出异常；
2. 大部分情况下不建议在循环中进行异常处理，应该在循环外对异常进行捕获处理；
3. 不使用异常来管理业务逻辑，应该使用条件语句；
4. 禁止使用返回值来表示异常，如C程序经常出现的-1等。

## 最佳实践
1. 异常的名字必须清晰且有具体的语义；
2. 应该捕获具体的异常，而不是统一 catch(Exception e) ；
3. 定义自己的异常类层次；
4. 使用适当的异常捕获粒度，为一个基本操作定义一个 try-catch 块，禁止为了简便，将几百行代码放到一个 try-catch 块中。
5. 为自定义异常生成足够的文档说明，至少是 JavaDoc。
6. 不推荐在 catch 中使用 Throwable（即不推荐在Catch再次抛出异常）。
7. Catch语句需要尽量保证简单，不能包含复杂的业务逻辑。
8. 不要忽略 Exception，即使可能在分析一个不可能发生的异常（代码是变化的，当前不可能不代表未来也不可能）。

## Pitfall
1. 多个 Catch 时，最先捕获特定的异常，否则可能导致后面的异常永远不可能捕获。


## 参考
* [Why, When and How to Implement Custom Exceptions in Java](https://stackify.com/java-custom-exceptions/)
* [9 Best Practices to Handle Exceptions in Java](https://stackify.com/best-practices-exceptions-java/)
* [7 Common Mistakes You Should Avoid When Handling Java Exceptions](https://stackify.com/common-mistakes-handling-java-exception/)


