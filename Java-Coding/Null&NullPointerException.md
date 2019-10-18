# “Java五分钟” - null & NullPointerException（NPE）

![Photo by SunnyChan](http://112.126.103.179/upload/2019/10/LiuXingHuaYuan-96821933e78c4122abe39b12cbd57809.JPG)

Java 的 null 确实是一个麻烦的存在，只要你稍不注意，就会就会碰到 NullPointerException（NPE）的困扰。
甚至 NPE 的发明人 Tony Hoare 在 2009年说：“null 引用的设计是一个十亿美元的错误”。

然而 null 又是 Java 中一个基础又重要的概念，你又不得不深入的去了解它。
接下来我们看看怎么更好的在你的代码中处理 null。

## 什么是 null
null 是 Java 中的关键字，它是大小写敏感的。

### 空类型 和 空引用
Java 中的类型区分为，值类型 和 引用 类型，通常我们理解 null 是一个空引用，代表不确定性的对象。
实际上 null 的类型是 [空类型](https://docs.oracle.com/javase/specs/jls/se8/html/jls-4.html#jls-4.1)，空引用是空类型的唯一可能的值。
空类型没有名称，因此无法声明空类型的变量或将其他对象强制转换为空类型，但空引用始终可以转换为任何引用类型。

当 JVM 尝试对变量进行解引用，发现变量值是 null 而不是对象时，都会引发 NullPointerException。
导致这个问题的原因还是 Java 类型系统设计的不够合理，空类型实际上是和具体的对象引用类型存在区别，但是并没有在语言层面做约束。

而 Java 中规定任何已声明但未初始化的引用变量都会自动引用 null，这也导致了 NPE 的频发。 

### null 的语义
上面说到 null 标识空类型，这是明确的。但如果结合代码语境，有时 null 所表达的语义又会含糊不清。
例如，Map.get(key) 返回 Null 时，可能表示 map 中的值是 null ，亦或 map 中没有 key 对应的值。
有时候程序员会用 null 来表示失败、成功，甚至几乎任何情况。

## null 的处理
根据上面的分析，要处理好 null，需要对 null 做好检查，以及定义清楚清楚 null 表达的含义。
另外就是一些 避免 NPE 的好的代码实践。

## null 检查
由于 NPE 带来的恐慌，出现一些极端的编码实践，如 对引用变量的每次解引用前都做 null 值检查。
带来的问题就是 代码中出现大量的样板代码，浪费大量的时间，提高了代码的维护成本。

以下是一些 null 检查方法的总结，大家可以酌情在代码中使用。

### Java7 java.util.Objects API
* Objects.isNull()

* Objects.nonNull()

* requireNonNull 校验参数是否为空，否则抛出异常
* * requireNonNull(T obj)

* * requireNonNull(T obj, String message)　

* * requireNonNull(T obj, Supplier<String> messageSupplier)

### Java8 Optional API
Google 公司著名的 Guava 项目引入了Optional类，Guava通过使用检查空值的方式来防止代码污染，它鼓励程序员写更干净的代码。
受到Google Guava的启发，Optional类已经成为Java 8类库的一部分。

Optional实际上是个容器，提供很多有用的方法，这样就不用显式进行空值检测。
Optional给了我们一个优雅的代码风格来解决null安全问题。

#### 实例
* orElse()
```java
return str != null ? str : "Hello World"
```
```java
return strOpt.orElse("Hello World")
```
* 简化if-else
```java
User user = ...;
if (user != null) {
    String userName = user.getUserName();
    if (userName != null) {
        return userName.toUpperCase();
    } else {
        return null;
    }
} else {
    return null;
}
```
```java
User user = ...;
Optional<User> userOpt = Optional.ofNullable(user);

return user.map(User::getUserName)
            .map(String::toUpperCase)
            .orElse(null);
```

### @NonNull 注解
#### lombok.NonNull 
使用@NonNull在方法或构造函数的参数上，让Lombok生成null-check语句。
默认情况下会抛出一个以field name is marked @NonNull but is null作为异常消息的java.lang.NullPointerException。
lombok.NonNull 作用于编译器，用于生成校验Null的代码，这和 Lombok 项目用于简化模板代码编写的目标是一致的。

#### javax.validation.constraints.NotNull
JSR 303 - Bean Validation - 为实体验证定义了元数据模型和API。

@NotNull 用于运行时校验，而不是静态分析。
用来帮助检测用户输入的，强制某些用户输入值不可以为 null。

### Guava
#### Preconditions.checkNotNull(T)
检查<T> value为null直接抛出NullPointerException，否则直接返回value。
```java
Preconditions.checkNotNull(1);//1
Preconditions.checkNotNull(null,"is null");  //java.lang.NullPointerException: is null
Preconditions.checkNotNull(null, "%s is null !", "object"); //java.lang.NullPointerException: object is null !
```
#### org.apache.commons.lang.StringUtils
* StringUtils.isEmpty()
```md
StringUtils.isEmpty(null): true
StringUtils.isEmpty(""): true
StringUtils.isEmpty(" "): false
```
* StringUtils.isNotEmpty()
```java
StringUtils.isEmpty(null): true
StringUtils.isEmpty(""): true
StringUtils.isEmpty(" "): false
```
* StringUtils.isBlank()
```java
StringUtils.isBlank(null): true
StringUtils.isBlank(""): true
StringUtils.isBlank(" "): true
```
* StringUtils.isNotBlank()
```java
StringUtils.isNotBlank(null): false
StringUtils.isNotBlank(""): false
StringUtils.isNotBlank(" "): false
```

## 避免 NullPointerException（NPE）
除了上述 对 null 做好检查 来避免 NPE 外，我们还可以有以下的编程实践：

1. 使用 "xxxx".equal(str) 而不是str.equal( "xxxx") 
2. 使用String.valueOf() 代替 toString()
```java
public static String valueOf(Object obj) {
    return (obj == null) ? "null" : obj.toString();
}
```
3. expression ? value1 : value2
4. 使用空集合而不是 null 来初始化 集合变量

## NPE 处理的一些其它思路
* 不要做 null 检查
[Why I Never Null-Check Parameters](https://dzone.com/articles/why-i-never-null-check-parameters)

* Groovy 和 C＃等其他语言提供空条件运算符
```C#
int? length = customers?.Length; // null if customers is null   
```
可以使用 Java 8 中的某些功能功能（例如方法引用）来创建类似的功能。

## 参考
[Better Null-Checking in Java](https://dev.to/scottshipp/better-null-checking-in-java-ngk)
