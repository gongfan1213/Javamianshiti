### 22.6 Lombok
Lombok是一款非常实用Java工具，用来帮助开发人员消除Java的冗长代码，尤其是对于简单的Java对象（POJO），它通过注释实现这一目的。

如果想在项目中使用Lombok，则需要执行以下三个步骤。
（1）在IDE中安装Lombok插件。
目前Lombok支持多种IDE，包括主流的Eclipse、IntelliJ IDEA和MyEclipse等。

在IntelliJ IDEA中安装Lombok插件的方式如图22 - 4所示。
```
• Go to File > Settings > Plugins
• Click on Browse repositories...
• Search for Lombok Plugin
• Click on Install plugin
• Restart IntelliJ IDEA
```

（2）导入相关依赖。
Lombok支持使用多重构建工具导入依赖，目前主要支持Maven、Gradle和Ant等。

使用Maven的导入方式如下：
```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.12</version>
    <scope>provided</scope>
</dependency>
```

（3）在代码中使用注解。
Lombok精简代码的方式主要是通过注解来实现的，其中常用的有@Data、@Getter/@Setter、@Builder和@NonNull等。

例如，使用@Data注解即可简单地定义一个JavaBean：
```java
import lombok.Data;
@Data
public class Menu {
    private String shopId;
    private String skuMenuId;
    private String skuName;
}
```

在类上使用@Data注解，相当于同时使用了@ToString、@EqualsAndHashCode、@Getter、@Setter和@RequiredArgsConstructor这些注解，对于POJO类十分有用，即自动给例子中的Menu类中定义了toString、Getter和Setter等方法。

通过上面的例子可以发现，使用@Data注解大大减少了代码量，这也是很多开发者热衷于使用Lombok的主要原因。

但Lombok也并不是完全没有缺点，关于要不要使用Lombok在业内也一直是一个争论的焦点，为什么有人不建议使用Lombok呢？使用Lombok会带来哪些问题呢？

#### 使用Lombok有什么坏处？
1. **侵入性高**
使用Lombok时要求开发者一定要在IDE中安装对应的插件。

如果未安装对应的插件，那么使用IDE打开一个基于Lombok的项目会提示找不到方法等错误，导致项目编译失败。

也就是说，如果项目组中有一个人使用了Lombok，那么其他人也要安装IDE插件，否则无法进行协同开发。

更重要的是，如果我们定义的一个jar包中使用了Lombok，那么就要求所有依赖这个jar包的应用都必须安装对应的插件，对于代码的侵入性是很高的。
2. **代码可读性和可调试性低**
在代码中使用Lombok确实可以减少很多代码，因为Lombok会自动生成很多代码，但这些代码在编译阶段才会生成，所以在开发的过程中，很多代码其实是缺失的。

在代码中大量使用Lombok会降低代码的可读性，而且给代码调试带来一定的问题。

比如，如果我们想要知道某个类中的某个属性的getter方法都被哪些类引用了，就没那么简单了。
3. **有“坑”**
因为Lombok使代码开发非常简便，这就使得部分开发者对其产生过度的依赖。

在使用Lombok的过程中，如果不理解各种注解的底层原理，那么很容易产生意想不到的结果。

举一个简单的例子，当我们使用@Data定义一个类时，类在编译时会自动生成equals()方法。

如果只使用了@Data，而不使用@EqualsAndHashCode(callSuper=true)，那么类在编译后，则会默认设置成@EqualsAndHashCode(callSuper=false)，这时生成的equals()方法只会比较子类的属性，不会考虑从父类继承的属性，无论父类属性访问权限是否开放。

这样就可能得到意想不到的结果。
4. **影响JDK的升级**
因为Lombok对于代码有很强的侵入性，所以可能带来一个比较大的问题，那就是会影响JDK的升级。

按照如今JDK的升级频率，每半年都会推出一个新的版本，但Lombok作为一个第三方工具，并且是由开源团队维护的，它的迭代速度是无法保证的。

所以，如果我们需要升级到某个新版本的JDK的时候，若其中的特性在Lombok中不支持，那么升级就会受到影响。

还有一个使用Lombok可能带来的问题，就是Lombok自身的升级也会受到限制。

因为一个应用可能依赖了多个jar包，而每个jar包可能又要依赖不同版本的Lombok，这就导致在应用中需要做版本仲裁，而我们知道，jar包的版本仲裁是不容易的，而且发生问题的概率也很高。

笔者遇到过一种特殊的场景，有一次将IntelliJ IDEA升级到2020版，但新版本对应的Lombok插件还没开发出来，导致无法正常使用新的IDEA，只能被迫退回到旧版本了。
5. **破坏封装性**
众所周知，Java的三大特性包括封装性、继承性和多态性。

如果我们在代码中直接使用Lombok，那么它会自动生成getter和setter等方法，这就意味着，一个类中的所有参数都自动提供了设置和读取的方法。

举个简单的例子，我们定义一个购物车类：
```java
@Data
public class ShoppingCart {
    // 商品数目
    private int itemsCount;
    // 总价格
    private double totalPrice;
    // 商品明细
    private List<Item> items = new ArrayList<>();
}
```

购物车中的商品数目、商品明细和总价格三者其实是有关联关系的，如果需要修改，则要一起修改。

笔者曾见过很多人为了使用方便，会直接使用Lombok的@Data注解，使得itemsCount和totalPrice这两个属性被自动提供public的getter和setter方法。

外部可以通过setter方法随意地修改这两个属性的值。我们可以随意调用setter方法重新设置itemsCount和totalPrice属性的值，这也会导致这两个字段和items属性的值不一致。

而面向对象封装的定义是：通过访问权限控制隐藏内部数据，外部仅能通过类提供的有限的接口访问和修改内部数据。所以，暴露不应该暴露的setter方法，明显违反了面向对象的封装特性。

更好的做法应该是不提供getter/setter，而是只提供一个public的addItem方法，同时修改itemsCount、totalPrice和items三个属性。

所以，我们在使用Lombok时，不建议直接使用@Data注解，而是使用@Getter、@Setter和@ToString等，在需要的地方使用正确的注解。

关于这一点，其实在Java 14中提供了一种新的Records类型来专门定义纯数据载体类型，这种类型的内部也会自动生成构造函数、getter/setter、equals()、hashCode()及toString()等方法。

### 小结
本节总结了常用的Java开发工具Lombok的优缺点。

优点是使用注解即可自动生成代码，大大减少了代码量，使代码非常简洁。

缺点是在使用Lombok的过程中，还可能存在对队友不友好、对代码不友好、对调试不友好、对升级不友好、破坏封装性等问题。 
