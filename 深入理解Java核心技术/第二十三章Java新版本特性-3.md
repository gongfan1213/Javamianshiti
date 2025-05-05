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



在Java 8中提供了Optional，Optional在可能为null的对象上做了一层封装，Optional对象包含了一些方法来显式地处理某个值是存在还是缺失，Optional类强制开发人员思考值不存在的情况，这样就能避免潜在的空指针异常。



但设计Optional类的目的并不是完全取代null，它的目的是设计更易理解的API。通过Optional，开发人员可以从方法签名就知道这个函数有可能返回一个缺失的值，这样强制开发人员处理这些缺失值的情况。



另一个值得一提的就是JDK 14中的新功能JEP 358: Helpful NullPointerExceptions。

4. **更有帮助的NullPointerException**

在JDK 14中对NullPointerException有了一个增强，既然NullPointerException暂时无法避免，那么就让它对开发者更有帮助一些。


每个Java开发人员都遇到过NullPointerExceptions异常。由于NPE可以发生在程序的几乎任何地方，因此试图捕获并从异常中恢复通常是不切实际的。因此，开发人员通常依赖于JVM来确定NullPointerException实际发生时的来源。例如，假设在这段代码中出现了一个NullPointerException：
```java
a.i = 99;
```
JVM将打印出导致NullPointerException的方法、文件名和行号：
```
Exception in thread "main" java.lang.NullPointerException
at Prog.main(Prog.java:5)
```
通过以上堆栈信息，开发人员可以定位到a.i = 99这一行，并推断出a一定是null。

但是，对于更复杂的代码，如果不使用调试器，则不可能确定哪个变量是null。假设在这段代码中出现了一个NullPointerException：
```java
a.b.c.i = 99;
```
我们根本无法确定到底是a还是b或者是c在运行时是一个null值。

但是，在JDK 14以后，这种问题就有解了。

在JDK 14中，当在运行期试图对一个null对象进行引用时，JVM依然会抛出一个NullPointerException，除此之外，还会通过分析程序的字节码指令，精确地确定哪个变量是null，并且在堆栈信息中明确地提示出来。

在JDK 14中，如果上面代码中的a.i = 99发生NullPointerException，将会打印如下堆栈：
```
Exception in thread "main" java.lang.NullPointerException:
Cannot assign field "i" because "a" is null
at Prog.main(Prog.java:5)
```
如果是a.b.c.i = 99中的b为null导致了空指针，则会打印以下堆栈信息：
```
Exception in thread "main" java.lang.NullPointerException:
Cannot read field "c" because "a.b" is null
at Prog.main(Prog.java:5)
```
堆栈中明确指出了到底是哪个对象为null而导致了NullPointerException，一旦应用中发生NullPointerException，开发者可以通过堆栈信息第一时间确定到底是代码中的哪个对象为null的问题。

这算是JDK的一个小的改进，但这个改进对于开发者来说确实是非常友好的。

### 23.9 Java 14：record类型
2020年3月17日，Java正式发布了JDK 14。在JDK 14中，共有16个新特性，本节主要来介绍其中的一个特性：JEP 359: Records。

这一特性在Java 14和Java 15中作为预览特性引入，并在JDK 16中成为一个永久特性。它提供了一种紧凑的语法来声明类，这些类是浅层不可变数据的透明持有者。这将大大简化这些类，并提高代码的可读性和可维护性。

1. **官方“吐槽”最为致命**

早在2019年2月份，Java语言架构师Brian Goetz曾经写过一篇文章，详尽地说明了并“吐槽”了Java语言，他和很多程序员一样抱怨“Java太啰唆”或有太多的“繁文缛节”。他提到：开发人员想要创建纯数据载体类（Plain Data Carriers）通常都必须编写大量低价值、重复的、容易出错的代码。比如构造函数、getter/setter、equals()、hashCode()及toString()等。以至于很多人选择使用IDE的功能来自动生成这些代码。还有一些开发人员会选择使用一些第三方类库，如Lombok等来生成这些方法，从而导致了令人吃惊的表现（Surprising Behavior）和糟糕的可调试性（Poor Debuggability）。


那么，Brian Goetz提到的纯数据载体到底指的是什么呢？他举了一个简单的例子：
```java
final class Point {
    public final int x;
    public final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    // state-based implementations of equals, hashCode, toString
    // nothing else
}
```
其中的Point其实就是一个纯数据载体，它表示一个“点”中包含x坐标和y坐标，并且只提供了构造函数，以及一些equals和hashCode等方法。

于是，Brian Goetz提出一种想法：Java完全可以通过另外一种方式表示这种纯数据载体。

其实在其他的面向对象语言中，早就针对这种纯数据载体有单独的定义了，如Scala中的case、Kotlin中的data及C#中的record。这些定义尽管在语义上有所不同，但它们的共同点是类的部分或全部状态可以直接在类头中描述，并且这个类中只包含了纯数据。

于是，Brian Goetz提出Java中是不是也可以通过如下方式定义一个纯数据载体呢？
```java
record Point(int x, int y) {}
```

2. **神”说要用record，于是就有了**

就像Brian Goetz“吐槽”的那样，我们通常需要编写大量代码才能使类变得有用。比如以下内容：
 - toString()方法。
 - hashCode()和equals()方法。
 - Getter方法。
 - 一个共有的构造函数。

对于简单的类，这些方法通常是无聊的、重复的，而且可以很容易、机械地生成的这些代码（IDE通常提供了这种功能）。

当你阅读别人的代码时，可能会更加头大。例如，别人可能使用IDE生成的hashCode()和equals()来处理类的所有字段，但如何才能在不检查每一行代码的情况下确定他写得对呢？如果在重构过程中添加了字段而没有重新生成方法，则会发生什么情况呢？

Brian Goetz提出使用record定义一个纯数据载体的想法，于是，Java 14中便包含了一个新特性JEP 359: Records，作者正是Brian Goetz。

Records的目标是扩展Java语言的语法，Records为声明类提供了一种紧凑的语法，用于创建一种类中是“字段，只是字段，除了字段什么都没有”的类。通过对类做这样的声明，编译器可以自动创建所有方法并让所有字段参与hashCode()等方法。

3. **一言不合反编译**

Records的用法比较简单，和定义Java类一样：
```java
record Person (String firstName, String lastName) {}
```
如上我们定义了一个Person记录，其中包含两个组件firstName和lastName，以及一个空的类体。

这个语法看上去也是个语法糖，它到底是怎么实现的呢？

我们先尝试对其进行编译，记得使用--enable-preview参数，因为Records功能目前在JDK 14中还是一个预览（Preview）功能。
```
> javac --enable-preview --release 14 Person.java
Note: Person.java uses preview language features.
Note: Recompile with -Xlint:preview for details.
```
如上所述，Record只是一个类，其目的是保存和公开数据。使用javap对其进行反编译，将得到以下代码：
```java
public final class Person extends java.lang.Record {
    private final String firstName;
    private final String lastName;
    public Person(java.lang.String, java.lang.String);
    public java.lang.String toString();
    public final int hashCode();
    public final boolean equals(java.lang.Object);
    public java.lang.String firstName();
    public java.lang.String lastName();
}
```
通过反编译得到的类，我们可以得到以下信息：
 - 生成了一个final类型的Person类，说明这个类不能再有子类了。
 - 这个类继承了java.lang.Record类，和我们使用enum创建的枚举都默认继承java.lang.Enum有点类似。
 - 类中有两个private final类型的属性。所以，record定义的类中的属性都应该是private final类型的。
 - 一个public的构造函数，入参就是两个主要的属性。如果通过字节码查看其方法体，那么其内容就是以下代码。
```java
public Person(String firstName, String lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
}
```
 - 有两个getter方法，分别叫作firstName和lastName。这和JavaBean中定义的命名方式有区别，或许Brian Goetz想通过这种方式告诉我们record定义出来的并不是一个JavaBean。
 - 还帮助我们自动生成了toString()、hashCode()和equals()方法。值得一提的是，这三个方法依赖invokedynamic来动态调用包含隐式实现的适当方法。

4. **还可以这样玩**

在前面的例子中，我们简单地创建了一个record，record中还能有其他的成员变量和方法吗？

 - 我们不能将实例字段添加到record中。但是，我们可以添加静态字段。
```java
record Person (String firstName, String lastName) {
    static int x;
}
```
 - 我们可以定义静态方法和实例方法，还可以操作对象的状态。
```java
record Person (String firstName, String lastName) {
    static int x;

    public static void doX(){
        x++;
    }

    public String getFullName(){
        return firstName + " " + lastName;
    }
}
```
 - 我们还可以添加构造函数。
```java
record Person (String firstName, String lastName) {
    static int x;

    public Person{
        if(firstName == null){
            throw new IllegalArgumentException( "firstName can not be null !");
        }
    }

    public Person(String fullName){
        this(fullName.split(" ")[0],this(fullName.split(" ")[1])
    }
}
```

所以，我们是可以在record中添加静态字段/方法的，但问题是，我们应该这么做吗？

请记住，record的目标是使开发人员能够将相关字段作为单个不可变数据项组合在一起，而不需要编写冗长的代码。这意味着，每当我们想要向记录添加更多的字段/方法时，请考虑是否应该使用完整的class来代替record。

**小结**

record解决了使用类作为数据包装器的一个常见问题。纯数据类从几行代码显著地简化为一行代码。

目前record是一种预览语言特性，这意味着，尽管它已经完全实现，但在JDK中还没有标准化。

### 23.10 Java 15：封闭类
Java SE 15的发布引入了封闭类（JEP 360）作为预览特性。

这个特性是关于在Java中启用更细粒度的继承控制。类或接口现在可以定义哪些类可以实现或扩展它。对于域建模和提高库的安全性来说，这是一个非常有用的特性。

我们知道，Java中一个很重要的特性就是通过继承来复用类，在Java 15之前，Java认为代码重用始终是一个终极目标，所以，一个类和接口都可以被任意的类实现或继承。

但是，在很多场景中，这样做是容易造成错误的，而且也不符合物理世界的真实规律。

例如，假设一个业务领域只适用于汽车和卡车，而不适用于摩托车。在Java中创建Vehicle抽象类时，应该只允许Car和Truck类扩展它。通过这种方式，我们希望确保在域内不会出现误用Vehicle抽象类的情况。

为了解决类似的问题，在Java 15中引入了一个新的特性——封闭。封闭特性在Java中引入了新的关键字：sealed、non - sealed和permits。

1. **密闭接口**

想要定义一个密闭接口，可以将sealed修饰符应用到接口的声明中。然后，permit子句指定允许实现密闭接口的类：
```java
public sealed interface Service permits Car, Truck {
}
```

以上代码定义了一个密闭接口Service，它规定只能被Car和Truck两个类实现。

2. **密闭类**

与接口类似，我们可以通过使用相同的sealed修饰符来定义密闭类：
```java
public abstract sealed class Vehicle permits Car, Truck {
}
```
通过密闭特性，我们定义出来的Vehicle类只能被Car和Truck继承。当然，被许可的类Car和Truck既可以定义为密闭类，也可以定义为非密闭类，还可以定义为final类型：
```java
public sealed class Car extends Vehicle implements Service {
}
public non-sealed class Car extends Vehicle implements Service {
}
public final class Truck extends Vehicle implements Service {
}
```
需要注意的是，在定义一个封闭类的子类时，这个类必须声明为final、sealed或non - sealed中的一种。

### 23.11 Java 16：instanceof模式匹配
本节介绍的一个特性是Pattern Matching for instanceof，即针对instanceof的模式匹配，其最早在Java 14中作为预览特性引入，在Java 16中成为

### 23.11 Java 16：instanceof模式匹配
本节介绍的一个特性是Pattern Matching for instanceof，即针对instanceof的模式匹配，其最早在Java 14中作为预览特性引入，在Java 16中成为一个永久特性。

instanceof是Java中的一个关键字，我们在对类型做强制转换之前，会使用instanceof做一次判断，例如：
```java
if (animal instanceof Cat) {
    Cat cat = (Cat) animal;
    cat.miaow();
} else if (animal instanceof Dog) {
    Dog dog = (Dog) animal;
    dog.bark();
}
```
在这个例子中，每一个判断逻辑都是对animal的一次条件判断，以确定它的类型。然后强制转换它，并声明一个局部变量，我们就可以针对特定的动物进行特定的操作了。

尽管这种方法有效，但它也有很多缺点，例如：
 - 代码比较烦琐，我们需要写很多个if-else的分支判断语句。
 - 代码可读性很差，因为强制类型转换和变量提取是代码的主要部分。
 - 代码并不稳定，当我们想要新增一个动物类型时需要改动代码。

Java 14通过JEP 305带来了改进版的instanceof操作符，该操作符既测试参数，又将其赋值给适当类型的绑定变量。

这意味着我们可以用更简洁的方式写出之前的代码例子：
```java
if (animal instanceof Cat cat) {
    cat.miaow();
} else if(animal instanceof Dog dog) {
    dog.bark();
}
```
在第一个if块中，我们测试动物变量，查看它是否为Cat的一个实例。如果是，那么它将被转换为Cat类型，最后将结果赋值给cat。

需要注意的是，变量cat和dog仅在作用域内，并在各自的模式匹配表达式返回true时才会被赋值。因此，如果我们试图在另一个位置使用任何一个变量，则代码将生成编译器错误。

不管怎样，我们都不难发现这种写法大大简化了代码，省略了显式强制类型转换的过程，可读性也大大提高了。

### 23.12 Java 17：switch模式匹配
基于instanceof模式匹配这个特性，我们可以使用如下方式来对对象o进行处理：
```java
static String formatter(Object o) {
    String formatted = "unknown";
    if (o instanceof Integer i) {
        formatted = String.format("int %d", i);
    } else if (o instanceof Long l) {
        formatted = String.format("long %d", l);
    } else if (o instanceof Double d) {
        formatted = String.format("double %f", d);
    } else if (o instanceof String s) {
        formatted = String.format("String %s", s);
    }
    return formatted;
}
```
可以看到，这里使用了很多if-else，其实，Java给我们提供了一个多路比较的工具，那就是switch，而且从Java 14开始支持switch表达式，但switch的功能一直都是非常有限的。switch只能操作部分类型，如数字类型、枚举类型和String等，并且只能用来判断一个值是否与一个常量精确相等。

这个问题在Java 17中得到了解决，Java的工程师们扩展了switch语句和表达式，使其可以适用于任何类型，并允许case标签中不仅带有变量，还带有模式匹配。我们就可以更清楚、更可靠地重写上述代码，例如：
```java
static String formatterPatternSwitch(Object o) {
    return switch (o) {
        case Integer i -> String.format("int %d", i);
        case Long l -> String.format("long %d", l);
        case Double d -> String.format("double %f", d);
        case String s -> String.format("String %s", s);
        default -> o.toString();
    };
}
```
可以看到，以上的switch处理的是一个Object类型，而且case中也不再是精确的值匹配，而是模式匹配了。

1. **模式匹配与null**

在以前，如果switch表达式的值为null，那么switch语句和表达式会抛出NullPointerException，因此我们通常必须在switch之外进行null测试：
```java
static void testFooBar(String s) {
    if (s == null) {
        System.out.println("oops!");
        return;
    }
    switch (s) {
        case "Foo", "Bar" -> System.out.println("Great");
        default -> System.out.println("Ok");
    }
}
```
当switch只支持少数引用类型时，这是合理的。如果switch允许任何类型，并且case标签可以有类型模式，那么独立的非空检测就显得很臃肿了，这时最好将null检测集成到switch中：
```java
static void testFooBar(String s) {
    switch (s) {
        case null -> System.out.println("Oops");
        case "Foo", "Bar" -> System.out.println("Great");
        default -> System.out.println("Ok");
    }
}
```
如果我们希望以与另一个case标签相同的方式处理null时，则可以使用如下方式：
```java
static void testStringOrNull(Object o) {
    switch (o) {
        case null, String s -> System.out.println("String: " + s);
    }
}
```
2. **精炼写法**

假设我们通过类似以下的用法来使用switch： （此处文档未完整展示相关代码示例）

```java
class Shape {}
class Rectangle extends Shape {}
class Triangle extends Shape {
    int calculateArea() { ... }
}

static void testTriangle(Shape s) {
    switch (s) {
        case null:
            break;
        case Triangle t:
            if (t.calculateArea() > 100) {
                System.out.println("Large triangle");
            }
            break;
        default:
            System.out.println("A shape, possibly a small triangle");
    }
}
```
这里，我们遇到的场景是通过多个组合条件来处理Shape的对象，所以使用了switch+if的组合，把 “当一个三角形的面积大于100时” 作为一种特殊情况。

在Java 17中，提供了新的支持可以简化上面的写法，Java 17提供了一种新的模式，称为保护模式，即p && b，它允许使用任意布尔表达式b对模式p进行优化。简化后的写法如下：
```java
static void testTriangle(Shape s) {
    switch (s) {
        case Triangle t && (t.calculateArea() > 100) ->
            System.out.println("Large triangle");
        default ->
            System.out.println("A shape, possibly a small triangle");
    }
}
```
我们在第一个case后面使用了Triangle t && (t.calculateArea() > 100)这样的写法，通过这种方式，我们减少了一个if块的编写。

在Java 17中，switch的功能更加强大了。 
