# Null & NullPointerException（NPE）



## Null 检查
### Java7 java.util.Objects API
* Objects.isNull()

* Objects.nonNull()

* requireNonNull 校验参数是否为空，否则抛出异常
* * requireNonNull(T obj)

* * requireNonNull(T obj, String message)　

* * requireNonNull(T obj, Supplier<String> messageSupplier)

### Java8 Optional API
```md
Google公司著名的Guava项目引入了Optional类，Guava通过使用检查空值的方式来防止代码污染，它鼓励程序员写更干净的代码。
受到Google Guava的启发，Optional类已经成为Java 8类库的一部分。
```
```md
Optional实际上是个容器，提供很多有用的方法，这样就不用显式进行空值检测。
Optional给了我们一个优雅的代码风格来解决null安全问题。
```
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
* lombok.NonNull 
```md
使用@NonNull在方法或构造函数的参数上，让Lombok生成null-check语句。
```
```md
默认情况下会抛出一个以field name is marked @NonNull but is null作为异常消息的java.lang.NullPointerException。
```
```md
lombok.NonNull 作用于编译器，用于生成校验Null的代码，这和 Lombok 项目用于简化模板代码编写的目标是一致的。
```
* javax.validation.constraints.NotNull
```md
JSR 303 - Bean Validation - 为实体验证定义了元数据模型和API。
```
```md
@NotNull 用于运行时校验，而不是静态分析。
用来帮助检测用户输入的，强制某些用户输入值不可以为 null。
```
## Guava
* Preconditions.checkNotNull(T)
```md
检查<T> value为null直接抛出NullPointerException，否则直接返回value。
```
```java
Preconditions.checkNotNull(1);//1
Preconditions.checkNotNull(null,"is null");  //java.lang.NullPointerException: is null
Preconditions.checkNotNull(null, "%s is null !", "object"); //java.lang.NullPointerException: object is null !
```
### org.apache.commons.lang.StringUtils
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