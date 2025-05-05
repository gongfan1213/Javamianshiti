### 第23章 Java新版本特性
#### 23.1 Java 8：函数式编程
2014年，Oracle发布了Java 8，在这一版本中推出了很多新的特性，如函数式编程、Stream相关API、新的时间处理类和接口支持默认方法等。

本节介绍Java 8中重要的一个新特性——提供了对函数式编程的支持。

Java 8在java.util.function下增加了一系列的函数接口，其中主要有Consumer、Supplier、Predicate和Function等。

函数式接口：有且只有一个抽象方法的接口被称为函数式接口，函数式接口适用于函数式编程的场景。

Lambda表达式是Java中函数式编程的体现，可以使用Lambda表达式创建一个函数式接口的对象。即适用于函数式编程场景的接口，可以被隐式转换为Lambda表达式来表示接口的一个实现。

Java 8中专门为函数式接口引入了一个新的注解@FunctionalInterface：
```java
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface FunctionalInterface {}
```

这个注解可以用来声明接口，一旦使用该注解来定义接口，那么编译器就会强制校验这个接口是否有且只有抽象方法。

但这个注解不是必需的，只要符合函数式接口的定义，这个接口就是函数式接口。

1. **Supplier**

Supplier是一个供给型接口，简单地说，这就是一个返回某些值的方法。

一个简单的Supplier如下：
```java
public List<String> getList() {
    return new ArrayList<>();
}
```
使用Supplier表示：
```java
Supplier<List<String>> listSupplier = ArrayList::new;
```
Supplier中有一个get方法，使用这个方法会得到一个返回值，例如：
```java
List<String> strings = listSupplier.get();
```
此时会得到一个List。

2. **Consumer**

Consumer是一个消费型接口，简单地说，这就是一个使用某些值（如方法参数）并对其进行操作的方法。

一个简单的Consumer如下：
```java
public void sum(String a1) {
    System.out.println(a1);
}
```
使用Consumer表示：
```java
Consumer<String> printConsumer = a1 -> System.out.println(a1);
```
最常见的使用Consumer的例子就是Stream.forEach(Consumer)这样的用法，它接受一个Consumer，该Consumer消费正在迭代的流中的元素，并对每个元素执行一些操作，比如打印：
```java
Consumer<String> stringConsumer = (s) -> System.out.println(s.length());
Arrays.asList("ab", "abc", "a", "abcd").stream().forEach(stringConsumer);
```
Consumer中提供了一个accept方法，这个方法就是对消费者的调用：
```java
printConsumer.accept("hello world");
```
此时会打印hello world。

3. **Predicate**

Predicate是一个断言型接口，这是一个判断接口，返回true或者false的判断结果。
一个简单的Predicate如下：
```java
public boolean judge(int a1) {
    return a1 > 0;
}
```
使用Predicate表示：
```java
Predicate<Integer> predicate = a1 -> a1 > 0;
```
Predicate中有一个test方法，这个方法会返回一个布尔值，例如：
```java
predicate.test(1)
predicate.test(-1)
```
以上调用predicate.test得到的结果分别是true和false。

4. **Function**

Function是一个方法型接口，即输入一个参数，得到一个结果。

一个简单的Function如下：

```java
public int increment(int a1) {
    return a1 + 1;
}
```
使用Function表示：
```java
Function<Integer, Integer> function = a1 -> a1 + 1;
```
Predicate中有一个apply方法，这个方法会返回一个结果，例如：
```java
function.apply(1);
```
得到的结果是2。

**小结**

以上四个主要的函数式接口的含义如下表所示，其中T表示任意类型。

|接口|类型|主要方法|方法入参|方法返回值|
| ---- | ---- | ---- | ---- | ---- |
|Function|方法型|apply|T|T|
|Predicate|断言型|test|T|Boolean|
|Supplier|供给型|get|无|T|
|Consumer|消费型|accept|T|无|

#### 23.2 Java 8：接口的默认方法
在Java 8之前，接口中只能有方法声明，不能有方法的实现，但在Java 8中，提供了默认方法（Default Method），即可以在接口中定义默认方法。

我们只需在方法名前面加default关键字即可实现默认方法。例如：
```java
public interface TestDefault {
    default void print() {
        System.out.println("hello world");
    }
}
```
当这个接口被实现之后，实现类中如果没有重写print方法，那么在方法调用时，接口中的print方法会被调用，如果方法被重写过，那么接口中的print方法会被覆盖。

在2.4节介绍Java中的继承时，我们提到了Java为了避免菱形继承，所以不支持多继承。

但是，Java支持同时实现多个接口，而Java 8中支持了默认方法，这就相当于通过implements就可以从多个接口中继承多个方法了。这不就产生了菱形继承的问题了吗？

Java是怎么解决菱形继承问题的呢？我们定义两个接口，并提供默认方法：
```java
public interface Pet {
    public default void eat() {
        System.out.println("Pet Is Eating");
    }
}
public interface Mammal {
    public default void eat() {
        System.out.println("Mammal Is Eating");
    }
}
```
然后定义一个Cat，让其分别实现两个接口：
```java
public class Cat implements Pet, Mammal {
}
```
在编译期会报错：
```
error: class Cat inherits unrelated defaults for eat() from types Mammal and Pet
```
这时就要求在Cat类中必须重写eat()方法：
```java
public class Cat implements Pet, Mammal {
    @Override
    public void eat() {
        System.out.println("Cat Is Eating");
    }
}
```
可以看到，Java并没有真正解决多继承的歧义问题，而是把这个问题识别出来，留给开发人员通过重写方法的方式自己解决。

#### 23.3 Java 9：模块化技术
在Java 8推出3年以后的2017年9月，Java 9如期而至，也是从这个版本开始，Java修改了新版本的发布周期，改为固定的6个月一次，即分别在每年的3月份和9月份发布新的版本。

Java 9提供了超过150项新功能特性，其中比较重要的变化就是模块化技术（Modular）。提到Java模块化技术，就不得不提一个名叫Jigsaw的项目。

Jigsaw是OpenJDK项目下的一个子项目，这个项目的主要目标是：

（1）使开发人员更容易构建和维护类库和大型应用程序。

（2）提高Java SE平台实现的安全性和可维护性。

（3）提高应用程序性能。

（4）允许Java SE平台和JDK缩小规模，以便在小型计算设备和密集云部署中使用。

其实这个项目早在Java 7中就启动了，但由于巨大变动，最终在Java 9中才姗姗来迟。

这个项目所谓的模块化做了什么事情呢？又解决了哪些问题呢？

首先我们需要理解什么是模块。

**23.3.1 模块**

在OpenJDK的官网上，关于模块的解释是：
“A module is a named, self - describing collection of code and data. Its code is organized as a set of packages containing types, i.e., Java classes and interfaces; its data includes resources and other kinds of static information”

这句话不容易理解，其实可以把module和Java中我们熟知的package、jar等放在一起理解，它只是Java中另一个包含了类、接口、资源文件和静态信息的集合体而已。

比如，在Java 9之前，Java中常用的类库都被统一打包到一个rt.jar中，即使我们只想使用其中的部分功能，也要依赖整个JRE。

但是，在Java 9中，采用模块这种划分方式，可以更细粒度地划分这些类库，即通过模块化，把原来的一个大的jar包拆分成多个模块，如java.logging和java.sql等，都可以被封装在一个单独的模块中。这样，我们可以按需依赖部分模块。

![image](https://github.com/user-attachments/assets/b3d4de17-ce19-4d25-8b78-4df082398b5f)


为了区分于jar文件，模块以.jmod作为扩展名。在Java 9中，不再有jre目录了（也就没有rt.jar和tool.jar了），取而代之的是jmods目录，打开这个目录，可以看到很多.jmod文件。而且在Java 9的bin目录下，也增加了一个新命令jmod。

jmod是JDK 9中新增的一个命令，它位于JDK_HOME\bin目录中。它主要用来操作jmod文件，如创建一个JMOD文件（create）、列出一个JMOD文件的内容（list）、打印一个模块的描述（describe）、记录使用的模块的Hash值（hash）。使用jmod工具的一般语法如下：
```
jmod <subcommand> <options> <jmod-file>
```
我们可以使用这个命令来操作jmod文件，比如查看一个模块中包含哪些内容：
```
jmod list java.base.jmod
```
jmod文件中的主要内容包含以下信息：
```
classes/module-info.class
classes/sun/util/calendar/CalendarDate.class
classes/java/util/HashMap.class
conf/net.properties
conf/security/java.policy
conf/security/java.security
```
其中就包含了一些我们熟悉的Class文件和配置文件等，这个module-info.class文件主要是用来描述这个模块的文件。

我们来看一个模块的描述信息：
```
jmod describe java.sql.jmod
```
输出信息如下：
```
java.sql@9
exports java.sql
exports javax.sql
requires java.base mandated
requires java.logging transitive
requires java.transaction.xa transitive
requires java.xml transitive
uses java.sql.Driver
platform macos-amd64
```
其中主要包含了以下几部分信息：
- 模块名称。
- 依赖哪些模块。
- 导出模块内的哪些包。
- 开放模块内的哪些包。
- 提供哪些服务。
- 依赖哪些服务。

**23.3.2 模块化的好处**

Java 9的模块化技术给我们带来的好处主要有以下几个方面。

1. **精简JRE**

在Java 9之前，想要搭建Java运行环境，需要依赖一整个JRE的所有jar包，在Java中引入模块化系统之后，JDK自身被划分为94个模块。通过Java 9新增的jlink工具，开发者可以根据实际应用场景，随意组合这些模块，只选择自己需要的模块，将它们组装成一个自定义的JRE即可，从而可以有效地缩小Java运行时环境的大小。



精简的JRE不仅使得Java的运行环境搭建变得更加简单，而且还使Java在运行时占用的内存更小。这使得在IoT领域使用Java进行开发变得更加友好。

2. **更优美的依赖关系**

模块化之后，每一个模块中都会包含描述信息，可以根据module-info.class中的模块描述信息计算出各个模块间的依赖关系。在Java 9之后，JDK中各个模块的依赖关系见本书下载资源。



而在Java 9之前，主要的类库之间的依赖关系很复杂，而且还有很多循环依赖。



而在Java 9中，通过模块化技术可以解决循环依赖的问题，即在应用启动时可以做依赖分析，如果发现循环依赖，就可以终止启动。

3. **访问控制更加细粒度**

在Java之前，控制文件的访问权限只有public、private和protected。只要一个类或者方法是public的，就可以在任何地方被任何类访问。



在Java 9中，这种情况已经有所改变了，利用module descriptor中的exports关键词，模块维护者可以精准控制哪些类对外开放使用，哪些类只能内部使用。



在Java 9中，可访问性被更加细化成以下6种：

- public to everyone。

- public but only to specific modules。

- public only within a module。
- Protected。

- <package>。

- Private。


类的可见性被更加细化，这就带来了更高的安全性。

**小结**

本节简单地介绍了Java 9的模块化技术，主要聚焦于新特性的介绍，更多模块化的内容不再赘述。

#### 23.4 Java 10：本地变量类型推断

北京时间2018年3月21日，Oracle官方宣布Java 10正式发布。这是Java大版本周期变化后的第一个正式发布版本。关于Java 10，最值得程序员关注的一个新特性恐怕就是本地变量类型推断（Local - Variable Type Inference）了。

1. **什么是本地变量类型推断**

本地变量类型推断其实是一个新的语法糖，类型推断并不是Java独有的特性，许多流行的编程语言，比如C++、C#及Go，在定义过程中，都提供了一种局部变量类型推断的功能（例如，C++提供了auto关键字，C#提供var关键字）。

在当前版本的Java中定义局部变量时，我们需要在赋值语句的左侧提供显式类型，并在赋值语句的右侧提供实现类型。例如：
```java
MyObject value = new MyObject();
List list = new ArrayList();
```
在Java 10中，可以这样定义对象：
```java
var value = new MyObject();
var list = new ArrayList();
```
本地变量类型推断引入了var关键字，所以不需要显式地规范变量的类型。

如果只是想单独地使用这个特性，在定义局部变量时引入var关键字即可。 
