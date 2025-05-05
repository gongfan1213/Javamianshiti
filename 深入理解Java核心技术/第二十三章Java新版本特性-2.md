### 23.4 Java 10：本地变量类型推断
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

2. **背后的故事**

在JEP 286诞生之前，Oracle曾做过一个调查，主要是想了解社区对于这一特性的反应。
 - 第一个调查是：你认为Java引入局部变量的类型推断怎么样？
 - 第二个调查是：你希望使用哪个关键字来定义变量？

从上面的两个调查可以知道，这一特性是受到广大开发者欢迎的。

3. **它将如何影响我的代码**

当一个新特性来临时，我们首先要问自己一个问题：它将如何影响我的代码？下面分析这一特性可以在哪些场景中使用，以及不能在哪些场景中使用。
    - **适用范围**
        - 初始化局部变量：一定是初始化局部变量时才可以使用本地变量类型推断，只定义局部变量是不可以使用本地变量类型推断的。例如，var foo是不可以的，但var foo = "Foo"是可以的。
        - 增强for循环的索引：如for (var nr : numbers)。
        - 传统for循环的局部变量定义：如for (var i = 0; i < numbers.size(); i++)。
    - **不适用范围**
        - 方法的参数。
        - 构造函数的参数。
        - 方法的返回值类型。
        - 对象的成员变量。
        - 只是定义而不初始化。

4. **原理**

本地变量类型推断主要可以应用在以下几个场景中：
```java
public class VarDemo {
    public static void main(String[] args) {
        // 初始化局部变量
        var string = "hollis";
        // 初始化局部变量
        var stringList = new ArrayList<String>();
        stringList.add("hollis");
        stringList.add("chuang");
        stringList.add("weChat:hollis");
        stringList.add("blog:http://www.hollischuang.com");
        // 增强for循环的索引
        for (var s : stringList) {
            System.out.println(s);
        }
        // 传统for循环的局部变量定义
        for (var i = 0; i < stringList.size(); i++) {
            System.out.println(stringList.get(i));
        }
    }
}
```
使用Java 10的javac命令进行编译：
```
/Library/Java/JavaVirtualMachines/jdk-10.jdk/Contents/Home/bin/javac VarDemo.java
```
生成VarDemo.class文件，使用jad对其进行反编译，得到以下代码：
```java
public class VarDemo {
    public static void main(String args[]) {
        String s = "hollis";
        ArrayList arraylist = new ArrayList();
        arraylist.add("hollis");
        arraylist.add("chuang");
        arraylist.add("weChat:hollis");
        arraylist.add("blog:http://www.hollischuang.com");
        String s1;
        for(Iterator iterator = arraylist.iterator(); iterator.hasNext(); System.out.println(s1))
            s1 = (String)iterator.next();
        for(int i = 0; i < arraylist.size(); i++)
            System.out.println((String)arraylist.get(i));
    }
}
```
本地变量类型推断写法与正常写法的代码的对应关系如下表所示。

|本地变量类型推断写法|正常写法|
| ---- | ---- |
|var string = "hollis";|String string = "hollis";|
|var stringList = new ArrayList();|ArrayList stringList = new ArrayList();|
|for (var s : stringList)|for (String s : stringList)|
|for (var i = 0; i < stringList.size(); i++)|for (int i = 0; i < stringList.size(); i++)|

ArrayList arraylist = new ArrayList()其实是ArrayList<String> stringList = new ArrayList<String>()解糖且类型擦除后的写法。

for(Iterator iterator = arraylist.iterator(); iterator.hasNext(); System.out.println(s1))其实是for (String s : stringList)这种for循环解糖后的写法。

所以，本地变量类型推断也是Java 10提供给开发者的语法糖。虽然在代码中使用var对变量进行了定义，但对于虚拟机来说，它不认识这个var，在.java文件编译成.class文件的过程中，会对代码进行解糖，使用变量真正的类型来替代var（如使用String string替换var string）。对于虚拟机来说，完全不需要对var做任何兼容性改变，因为它的生命周期在编译阶段就结束了。唯一变化的是编译器在编译过程中需要增加一个关于var的解糖操作。

感兴趣的读者可以编写两段代码，一段使用var，另一段不使用var，然后对比编译后的字节码。

5. **和JavaScript有什么区别**

很多人都知道，在JavaScript中，变量的定义就是使用var来声明的。所以，Java 10的本地变量类型推断一出现，就有人说，这不是“抄袭”JavaScript的吗？这和JavaScript中的var不是一样的吗？

其实，还真的不一样。

首先，JavaScript是一种弱类型（或称动态类型）语言，即变量的类型是不确定的。在JavaScript中使用'5' - 4这样的语法，它的结果是数字1，这里是将字符串和数字做运算了。

![image](https://github.com/user-attachments/assets/689b00db-d050-44ef-9a3c-fa873913acfb)


在Java中虽然可以使用var来声明变量，但它还是一种强类型的语言。通过上面反编译的代码可知，var只是Java给开发者提供的语法糖，最终在编译之后还要将var定义的对象类型定义成编译器推断出来的类型。

6. **本地变量类型推断到底会不会影响可读性**

本地变量类型推断最让人诟病的恐怕就是其可读性了，因为在本地变量类型推断出现之前，定义变量时要明确指定它的类型，所以在阅读代码时只要看其声明的类型就可以知道它的类型，全都使用var之后，则会损失一部分可读性。但在代码中使用var声明对象同样带来了很多好处，如代码更加简洁等。

如果开发者都使用var来声明变量，那么变量的名字就更加重要了。开发者会更注重变量名的可读性。相信不久以后，各大IDE就会推出智能显示变量的推断类型功能。

总之，对于本地变量类型推断这一特性，笔者的态度是比较积极的。

最后提出一个问题供读者思考，既然Java已经决定在新版本中推出本地变量类型推断，那么为什么要限制它的用法呢？现在已知的可以使用var声明变量的几个场景是初始化局部变量、增强for循环的索引和传统for循环的局部变量定义，还有几个场景是不支持这种用法的，比如方法的参数、构造函数的参数、方法的返回值类型、对象的成员变量、只是定义而不初始化。

**小结**
Java 10之后，在声明局部变量类型时可以使用var来告知编译器进行类型推断。这仅仅发生在变量初始化的阶段，就像var s = ""这样。此外，var也可以在普通for循环和增强for循环中使用。

除了局部变量，在属性和方法返回值类型中不能使用var。这样做是为了避免引起一些无法预知的错误。

### 23.5 Java 11：增强var
Java 10中增加了本地变量类型推断的特性，可以使用var来定义局部变量，编译器会根据赋值给变量的值来推断类型。

在Java 10中，不能将此特性与Lambda表达式一起使用，但是，在Java 11中，对var进行了增强，可以配合Lambda一起使用了。

例如，在以下简单的Lambda表达式中显式地指定了形参的类型：
```java
(String s1, String s2) -> s1 + s2;
```
当然，基于Java 8可以跳过参数类型，重写Lambda为：
```java
(s1, s2) -> s1 + s2;
```
在Java 11中，可以使用以下方式：
```java
(var s1, var s2) -> s1 + s2;
```
很多人看到这个例子后，会发现Java 11中的写法比Java 8中支持的写法更复杂一些。这么做又有什么好处呢？

那就是可以使用注解修饰符来定义局部变量，例如：
```java
(@Nonnull var s1, @Nullable var s2) -> s1 + s2;
```
如果没有指定类型，就不能使用@Nonnull这种注解，而有了var，就可以使用Lambda的写法对变量进行修饰，并且不失简洁。

当然，在Lambda中使用var有一些限制。例如，不能对某些参数使用var而对其他参数不使用var，例如：
```java
(var s1, s2) -> s1 + s2;
```
类似地，不能将var与显式类型混合使用，例如：
```java
(var s1, String s2) -> s1 + s2;
```

### 23.6 Java 12：switch表达式
在JDK 12中引入了Switch表达式作为预览特性，最终其在JDK 14成为正式版本的功能。

以前想要在switch中返回内容，一般语法如下：
```java
int i;
switch (x) {
    case "1":
        i = 1;
        break;
    case "2":
        i = 2;
        break;
    default:
        i = x.length();
        break;
}
```
在JDK 12中使用以下语法：
```java
int i = switch (x) {
    case "1" -> 1;
    case "2" -> 2;
    default -> {
        int len = args[1].length();
        yield len;
    }
};
```
或者：
```java
int i = switch (x) {
    case "1": yield 1;
    case "2": yield 2;
    default: {
        int len = args[1].length();
        yield len;
    }
};
```
在这之后，switch中就多了一个关键字用于跳出switch块，那就是yield，它用于返回一个值。

yield和return的区别在于：return会直接跳出当前循环或者方法，而yield只会跳出当前switch块。

### 23.7 Java 13：text block
在JDK 13之前，当从外部复制一段文本串到Java中时，文本串会被自动转义。比如有以下一段字符串：
```html
<html>
<body>
    <p>Hello, world</p>
</body>
</html>
```
将其复制到Java的字符串中，会展示成以下内容：
```java
"<html>\n" +
"    <body>\n" +
"        <p>Hello, world</p>\n" +
"    </body>\n" +
"</html>\n";
```
即字符串被自动进行了转义，这样的字符串看起来不是很直观。

为了解决这个问题，在JDK 12中曾引入了Raw String Literals特性，但在发布之前就放弃了。

在2019年9月，JDK 13提供了一项新的功能——文本块（Text Block）作为预览功能。最终在JDK 15中成为一个永久特性。

文本块是一个多行字符串文字，它避免了对大多数转义序列的需要，可以预测的方式自动格式化字符串，并在需要时让开发人员控制格式。

在JDK 13中，可以使用以下语法：
```java
"""
<html>
<body>
    <p>Hello, world</p>
</body>
</html>
""";
```
使用"""作为文本块的开始符和结束符，在其中就可以放置多行的字符串，不需要进行任何转义。比如常见的SQL语句：
```java
String query = """
    SELECT `EMP_ID`, `LAST_NAME` FROM `EMPLOYEE_TB`
    WHERE `CITY` = 'INDIANAPOLIS'
    ORDER BY `EMP_ID`, `LAST_NAME`;
""";
```

### 23.8 Java 14：更有价值的NullPointerException

2020年3月17日Java正式发布了JDK 14，在JDK 14中，共有16个新特性，本节主要介绍其中的一个特性：JEP 358: Helpful NullPointerExceptions。

1. **null何错之有**

对于Java程序员来说，null是令人头痛的问题，Java程序员时常会遇到空指针异常（NullPointerException）的问题。相信很多程序员都特别害怕在程序中出现NullPointerException，因为这种异常往往伴随着代码的非预期运行。

在编程语言中，空引用（Null Reference）是一个与空指针类似的概念，空引用是一个已宣告但其并未引用到一个有效对象的变量。

在Java 1中就包含了空引用和NullPointerException了，但空引用是伟大的计算机科学家Tony Hoare早在1965年发明的，最初作为编程语言ALGOL W的一部分。

1965年，英国一位名为Tony Hoare的计算机科学家在设计ALGOL W语言时提出了空引用的想法。ALGOL W是第一批在堆上分配记录的类型语言之一。Hoare选择空引用这种方式，“只是因为这种方法实现起来非常容易”。虽然他的设计初衷就是要“通过编译器的自动检测机制，确保所有使用引用的地方都是绝对安全的”，他还是决定为空引用开个绿灯，因为他认为这是为“不存在的值”建模最容易的方式。

但是在2009年，他开始为自己曾经做过这样的决定而后悔不已，把空引用称为“一个价值十亿美元的错误”。实际上，Hoare的这段话低估了过去五十年来数百万程序员为修复空引用所耗费的代价。因为在ALGOL W之后出现的大多数现代程序设计语言，包括Java，都采用了同样的设计方式，其原因是为了与更老的语言保持兼容，或者就像Hoare曾经陈述的那样，“只是因为这种方法实现起来非常容易”。

相信很多Java程序员都对null和NullPointerException深恶痛绝，因为它确实会带来各种各样的问题（来自《Java 8实战》）。例如：

 - 它是错误之源。NullPointerException是目前Java程序开发中最典型的异常。它会使代码膨胀。
 
 - 它让代码充斥着深度嵌套的null检查，代码的可读性糟糕透顶。
 
 - 它自身是毫无意义的。null自身没有任何的语义，尤其它代表的是在静态类型语言中以一种错误的方式对缺失变量值的建模。
 
 - 它破坏了Java的哲学。Java一直试图避免让程序员意识到指针的存在，唯一的例外是null指针。
 
 - 它在Java的类型系统上开了个口子——null并不属于任何类型，这意味着它可以被赋值给任意引用类型的变量。这会导致问题——当这个变量被传递到系统中的另一部分后，将无法获知这个null变量最初赋值到底是什么类型。

2. **其他语言如何解决NullPointerException问题**

知道，除了Java，还有很多其他的面向对象语言，在其他的一些语言中，是如何解决NullPointerException问题的呢？


比如在Groovy中使用安全导航操作符（Safe Navigation Operator）可以访问可能为null的变量：
```java
def carInsuranceName = person?.car?.insurance?.name
```
Groovy的安全导航操作符能够避免在访问这些可能为空引用的变量时发生NullPointerException，在调用链中的变量遭遇null时将空引用沿着调用链传递下去，返回一个null。

另外，在Haskell和Scala也有类似的替代品，如Haskell中的Maybe类型、Scala中的Option[T]。

在Kotlin中，其类型系统严格区分一个引用可以容纳null还是不能容纳。也就是说，一个变量是否可空必须显式声明，对于可空变量，在访问其成员时必须做空处理，否则无法编译通过：
```java
var a: String = "abc"
a = null // 编译错误
```
如果允许为空，则可以声明一个可空字符串，写作String?：
```java
var b: String? = "abc" // String?表示该String类型变量可为空
b = null // 编译通过
```
看到这个“?”，是不是发现和Groovy有点像？

下面分析作为TIOBE编程语言排行榜第一名的语言，Java对NullPointerException做出了哪些努力。

3. **Java做了哪些努力**
其实在Java 8推出之前，Google的Guava库中就率先提供了Optional接口来使null快速失败。

在Java 8中提供了Optional，Optional在可能为null的对象上做了一层封装，Optional对象包含了一些方法来显式地处理某个值是存在还是缺失，Optional类强制开发人员思考值不存在的情况，这样就能避免空指针异常

但是设计Optional类的目的并不是完全取代null，它的目的是设计更容易理解的api，通过opetional，开发人员可以从方法签名就知道这个函数有可能返回一个缺失的值，这样强制开发人员处理这些缺失的值

![image](https://github.com/user-attachments/assets/afdfb4b1-c6e0-4a88-a505-d29c65eb7077)


![image](https://github.com/user-attachments/assets/8aa8200c-d022-4f88-8f83-1aeb77b726d5)
