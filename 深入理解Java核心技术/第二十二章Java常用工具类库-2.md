### 22.4 BeanUtils与深/浅拷贝

前面介绍了几种BeanUtils的工具类，笔者在代码中也经常使用这类工具。在之前的一个项目中，笔者在代码中引入了MapStruct和Spring的BeanUtils。

如果是DO和DTO/Entity之间的转换，那么统一使用MapStruct，因为它可以指定单独的Mapper，可以自定义一些策略。

如果是同对象之间的复制（比如用一个DO创建一个新的DO），或者完全不相关的两个对象转换，则使用Spring的BeanUtils。

刚开始没什么问题，但后面笔者在写单元测试时发现了一个问题。

先来看一下我们在什么地方使用了Spring的BeanUtils。

在业务逻辑中，我们需要对订单信息进行修改，在更改订单信息时，不仅要更新订单的属性信息，还需要创建一条变更流水。

而变更流水中同时记录了变更前和变更后的数据，所以就有了以下代码：
```java
// 从数据库中查询出当前订单, 并加锁
OrderDetail orderDetail = orderDetailDao.queryForLock();
// 复制一个新的订单模型
OrderDetail newOrderDetail = new OrderDetail();
BeanUtils.copyProperties(orderDetail, newOrderDetail);
// 对新的订单模型进行修改逻辑的操作
newOrderDetail.update();
// 使用修改前的订单模型和修改后的订单模型组装出订单变更流水
OrderDetailStream orderDetailStream = new OrderDetailStream();
orderDetailStream.create(orderDetail, newOrderDetail);
```

因为创建订单变更流水时需要一个改变前的订单和改变后的订单，所以我们想到了要新建一个新的订单模型，然后操作新的订单模型，避免对旧的有影响。

但这个BeanUtils.copyProperties的执行过程其实是有问题的。

因为BeanUtils在进行属性拷贝时，本质上是浅拷贝，而不是深拷贝。

我们举一个例子来看一下为什么说BeanUtils.copyProperties的执行过程是浅拷贝。

首先定义两个类：
```java
public class Address {
    private String province;
    private String city;
    private String area;
    // 省略构造函数和setter/getter
}
class User {
    private String name;
    private String password;
    private Address address;
    // 省略构造函数和setter/getter
}
```

然后编写一段测试代码：
```java
User user = new User("Hollis", "hollischuang");
user.setAddress(new Address("zhejiang", "hangzhou", "binjiang"));
User newUser = new User();
BeanUtils.copyProperties(user, newUser);
System.out.println(user.getAddress() == newUser.getAddress());
```

以上代码的输出结果为true。

即通过BeanUtils.copyProperties拷贝出来的newUser中的address对象和原来的user中的address对象是同一个对象。

修改newUser中的address对象：
```java
newUser.getAddress().setCity("shanghai");
System.out.println(JSON.toJSONString(user));
System.out.println(JSON.toJSONString(newUser));
```

输出结果如下：
```
{"address":{"area":"binjiang","city":"shanghai","province":"zhejiang"},"name":"Hollis","password":"hollischuang"}
{"address":{"area":"binjiang","city":"shanghai","province":"zhejiang"},"name":"Hollis","password":"hollischuang"}
```

可以发现，原来的对象也受到了修改的影响。

这就是所谓的浅拷贝！关于深拷贝、浅拷贝的问题，我们在第3章介绍clone方法时提到过，可以通过重写clone方法实现深拷贝。我们也在12.6节中介绍过使用序列化技术实现深拷贝，这里就不再赘述了。

需要注意的是，在使用BeanUtils时要时刻警惕深拷贝和浅拷贝的问题。

### 22.5 Guava
22.1节介绍了Apache Commons类库，这是一个集合了很多Java基础工具的类库，由Apache基金会维护。市面上还有一个开源的类库，也提供了很多类似的功能，甚至有些功能要比Apache Commons类库更加完善，那就是由Google开源的Guava。

Guava包含了若干被Google的Java项目广泛依赖的核心库，比如集合（collections）、缓存（caching）、原生类型支持（primitives support）、并发库（concurrency libraries）、通用注解（common annotations）、字符串处理（string processing）和I/O等。这些工具每天都在被Google的工程师应用在各种产品服务中。

很多Apache Commons类库提供的工具类和方法在Guava中也都有类似的实现，这里就不重复介绍了。

本节主要介绍几个Guava中比较好用的核心库。

#### 22.5.1 Optional
Optional是Java 8中提供的一个解决了空指针异常的工具类。

其实在Java 8之前，Optional就已经有了——在Guava中提供的。

Optional的存在主要是帮助开发者避免NullPointerException异常的，当我们操作一个值为null的对象时，就会发生NullPointerException，这就使得很多开发者不得不在对象真正被使用之前做很多检查工作，例如：
```java
if(dog != null){
    dog.run();
}
```

为了减少开发者对null的关注，Google定义了一个Optional类来帮助开发者处理null。

我们可以把Optional理解为对对象的一种包装。我们可以将一个对象包装到Optional中，通过Optional来屏蔽对对象是否为null的判断逻辑。

Optional中的对象可以有两种，一种是可以为null的对象，另一种是不能为null的对象。当我们想把一个不能为null的对象封装到Optional中时，可以使用以下方法：
```java
Optional.of(dog);
```

在执行以上方法时，如果dog == null，那么会立即抛出NullPointException，这使得对象在尚未被真正使用之前就强制强制地执行了一次是否为null的判断逻辑。

当我们想把一个可以为null的对象封装到Optional中时，可以使用以下方法：
```java
// Guava中的用法:
Optional.fromNullable(dog);
// Java 8中的用法:
Optional.ofNullable(dog);
```

这样就可以得到一个被Optional包装过的对象。

当我们想对一个可能为null的对象进行处理时，应该怎么办呢？这时就不需要做是否为null的判断了吗？

先来看一下Optional中都提供了哪些方法：
 - get()：返回Optional中所包含的实例，如果不存在实例，则抛出异常。
 - isPresent()：如果对象不为null则返回true，否则返回false。
 - or(T)：Java 8中为orElse(T)，如果Optional中包含一个对象，则返回该对象，否则返回方法中传入的对象T。

我们一般在代码中很少使用isPresent()，因为使用Optional之后还需要使用isPresent()判断对象实例是否存在，这样就和我们自己执行!=null判断没什么区别了。

我们经常使用的是get()和or(T)这两个方法，以下是几个比较常见的用法。

对象存在即返回，如果不存在则提供默认值：
```java
public User getUserName(User user){
    Optional.fromNullable(user).or(new User("游客"));
}
```

对象存在即返回，如果不存在则通过指定方法进行处理：
```java
Optional.fromNullable(user).or(() -> getDefaultUser());
```

如果对象存在则对其进行处理，如果不存在则什么都不做：
```java
Optional.fromNullable(user).ifPresent(System.out::println);
```

#### 22.5.2 Guava Cache
随着互联网的蓬勃发展，网络流量与并发请求越来越多，全面提升服务性能是很多大型网站的重要课题。

如何在有限的机器资源的情况下，提供可用性更高、性能更好的服务呢？在众多的技术方案中，缓存无疑是解决高并发问题的一大利器。

在服务中引入缓存来存储一些热点数据，可以达到快速响应的目的，通常在Web服务开发中，经常用到的缓存技术主要有两种，分别是本地缓存和分布式缓存。

本地缓存指的是在应用中的缓存组件。对于Java应用来说，本地缓存是存储在JVM中的。所以，本地缓存最大的优点就是存取速度非常快，没有过多的网络开销。对于单体应用来说，本地缓存没有任何缺点。但对于集群应用来说，每个机器上都有一份缓存数据，这就导致缓存无法共享，可能出现缓存不一致的问题。

而分布式缓存是指一个独立的缓存服务，不和应用部署在一起。这就使得一个集群中的多台机器可以同时共享同一份缓存数据。缺点是成本比较高，性能要比本地缓存低一些。

虽然本地缓存在分布式系统中存在不一致的问题，但还是有很多场景适合使用本地缓存，例如：

（1）缓存静态配置、数据字典等。

（2）缓存一些在单个线程可能会多次用到的中间或者结果数据。

（3）本地缓存+分布式缓存组合成多级缓存。

最简单的缓存的实现方式就是定义一个HashMap保存想要缓存的数据。但是，因为本地缓存和应用同属于一个进程，并且在Java中，本地缓存也需要存储在JVM中，一旦使用不当，就可能导致OOM等问题。所以设计本地缓存时需要考虑容量限制、过期策略、淘汰策略和自动刷新等很多因素。

很多开源框架提供了缓存功能，如Guava Cache、Caffeine和Encache等。下面介绍Guava中提供的本地缓存工具——Guava Cache。

Guava Cache的设计灵感来源于ConcurrentHashMap，都使用了多个segments方式的细粒度锁，在保证线程安全的同时，支持高并发场景需求，同时支持多种类型的缓存清理策略，包括基于容量的清理、基于时间的清理和基于引用的清理等。

Guava Cache的用法很简单，在引入Guava的相关依赖后，使用方式如下：
```java
// CacheLoader是一个加载器
LoadingCache<Key, Graph> graphs = CacheBuilder.newBuilder()
    // 设置最大容量
   .maximumSize(1000)
    // 设置缓存超时时长
   .expireAfterWrite(10, TimeUnit.MINUTES)
    // 设置缓存刷新时长
   .refreshAfterWrite(1, TimeUnit.SECONDS)
    // 设置缓存被移除时的监听器
   .removalListener(notification -> log.info("notification={}", JSON.toJSONString(notification)))
    // 创建一个CacheLoader, 重写load方法
   .build(new CacheLoader<Key, String>() {
        @Override
        public String load(String key) throws Exception {
            return key;
        }
    });
```

关于GuavaCache的使用，还有很多自定义的参数可以设置，如初始容量、并发级别、统计缓存命中率和失效策略等。

#### 22.5.3 不可变集合

8.1节介绍了String的不可变性，提到了将String设计成不可变可以提升其安全性和性能。

很多时候，我们使用的集合是不会发生变化的，如果能够定义一种不可变的集合，那么就能带来以下几个好处：

（1）因为不可变集合类不能发生改变，所以不支持写操作，也就不会发生并发问题。

（2）不可变集合类不需要支持修改操作，可以节省空间和时间的开销。

（3）不可变集合类可以避免被错误修改，保证其安全性。


Guava提供了Immutable集合，这是一组不可变的集合，其中包含了ImmutableList、ImmutableSet和ImmutableMap等常见的集合类，如下表所示。

|可变集合接口|不可变版本|
| ---- | ---- |
|Collection|ImmutableCollection|
|List|ImmutableList|
|Set|ImmutableSet|
|SortedSet/NavigableSet|ImmutableSortedSet|
|Map|ImmutableMap|
|SortedMap|ImmutableSortedMap|

Guava提供的不可变集合是不支持修改的，所有的修改类型操作都会抛出UnsupportedOperationException异常。ImmutableList中的put和remove等方法的实现如下：
```java
public final boolean addAll(int index, Collection<? extends E> newElements) {
    throw new UnsupportedOperationException();
}
public final E set(int index, E element) {
    throw new UnsupportedOperationException();
}
public final void add(int index, E element) {
    throw new UnsupportedOperationException();
}
public final E remove(int index) {
    throw new UnsupportedOperationException();
}
public final void replaceAll(UnaryOperator<E> operator) {
    throw new UnsupportedOperationException();
}
public final void sort(Comparator<? super E> c) {
    throw new UnsupportedOperationException();
}
```

Guava的不可变集合类使用起来也非常方便，其中主要提供了copyOf和of方法，比如ImmutableList中这两个方法的功能都是“返回包含给定元素的不可变列表”，只不过of的入参是元素，copyOf的入参是集合。

![image](https://github.com/user-attachments/assets/2519ecc7-d94b-44ea-8b03-12a93e4037c5)



这两个方法的用法也很简单，比如创建一个包含a、b、c、d四个元素的不可变List：
```java
ImmutableList.of("a", "b", "c", "d");
```

创建一个包含k1-v1和k2-v2两组Key-Value元素的不可变Map：
```java
ImmutableMap.of("k1", "v1", "k2", "v2");
```

其他不可变集合的of用法也同理，copyOf的用法就更简单了，根据指定的集合创建一个不可变集合即可：
```java
List<String> list = new ArrayList<String>();
list.add("a");
list.add("b");
list.add("c");
ImmutableList.copyOf(list);
```

因为不可变集合的用法比较简单，语义也比较明确，所以当我们创建的是一个不会发生改变或者不支持修改的集合时，可以优先使用Guava中提供的不可变集合。

需要明确的是，不可变集合是不支持修改的，一旦要对其进行修改，就会抛出UnsupportedOperationException异常。

#### 22.5.4 集合工具类
Guava和Apache Commons一样，也提供了一些处理集合的工具类，比如处理List的Lists类、处理Map的Maps类等，如下表所示。

|集合接口|对应的Guava工具类|
| ---- | ---- |
|Collection|Collections2|
|List|Lists|
|Set|JDK Sets|
|SortedSet|Sets|
|Map|Maps|
|SortedMap|Maps|
|Queue|Queues|

这些工具类都提供了一些JDK中没有的新的方法，比如Lists中提供的partition、reverse等方法，Sets中提供的union、difference等方法。

这些集合类还提供了很多用于初始化的方法，比如Lists.newArrayList、Lists.newArrayListWithCapacity、Lists.newArrayListWithExpectedSize、Lists.newLinkedList和Lists.newCopyOnWriteArrayList等，可以让我们在代码中创建集合的同时对其进行初始化。

当我们想创建一个集合并指定其初始化容量时，可以使用Maps.newHashMapWithExpectedSize，使用这个方法创建的Map可以最大限度地减少空间损耗。

以上我们介绍了Guava中提供的Optional、Cache、不可变集合类及集合工具类等，这些只是Guava众多强大功能中的一部分。Guava对于并发、I/O等都有很多好用的工具类，建议读者查看Guava官方文档了解其常见用法，使用这些成熟的工具类，可以大大提升代码的健壮性。 
