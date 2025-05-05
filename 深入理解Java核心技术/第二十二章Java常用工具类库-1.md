### 第22章 常用的Java工具库
#### 22.1 Apache Commons
Apache Commons是Apache软件基金会的一个开源项目，曾隶属于Jakarta项目。Commons的目的是提供可重用的、开源的Java代码。Commons由三部分组成：Proper（一些已发布的项目）、Sandbox（一些正在开发的项目）和Dormant（一些刚启动或者已经停止维护的项目）。

Apache Commons Proper致力于一个主要目标：创建和维护可重用的Java组件。截至2021年7月份，Proper中共有43个重要组件。常用的有BeanUtils、Collections、IO、Lang、Logging和OGNL。

1. **Apache Commons Lang**

Apache提供的Commons Lang主要是对Java中的java.lang包的补充，提供了更多的通用方法来操作Java中的一些核心类库。


需要特别注意的是，Apache Commons Lang目前有两个主要的版本，分别是org.apache.commons.lang3与org.apache.commons.lang，它们当中的大部分方法是互相兼容的，而且在代码中可以同时使用这两个类库。

之所以出现Apache Commons Lang，主要是为了提供一些Java中原本没有的工具类库，方便开发者使用。随着Java的迭代升级，尤其是Java 5的推出，使得部分功能在Java中已经天然被支持了。所以Apache就对Commons Lang进行了一次比较大的改进，主要是删除了一些不受欢迎、功能薄弱及不必要的部分。因为涉及代码的删除，也就意味着这个版本不再是向前兼容的了。

为了让开发者可以同时使用新版和旧版的Lang，官方更改了包名，允许Lang 3.0与之前的Lang版本同时使用。

其实我们在使用Apache Commons Lang类库时，可以优先选择使用Lang 3，如果一些功能在Lang 3（org.apache.commons.lang3）中没有，那么再考虑使用Lang（org.apache.commons.lang）。

Commons Lang中提供了很多好用的工具类，比如用于处理字符串的StringUtils、用于处理数组的ArrayUtils、用于处理时间的DateUtils。
 - **StringUtils中的常用方法如下**：
    - isBlank()：检查一个字符串是否为空白、null或者空字符串（""）。
    - isNotBlank()：检查一个字符串是否不为null或者空字符串（""）。
    - isEmpty()：检查一个字符串是否为null或者空字符串（""）。
    - isNotEmpty()：检查一个字符串是否不为null或者空字符串（""）。
    - equals()：判断两个字符串的内容是否相等。
    - split()：将字符串分割为一个数组。
    - trimToNull()：从这个字符串的两端移除控制字符，如果字符串在修剪后为空（""）或者是null，则返回null。
 - **ArrayUtils中的常用方法如下**：
    - contains()：检查给定值是否在给定数组中。
    - addAll()：将给定数组的所有元素添加到一个新数组中。
    - clone()：复制一个数组。
    - isEmpty()：判断一个数组是否为空。
    - add()：复制给定数组并在新数组的末尾添加给定元素。
    - indexOf()：在数组中查找给定值的索引。
    - toObject()：将数组转换为对象。
 - **DateUtils中的常用方法如下**：
    - isSameDay：判断两个日期是不是同一天。
    - addDays：在日期的基础上增加指定天数。
    - addYears：在日期的基础上增加指定年数。
    - setHours：设置时间的小时字段。
    - Truncate：截断日期，将指定的字段保留为最重要的字段，如2021-10-01 11:20:22，按照DATE进行截断得到2021-10-01 00:00:00。
    - truncatedCompareTo：对多个日期先进行截断之后再比较它们。

Apache Commons Lang是对java.lang包的增强，所以在对java.lang包中的类进行操作时，可以优先考虑使用Apache Commons Lang。

2. **Apache Commons IO**

Apache Commons IO是一个实用程序库，用于帮助程序员开发I/O功能，主要包括以下几个方面：

    - Io：这个包定义了用于处理流、读取器、写入器和文件的实用工具类。
    - Comparator：这个包为文件提供了各种比较器实现。
    - File：这个包提供了java.nio.file中的类库的扩展。
    - Filefilter：这个包定义了一个接口（ifilefilter），它结合了filefilter和FilenameFilter。
    - Function：这个包为Lambda表达式和方法引用定义了仅I/O相关的函数接口。
    - Input：这个包提供了输入类的实现，比如InputStream和Reader。
    - Buffer：这个包提供了缓冲输入类的实现，比如CircularBufferInputStream和PeekableInputStream。
    - monitor：这个包提供了一个用于监控文件系统事件（创建目录和文件，更新和删除事件）的组件。
    - Output：这个包提供输出类的实现，比如OutputStream和Writer。
    - Serialization：这个包提供了一个框架来控制类的反序列化。

在Apache Commons IO中，比较常用的工具类主要有IOUtils、FileUtils、FilenameUtils等。
 - **IOUtils中的常用方法如下**：
    - closeQuietly()：无条件地关闭一个可关闭的流。
    - copy()：提供了一系列的复制方法。
    - toByteArray()：以字节数组形式获取内容。
    - write()：内容的写操作。
    - readLines()：以字符串列表的形式获取内容，每行一个条目。
 - **FileUtils中的常用方法如下**：
    - deleteDirectory()：递归删除目录。
    - readFileToString()：将文件的内容读入字符串。
    - deleteQuietly()：删除文件，但不抛出异常。
    - copyFile()：将文件复制到新位置。
    - writeStringToFile()：将字符串内容写入文件。
    - forceMkdir()：创建一个目录，包括任何必要但不存在的父目录。
    - write()：写操作。
    - copyDirectory()：将整个目录复制到新位置。
    - forceDelete()：删除文件或目录。
 - **FilenameUtils中的常用方法如下**：
    - getExtension()：获取文件的扩展名。
    - getBaseName()：从完整的fileName中获取文件名称，减去完整的路径和扩展名。
    - getName()：从完整的fileName中获取名称，减去路径。
    - removeExtension()：从文件名中移除扩展名。
    - normalize()：正常化路径，删除双点和单点路径，比如/foo/./ -> /foo/。
    - wildcardMatch()：检查fileName是否匹配指定的通配符匹配器，比如wildcardMatch("c.txt", "*.txt") --> true。
    - separatorsToUnix()：将所有分隔符转换为正斜杠的UNIX分隔符。
    - getFullPath()：从完整的fileName中获取完整的路径，该文件名是前缀 + 路径。
    - isExtension()：检查fileName的扩展名是否为指定的扩展名。

3. **Apache Commons Collections**

Java集合框架是JDK 1.2中的一个重要补充。它添加了许多强大的数据结构，可以加速大多数重要Java应用程序的开发。

Commons Collections通过提供新的接口、实现和实用程序来构建JDK类。在Apache Commons Collections中，最常用的一个工具类就是CollectionUtils，主要有以下方法：
    - isEmpty()：检查指定的集合是否为空。
    - isNotEmpty()：检查指定的集合是否为空。
    - select()：从输入集合中选择与给定条件匹配的所有元素发送到输出集合中。
    - contains()：检查给定的集合中是否包含给定的元素。
    - filter()：根据指定条件对集合中的元素进行筛选。
    - addAll()：将数组中的所有元素添加到给定的集合中。
    - isEqualCollection()：如果给定的Collection中包含具有相同基数的完全相同的元素，则返回true。

我们在12.6节中介绍过Apache Commons Collections一些旧版本会导致序列化漏洞，所以，在使用该类库时，记得使用新版本。

4. **Apache Commons BeanUtils**

在软件体系架构设计中，分层式结构是最常见、最重要的一种结构。很多人对三层架构、四层架构等并不陌生。

甚至有人说：“计算机科学领域的任何问题都可以通过增加一个间接的中间层来解决，如果不行，那么就加两层。”

但是，随着软件架构分层越来越多，各个层次之间的数据模型就要面临相互转换的问题，典型的问题就是我们可以在代码中见到各种O，如DO、DTO和VO等。

一般情况下，我们在不同的层次要使用不同的数据模型。例如，在数据存储层，我们使用DO抽象一个业务实体；在业务逻辑层，我们使用DTO表示数据传输对象；在展示层，我们把对象封装成VO来与前端进行交互。

那么，数据从前端透传到数据持久化层（从持久层透传到前端），就需要进行对象之间的互相转化，即在不同的对象模型之间进行映射。

通常我们可以使用get/set等方式逐一进行字段映射的操作，例如：
```java
personDTO.setName(personDO.getName());
personDTO.setAge(personDO.getAge());
personDTO.setSex(personDO.getSex());
personDTO.setBirthday(personDO.getBirthday());
```
但是，编写这样的映射代码是一项冗长且容易出错的任务。还有，在某些情况下，需要动态访问Java对象的属性，而这些属性可能没提供setter和getter方法，这时可能就需要用到反射技术等。

所以，为了方便我们在代码中操作JavaBean，Apache提供了Commons BeanUtils，帮助我们屏蔽复杂的java.lang.reflect和java.beans包。


在Commons BeanUtils中，最常用的两个工具类分别是BeanUtils和PropertyUtils。

 - **BeanUtils主要有以下方法**：
    - copyProperties()：对于属性名称相同的两个对象，将属性值从源Bean复制到目标Bean。
    - getProperty()：返回指定Bean的指定属性的值。
    - setProperty()：设置指定的属性值。
    - populate()：基于指定的Map填充指定Bean的JavaBeans属性。
    - cloneBean()：基于可用的属性getter和setter复制Bean（即使Bean类本身没有实现Cloneable）。
 - **PropertyUtils主要有以下方法**：
    - getProperty()：返回指定Bean的指定属性的值。
    - setProperty()：设置指定Bean的指定属性的值。
    - isReadable()：如果指定的属性名标识了指定Bean上的可读属性，则返回true，否则返回false。
    - copyProperties()：将属性值从源Bean复制到目标Bean。
    - isWriteable()：如果指定的属性名标识了指定Bean上的可写属性，则返回true，否则返回false。
    - getPropertyType()：返回表示指定属性的属性类型的Java Class，如果指定的Bean没有这样的属性，则返回null。

以上，我们介绍了Apache Commons BeanUtils中的BeanUtils和PropertyUtils的主要方法。

其中，BeanUtils和PropertyUtils都提供了属性拷贝的方法，如copyProperties，但并不建议读者在代码中使用这两个类中的copyProperties方法，主要是因为这两个方法的效率比较低，并且是浅拷贝。

#### 22.2 各类BeanUtils工具的性能对比
前面我们介绍了Apache Commons BeanUtils，但这个工具类的性能并不好。其实，市面上有很多类似的工具类，除了Apache Commons BeanUtils，比较常用的有Spring BeanUtils、Cglib BeanCopier、Apache PropertyUtils、Dozer、MapStruct等。

到底应该选择哪种工具类更加合适呢？本节从性能的角度来对比不同的工具类的性能。

本节用到的第三方类库的Maven依赖如下：
```xml
<!--Apache PropertyUtils、Apache BeanUtils-->
<dependency>
    <groupId>commons-beanutils</groupId>
    <artifactId>commons-beanutils</artifactId>
    <version>1.9.4</version>
</dependency>
<dependency>
    <groupId>commons-logging</groupId>
    <artifactId>commons-logging</artifactId>
    <version>1.1.2</version>
</dependency>
<!--Spring PropertyUtils-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>org.springframework.beans</artifactId>
    <version>3.1.1.RELEASE</version>
</dependency>
<!--cglib-->
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib-nodep</artifactId>
    <version>2.2.2</version>
</dependency>
<!--dozer-->
<dependency>
    <groupId>net.sf.dozer</groupId>
    <artifactId>dozer</artifactId>
    <version>5.5.1</version>
</dependency>
<!-- 日志相关 -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.7</version>
</dependency>
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>jul-to-slf4j</artifactId>
    <version>1.7.7</version>
</dependency>
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>jcl-over-slf4j</artifactId>
    <version>1.7.7</version>
</dependency>
<dependency>
    <groupId>org.log4j</groupId>
    <artifactId>log4j-over-slf4j</artifactId>
    <version>1.7.7</version>
</dependency>
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-jdk14</artifactId>
    <version>1.7.7</version>
</dependency>
```

接下来是我们的测试代码，首先定义一个PersonDO类：
```java
public class PersonDO {
    private Integer id;
    private String name;
    private Integer age;
    private Date birthday;
    // 省略setter/getter
}
```

再定义一个PersonDTO类：
```java
public class PersonDTO {
    private String name;
    private Integer age;
    private Date birthday;
}
```

然后编写测试类，使用Spring BeanUtils进行属性拷贝：
```java
private void mappingBySpringBeanUtils(PersonDO personDO, int times) {
    StopWatch stopwatch = new StopWatch();
    stopwatch.start();
    for (int i = 0; i < times; i++) {
        PersonDTO personDTO = new PersonDTO();
        org.springframework.beans.BeanUtils.copyProperties(personDO, personDTO);
    }
    stopwatch.stop();
    System.out.println("mappingBySpringBeanUtils cost :" + stopwatch.getTotalTimeMillis());
}
```
其中的StopWatch用于记录代码的执行时间，方便进行对比。

使用Cglib BeanCopier进行属性拷贝：
```java
private void mappingByCglibBeanCopier(PersonDO personDO, int times) {
    StopWatch stopwatch = new StopWatch();
    stopwatch.start();
    for (int i = 0; i < times; i++) {
        PersonDTO personDTO = new PersonDTO();
        BeanCopier copier = BeanCopier.create(PersonDO.class, PersonDTO.class, false);
        copier.copy(personDO, personDTO, null);
    }
    stopwatch.stop();
    System.out.println("mappingByCglibBeanCopier cost :" + stopwatch.getTotalTimeMillis());
}
```

使用Apache BeanUtils进行属性拷贝：
```java
private void mappingByApacheBeanUtils(PersonDO personDO, int times) 
    throws InvocationTargetException, IllegalAccessException {
    StopWatch stopwatch = new StopWatch();
    stopwatch.start();
    for (int i = 0; i < times; i++) {
        PersonDTO personDTO = new PersonDTO();
        BeanUtils.copyProperties(personDTO, personDO);
    }
    stopwatch.stop();
    System.out.println("mappingByApacheBeanUtils cost :" + stopwatch.getTotalTimeMillis());
}
```

使用Apache PropertyUtils进行属性拷贝：
```java
private void mappingByApachePropertyUtils(PersonDO personDO, int times) 
    throws InvocationTargetException, IllegalAccessException, NoSuchMethodException {
    StopWatch stopwatch = new StopWatch();
    stopwatch.start();
    for (int i = 0; i < times; i++) {
        PersonDTO personDTO = new PersonDTO();
        PropertyUtils.copyProperties(personDTO, personDO);
    }
    stopwatch.stop();
    System.out.println("mappingByApachePropertyUtils cost :" + stopwatch.getTotalTimeMillis());
}
```

然后执行以下代码：
```java
public static void main(String[] args) 
    throws InvocationTargetException, IllegalAccessException, NoSuchMethodException {
    PersonDO personDO = new PersonDO();
    PersonDO.setName("Hollis");
    PersonDO.setAge(26);
    PersonDO.setBirthday(new Date());
    // 此处代码不完整，缺少后续调用测试方法等内容
}
```

然后执行以下代码：
```java
public static void main(String[] args) 
    throws InvocationTargetException, IllegalAccessException, NoSuchMethodException {
    PersonDO personDO = new PersonDO();
    personDO.setName("Hollis");
    personDO.setAge(26);
    personDO.setBirthday(new Date());
    personDO.setId(1);
    MapperTest mapperTest = new MapperTest();
    mapperTest.mappingBySpringBeanUtils(personDO, 100);
    mapperTest.mappingBySpringBeanUtils(personDO, 1000);
    mapperTest.mappingBySpringBeanUtils(personDO, 10000);
    mapperTest.mappingBySpringBeanUtils(personDO, 100000);
    mapperTest.mappingBySpringBeanUtils(personDO, 1000000);
    mapperTest.mappingByCglibBeanCopier(personDO, 100);
    mapperTest.mappingByCglibBeanCopier(personDO, 1000);
    mapperTest.mappingByCglibBeanCopier(personDO, 10000);
    mapperTest.mappingByCglibBeanCopier(personDO, 100000);
    mapperTest.mappingByCglibBeanCopier(personDO, 1000000);
    mapperTest.mappingByApachePropertyUtils(personDO, 100);
    mapperTest.mappingByApachePropertyUtils(personDO, 1000);
    mapperTest.mappingByApachePropertyUtils(personDO, 10000);
    mapperTest.mappingByApachePropertyUtils(personDO, 100000);
    mapperTest.mappingByApachePropertyUtils(personDO, 1000000);
    mapperTest.mappingByApacheBeanUtils(personDO, 100);
    mapperTest.mappingByApacheBeanUtils(personDO, 1000);
    mapperTest.mappingByApacheBeanUtils(personDO, 10000);
    mapperTest.mappingByApacheBeanUtils(personDO, 100000);
    mapperTest.mappingByApacheBeanUtils(personDO, 1000000);
}
```

得到的结果如表22 - 1所示。

![image](https://github.com/user-attachments/assets/b5d862d7-9c9f-4ee3-a659-75ad13f357bc)


|工具类|执行1000次拷贝耗时|执行10000次拷贝耗时|执行100000次拷贝耗时|执行1000000次拷贝耗时|
| ---- | ---- | ---- | ---- | ---- |
|Spring BeanUtils|5ms|10ms|45ms|169ms|
|Cglib BeanCopier|4ms|18ms|45ms|91ms|
|Apache PropertyUtils|60ms|265ms|1444ms|11492ms|
|Apache BeanUtils|138ms|816ms|4154ms|36938ms|
|Dozer|566ms|2254ms|11136ms|102965ms|

属性拷贝工具的性能对比如图22 - 1所示。

我们基本可以得出结论，在性能方面，Spring BeanUtils和Cglib BeanCopier的表现不错，而Apache PropertyUtils、Apache BeanUtils和Dozer的表现一般。

所以，如果考虑性能，那么建议不要选择Apache PropertyUtils、Apache BeanUtils和Dozer等工具类。

很多人不理解，为什么大名鼎鼎的Apache开源出来的类库的性能却不高呢？导致这些工具性能低下的原因又是什么呢？

这是因为Apache BeanUtils力求做得完美，在代码中增加了非常多的校验、兼容和日志打印等代码，过度的包装导致性能下降严重。

本节只是站在性能的角度对以上工具类进行了对比，我们在选择一个工具类时，还会有其他方面的考虑，比如使用成本、理解难度、兼容性和可扩展性等，对于这种拷贝类工具类，我们还会考虑其功能是否完善等。

虽然Dozer的性能比较差，但是它可以很好地和Spring结合，可以通过配置文件等进行属性之间的映射等，也受到了很多开发者的喜爱。

### 22.3 MapStruct
#### 22.3.1 MapStruct的使用
MapStruct是一种代码生成器，它极大地简化了基于“约定优于配置”方法的JavaBean类型之间映射的实现。生成的映射代码使用纯方法调用，因此快速、类型安全且易于理解。

约定优于配置，也称作按约定编程，是一种软件设计范式，旨在减少软件开发人员需做决定的数量，获得简单的好处，而又不失灵活性。

假设我们有两个类需要互相转换，分别是PersonDO和PersonDTO，类的定义如下：
```java
public class PersonDO {
    private Integer id;
    private String name;
    private int age;
    private Date birthday;
    private String gender;
}
public class PersonDTO {
    private String userName;
    private Integer age;
    private Date birthday;
    private Gender gender;
}
```

下面演示如何使用MapStruct进行Bean的映射。

想要使用MapStruct，首先需要依赖它相关的jar包，使用Maven依赖的方式如下：
```xml
<properties>
    <org.mapstruct.version>1.3.1.Final</org.mapstruct.version>
</properties>
<dependencies>
    <dependency>
        <groupId>org.mapstruct</groupId>
        <artifactId>mapstruct</artifactId>
        <version>${org.mapstruct.version}</version>
    </dependency>
</dependencies>
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.1</version>
            <configuration>
                <source>1.8</source> <!-- depending on your project -->
                <target>1.8</target> <!-- depending on your project -->
                <annotationProcessorPaths>
                    <path>
                        <groupId>org.mapstruct</groupId>
                        <artifactId>mapstruct-processor</artifactId>
                        <version>${org.mapstruct.version}</version>
                    </path>
                    <!-- other annotation processors -->
                </annotationProcessorPaths>
            </configuration>
        </plugin>
    </plugins>
</build>
```

因为MapStruct需要在编译器中生成转换代码，所以需要在maven - compiler - plugin插件中配置对mapstruct - processor的引用。这部分内容后续会再次介绍。

之后，我们需要定义一个实现映射的接口，主要代码如下：
```java
@Mapper
interface PersonConverter {
    PersonConverter INSTANCE = Mappers.getMapper(PersonConverter.class);
    @Mappings(@Mapping(source = "name", target = "userName"))
    PersonDTO do2dto(PersonDO person);
}
```

使用注解@Mapper定义一个Converter接口，在其中定义一个do2dto方法，方法的入参类型是PersonDO，出参类型是PersonDTO，这个方法用于将PersonDO转换为PersonDTO。

测试代码如下：
```java
public static void main(String[] args) {
    PersonDO personDO = new PersonDO();
    personDO.setName("Hollis");
    personDO.setAge(26);
    personDO.setBirthday(new Date());
    personDO.setId(1);
    personDO.setGender(Gender.MALE.name());
    PersonDTO personDTO = PersonConverter.INSTANCE.do2dto(personDO);
    System.out.println(personDTO);
}
```

输出结果如下：
```
PersonDTO{userName='Hollis', age=26, birthday=Sat Aug 08 19:00:44 CST 2020, gender=MALE}
```

可以看到，我们使用MapStruct完美地将PersonDO转换成了PersonDTO。

MapStruct的用法比较简单，主要依赖@Mapper注解。

大多数情况下，需要互相转换的两个类之间的属性名称和类型等并不完全一致，如果并不想直接对对象进行映射，那么该如何处理呢？

其实MapStruct在这方面也做得很好。

#### 22.3.2 MapStruct处理字段映射
如果要转换的两个类中源对象属性与目标对象属性的类型和名字一致，则会自动映射对应属性。

如果遇到特殊情况，那么如何处理呢？

1. **名字不一致如何映射**

在上面的例子中，在PersonDO中使用name表示用户名称，而在PersonDTO中使用userName表示用户名，如何进行参数映射呢？

这时就要使用@Mapping注解了，只需要在方法签名上使用该注解并指明需要转换的源对象的名字和目标对象的名字即可，比如将name的值映射给userName，可以使用如下方式：
```java
@Mapping(source = "name", target = "userName")
```

2. **可以自动映射的类型**

除了名字不一致，还有一种特殊情况，那就是类型不一致，比如在上面的例子中，在PersonDO中使用String类型表示用户性别，而在PersonDTO中使用一个Gender的枚举表示用户性别。

这时类型不一致，涉及类型互相转换的问题。

其实，MapStruct会对部分类型自动做映射，不需要我们做额外的配置。在上面的例子中，String类型自动转换成了枚举类型。

一般情况下，以下类型可以实现类型的自动转换：
    - 基本类型及其对应的包装类型之间。
    - 基本类型的包装类型和String类型之间。
    - String类型和枚举类型之间。

3. **自定义常量**

如果我们想要给一些属性定义一个固定的值，则可以使用constant：
```java
@Mapping(source = "name", constant = "hollis")
```

4. **类型不一致的字段如何映射**

在上面的例子中，如果我们需要在Person对象中增加家庭住址这个属性，那么一般在PersonDTO中会单独定义一个HomeAddress类来表示家庭住址，而在Person类中，一般使用String类型表示家庭住址。

这就需要在HomeAddress和String之间使用JSON进行类型的互相转换，在这种情况下，MapStruct也是可以支持的。
```java
public class PersonDO {
    private String name;
    private String address;
}
public class PersonDTO {
    private String userName;
    private HomeAddress address;
}
@Mapper
interface PersonConverter {
    PersonConverter INSTANCE = Mappers.getMapper(PersonConverter.class);
    @Mapping(source = "userName", target = "name")
    @Mapping(target = "address",expression = "java(homeAddressToString (dto2do.getAddress()))")
    PersonDO dto2do(PersonDTO dto2do);
    default String homeAddressToString(HomeAddress address){
        return JSON.toJSONString(address);
    }
}
```

我们只需要在PersonConverter中在定义一个方法（因为PersonConverter是一个接口，所以在JDK 1.8以后的版本中可以定义一个default方法），这个方法的作用就是将HomeAddress转换成String类型。

default方法：Java 8引入的新的语言特性，用关键字default标注，被default所标注的方法需要提供实现，而子类可以选择实现或者不实现该方法。

然后在dto2do方法上通过以下注解方式即可实现类型的转换：
```java
@Mapping(target = "address",expression = "java(homeAddressToString (dto2do.getAddress()))")
```

上面这种是自定义的类型转换，还有一些类型的转换是MapStruct本身就支持的，比如String和Date之间的转换：
```java
@Mapping(target = "birthday",dateFormat = "yyyy-MM-dd HH:mm:ss")
```

#### 22.3.3 MapStruct的性能
前面介绍了MapStruct的用法，可以看出MapStruct的使用还是比较简单的，那么它的性能到底怎么样呢？

参考上一节中的示例，我们对MapStruct进行性能测试。

分别执行1000、10000、100000、1000000次映射，耗时分别为0ms、1ms、3ms、6ms。

可以看到，MapStruct的耗时相比较于其他几款工具来说是非常短的。

为什么MapStruct的性能这么高呢？

MapStruct和其他几款工具最大的区别就是：MapStruct在编译时生成Bean映射，这确保了高性能，可以提前将问题反馈出来，也使得开发人员可以彻底地进行错误检查。

还记得前面在引入MapStruct的依赖时，特别在maven - compiler - plugin中增加了mapstruct - processor的支持吗？

并且我们在代码中使用了很多MapStruct提供的注解，这使得在编译期MapStruct就可以直接生成Bean映射的代码，相当于MapStruct代替我们写了很多setter和getter。

例如，在代码中定义一个Mapper：
```java
@Mapper
interface PersonConverter {
    PersonConverter INSTANCE = Mappers.getMapper(PersonConverter.class);
    @Mapping(source = "userName", target = "name")
    @Mapping(target = "address",expression = "java(homeAddressToString(dto2do.getAddress()))")
    @Mapping(target = "birthday",dateFormat = "yyyy-MM-dd HH:mm:ss")
    PersonDO dto2do(PersonDTO dto2do);
    default String homeAddressToString(HomeAddress address){
        return JSON.toJSONString(address);
    }
}
```

代码经过编译后，会自动生成一个PersonConverterImpl：
```java
@Generated(
    value = "org.mapstruct.ap.MappingProcessor",
    date = "2020-08-09T12:58:41+0800",
    comments = "version: 1.3.1.Final, compiler: javac, environment: Java 1.8.0_181 (Oracle Corporation)"
)
class PersonConverterImpl implements PersonConverter {
    @Override
    public PersonDO dto2do(PersonDTO dto2do) {
        if ( dto2do == null ) {
            return null;
        }
        PersonDO personDO = new PersonDO();
        personDO.setName( dto2do.getUserName() );
        if ( dto2do.getAge() != null ) {
            personDO.setAge( dto2do.getAge() );
        }
        if ( dto2do.getGender() != null ) {
            personDO.setGender( dto2do.getGender().name() );
        }
        personDO.setAddress( homeAddressToString(dto2do.getAddress()) );
        return personDO;
    }
}
```

在运行期，对Bean进行映射时，会直接调用PersonConverterImpl的dto2do方法，这个方法内部的操作就是简单的赋值操作了。

因为在编译期做了很多事情，所以MapStruct在运行期的性能会很高，并且还有一个好处，那就是可以把问题的暴露提前到编译期。

如果代码中的字段映射有问题，那么应用就无法编译，这时会强制开发者解决这个问题。

### 小结
本节介绍了一款Java中的字段映射工具类MapStruct，它的用法比较简单，并且功能非常完善，可以应付各种情况的字段映射。

因为MapStruct在编译期就会生成真正的映射代码，这使得JVM运行期的性能得到了很大的提升。 
