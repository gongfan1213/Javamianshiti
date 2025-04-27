### 第3章 Java对象
#### 3.1 Object类

前面介绍了Java中的继承机制，其实Java中的所有类都默认继承自一个类，那就是java.lang.Object类，这个类是一切Java类的“祖先”，是所有类的超类，即使在写代码时没有明确指定，也会默认继承这个类。

Object类中定义以下了几个方法：

- clone()：创建并返回此对象的副本。

- equals(Object obj)：指示其他对象是否“等于”此对象。

- hashCode()：返回对象的哈希码值。

- notify()：唤醒正在等待这个对象的监视器的单个线程。

- notifyAll()：唤醒正在等待这个对象的监视器的所有线程。

- toString()：返回对象的字符串表示形式。

- wait()：使当前线程进入等待状态，直到另一个线程调用此对象的notify()方法或notifyAll()方法。

- finalize()：当垃圾回收器决定回收某对象时，就会运行该对象的finalize()方法。


#### 3.2 JavaBean

在Java中定义一个类时，我们通常会定义一些属性和方法，这个类中会包含若干private的属性和public的方法。例如：

```java
public class User {
    private String name;
    public String getName() { return this.name; }
    public void setName(String name) { this.name = name; }
}
```

以上代码定义了一个name属性，并提供了两个方法，用于访问这个属性，其中一个是读方法getName，另一个是写方法setName。

我们通常将setName这种方法称为setter，将getName这种方法称为getter。作为开发者，我们可以任意形式给这些方法命名，但是在一般情况下，我们都会遵守一个规范，根据这个规范来创建类。


这个规范就是官方提供的“JavaBeans Specification”，规范中定义了一个叫作JavaBean的特殊的类，或者我们可以把JavaBean称为一种标准，只要符合这个标准定义出来的类都叫JavaBean。

这个标准主要有以下几个条件：

- 所有属性都是private的。

- 提供默认的构造函数。

- 提供一系列setter和getter方法。

- 实现Serializable接口。

其中关于私有属性及构造方法，我们在前面的章节中介绍过，这里就不展开介绍了，关于Serializable接口，我们在后面介绍序列化技术的时候再展开介绍，这里着重介绍setter和getter方法。

1. **getter和setter**

setter和getter的命名方式应该遵循JavaBean的命名约定，如getXxx()和setXxx()，其中Xxx是变量的名称。

根据JavaBeans Specification规定，如果是普通的参数propertyName，则要以以下方式定义其setter/getter：

```java
public <PropertyType> get<PropertyName>();
public void set<PropertyName>(<PropertyType> a);
```

但是，布尔类型的变量propertyName是单独定义的：

```java
public boolean is<PropertyName>();
public void set<PropertyName>(boolean m);
```

2. **success和isSuccess**

在日常开发中，我们会经常在类中定义布尔类型的变量，比如在给外部系统提供一个RPC接口时，一般会定义一个字段来表示本次请求是否成功。

关于“本次请求是否成功”的字段的定义，不同的开发者定义的方式都不同，大致有以下四种：

```java
boolean success
boolean isSuccess
Boolean success
Boolean isSuccess
```

通过观察我们可以发现，前两种和后两种定义方式的主要区别是变量的类型不同，前者使用的是boolean，后者使用的是Boolean。关于boolean和Boolean的区别及选择，我们在第8章自动拆装箱中展开介绍。

这里主要看一下变量命名时在success和isSuccess之间该如何选择。

从语义方面来讲，两种命名方式都可以讲得通，并且也都没有歧义。那么还有什么原则可以参考来让我们做选择呢？根据前面介绍的JavaBean的getter命名规范，success方法的getter应该是isSuccess/getSuccess，而isSuccess的getter应该是isIsSuccess/getIsSuccess。

但是，很多人在使用isSuccess作为属性名时，还是会采用isSuccess/getSuccess作为getter方法名，尤其是现在的很多IDE在默认生成getter时也会生成isSuccess。

一般情况下，这么做其实是没有影响的。但是，在一种特殊情况下就会有问题，那就是发生序列化时可能导致参数转换异常。因为这里涉及序列化的知识，所以我们在第12章中介绍这个异常的详细内容，这里先给读者抛出一个结论，在定义一个布尔类型的JavaBean时，应该使用success这样的命名方式，而不是isSuccess。


#### 3.3 equals和hashCode的关系

我们知道，Java中的每个类都隐式地继承自Java.lang.Object类，并且会继承equals()和hashcode()两个方法。

在没有重写这两个方法的情况下，equals()的默认实现与“==”操作符一致，即如果引用变量指向同一对象，则返回true。例如：

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```
测试代码如下：
```java
public class EqualsTest {
    public static void main(String[] args) {
        Person person1 = new Person("Hollis");
        Person person2 = new Person("Hollis");
        Person person3 = person1;
        System.out.println("person1 == person2? " + (person1.equals(person2)));
        System.out.println("person1 == person3? " + (person1.equals(person3)));
    }
}
class Person{
    private String name;
    public Person(String name) {
        this.name = name;
    }
}
```
以上代码的输出结果为：
```
person1 == person2? false
person1 == person3? true
```
在没有重写equals方法的情况下，使用equals做比较，判断的是两个对象的引用是否相等。

如果想判断两个对象的内容是否相等，则需要重写equals()和hashcode()方法。例如：
```java
public class EqualsTest {
    public static void main(String[] args) {
        Person person1 = new Person("Hollis");
        Person person2 = new Person("Hollis");
        Person person3 = person1;
        System.out.println("person1 == person2? " + (person1.equals(person2)));
        System.out.println("person1 == person3? " + (person1.equals(person3)));
    }
}
class Person{
    private String name;
    public Person(String name) {
        this.name = name;
    }
    @Override
    public boolean equals(Object o) {
        if (this == o) { return true; }
        if (o == null || getClass()!= o.getClass()) { return false; }
        Person person = (Person)o;
        return Objects.equals(name, person.name);
    }
}
```
以上代码的输出结果为：
```
person1 == person2? true
person1 == person3? true
```
因为我们重写了Person类的equals方法，比较其中的name值是否一致。

#### 3.3.1 equals的多种写法

其实，在实现equals方法时，有很多种方式。我们还以Person类为例，使用多种方式实现equals方法。

- **第一种，Intellij IDEA中默认的实现方式**：
```java
public boolean equals(Object o) {
    if (this == o) { return true; }
    if (o == null || getClass()!= o.getClass()) { return false; }
    Person person = (Person)o;
    return name!= null? name.equals(person.name) : person.name == null;
}
```
- **第二种，基于java.util.Objects#equals，也就是前面我们介绍过的实现方式**。
- **第三种，基于Apache Commons Lang框架的实现方式**：
```java
public boolean equals(Object o) {
    if (this == o) { return true; }
    if (o == null || getClass()!= o.getClass()) { return false; }
    Person person = (Person)o;
    return new EqualsBuilder()
      .append(name, person.name)
      .isEquals();
}
```
- **第四种，基于Guava框架的实现方式**：
```java
@Override
public boolean equals(Object o) {
    if (this == o) { return true; }
    if (o == null || getClass()!= o.getClass()) { return false; }
    Person person = (Person)o;
    return Objects.equal(name, person.name);
}
```
介绍以上这几种方式，不是想浪费文字介绍“茴字的4种写法”，主要有两方面的原因，第一是希望读者可以通过不同的思想方式，找到它们的共同点，即先使用“==”判断对象是不是同一个，再判断是不是同一种类型，最后再比较值。第二个目的是希望读者可以善于使用各类开源工具类来实现一些常用的方法和功能。一些提升代码效率和质量的工具类会在第22章中展开介绍。


#### 3.3.2 equals和hashCode

hashCode()方法也是Object类中定义的方法，作用是返回对象的哈希值，返回值类型是int。这个哈希值的作用是确定该对象在哈希表中的位置。哈希算法与哈希表将在第10章集合类中展开介绍。

Joshua Bloch在Effective Java中对hashCode有这样的描述：你必须在每个重写equals()的类中重写hashCode()。如果不这样做，那么将违反Object.hashCode()的一般约定，这将阻止类与所有基于散列的集合（包括HashMap、HashSet和Hashtable）一起正常工作。

其实要理解这个说法并不难，在HashMap和HashSet等基于散列的集合中，会使用对象的hashCode值来确定该对象应该如何存储在集合中，并且再次使用hashCode来在其集合中定位对象。

如果有一个类，只重写了equals方法，而没有重写hashCode方法，那么会发生什么问题呢？例如：

```java
public class EqualsTest {
    public static void main(String[] args) {
        Person person1 = new Person("Hollis");
        Person person2 = new Person("Hollis");
        HashSet<Person> set = new HashSet<>();
        set.add(person1);
        set.add(person2);
        System.out.println(set.size());
    }
}
class Person {
    private String name;
    public Person(String name) {
        this.name = name;
    }
    @Override
    public boolean equals(Object o) {
        if (this == o) { return true; }
        if (o == null || getClass()!= o.getClass()) { return false; }
        Person person = (Person)o;
        return Objects.equals(name, person.name);
    }
}
```

以上代码定义了一个Person类，只重写了equals方法，并没有重写hashCode方法，然后定义了两个值内容一样的对象，尝试把它们放入一个不能重复的Set，最后输出这个Set的元素个数是2，说明Set认为这两个对象不是相等的。

也就是说，两个对象相等的严格定义是：对象内容相等（equals()的结果），并且哈希值也要相等（hashCode的结果 ）。

我们给Person类添加一个hashCode方法，重新执行以上方法，最终得到的结果就是1了。

```java
class Person {
    private String name;
    public Person(String name) {
        this.name = name;
    }
    @Override
    public boolean equals(Object o) {
        if (this == o) { return true; }
        if (o == null || getClass()!= o.getClass()) { return false; }
        Person person = (Person)o;
        return Objects.equals(name, person.name);
    }
    @Override
    public int hashCode() {
        return Objects.hash(name);
    }
}
```
所以，我们在重写equals方法时，一定要同时重写hashCode方法。 


### 3.4 对象的clone方法

Java.lang.Object类中还有一个clone方法，用于复制一个新的对象。这个方法在不重写的情况下，其实是浅拷贝（Shadow Clone）的。



#### 浅拷贝与深拷贝

- **浅拷贝（Shadow Clone）**：对基本数据类型进行值传递，对引用数据类型进行引用传递的拷贝，此为浅拷贝，如图3-1所示。

- **深拷贝（Deep Clone）**：对基本数据类型进行值传递，为数据类型创建一个新的对象，并复制其内容，此为深拷贝，如图3-2所示。


![image](https://github.com/user-attachments/assets/31c835d8-4284-43c0-a735-7cd8660ce569)


下面通过一段示例代码展示默认情况下的clone方法的浅拷贝的现象：
```java
public class Person implements Cloneable {
    public static void main(String[] args) throws CloneNotSupportedException {
        Address address = new Address();
        address.setProvince("ZheJiang");

        Person person1 = new Person("Hollis", address);
        Person person2 = (Person)person1.clone();

        person2.getAddress().setProvince("JiangSu");

        System.out.println(person1);
        System.out.println(person2);
    }

    private String name;
    private Address address;

    public Address getAddress() {
        return address;
    }

    public void setAddress(Address address) {
        this.address = address;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Person(String name, Address address) {
        this.name = name;
        this.address = address;
    }

    @Override
    public String toString() {
        return new StringJoiner(", ", Person.class.getSimpleName() + "[", "]")
              .add("name='" + name + "'")
              .add("address=" + address)
              .toString();
    }
}

class Address {
    private String province;

    public String getProvince() {
        return province;
    }

    public void setProvince(String province) {
        this.province = province;
    }

    @Override
    public String toString() {
        return new StringJoiner(", ", Address.class.getSimpleName() + "[", "]")
              .add("province='" + province + "'")
              .toString();
    }
}
```

我们定义了一个Person类并实现Cloneable接口，这个类有两个属性，分别是String类型的name和Address类型的address，Address是一个类，它包含了一个String类型的province属性。

我们创建一个Person类的对象person1，然后使用clone方法复制一个新的对象并把它赋值给person2，只修改person2的address属性的province的值。最后打印出结果，发现person1和person2的值都发生了改变：
```
Person[name='Hollis', address=Address[province='JiangSu']]
Person[name='Hollis', address=Address[province='JiangSu']]
```
以上这种现象就是浅拷贝，那么如何实现深拷贝呢？最简单、直观的办法就是重写clone方法。修改上述代码，重写clone方法：
```java
public class Person implements Cloneable {
    public static void main(String[] args) throws CloneNotSupportedException {
        Address address = new Address();
        address.setProvince("ZheJiang");
        Person person1 = new Person("Hollis", address);
        Person person2 = (Person)person1.clone();

        person2.getAddress().setProvince("JiangSu");

        System.out.println(person1);
        System.out.println(person2);
    }

    private String name;
    private Address address;

    public Address getAddress() {
        return address;
    }

    public void setAddress(Address address) {
        this.address = address;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Person(String name, Address address) {
        this.name = name;
        this.address = address;
    }

    @Override
    public String toString() {
        return new StringJoiner(", ", Person.class.getSimpleName() + "[", "]")
              .add("name='" + name + "'")
              .add("address=" + address)
              .toString();
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        Person person = (Person)super.clone();
        person.setAddress((Address)address.clone());
        return person;
    }
}

class Address implements Cloneable {
    private String province;

    public String getProvince() {
        return province;
    }

    public void setProvince(String province) {
        this.province = province;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

    @Override
    public String toString() {
        return new StringJoiner(", ", Address.class.getSimpleName() + "[", "]")
              .add("province='" + province + "'")
              .toString();
    }
}
```
在重写Person类和Address类的clone方法之后，得到的结果如下：
```
Person[name='Hollis', address=Address[province='ZheJiang']]
Person[name='Hollis', address=Address[province='JiangSu']]
```
可以发现，对“clone”出来的新对象的修改并没有影响原有的对象，这就是实现了深拷贝。
关于深拷贝，还有另外一种实现方式，那就是使用序列化技术，在第12章序列化的部分再展开介绍。 

