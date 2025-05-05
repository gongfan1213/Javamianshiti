### 第21章 BigDecimal
#### 21.1 什么是BigDecimal

在Java中，很多基本数据类型可供我们直接使用，比如用于表示浮点型的float、double，用于表示字符型的char，用于表示整型的int、short、long等。

以整数为例，如果我们想要表示一个非常大的整数，比如这个整数超过64位，那么能表示最大数字的long类型也无法存取这样的数字，这时该怎么办呢？以前的做法是把数字存储在字符串中，大数之间的四则运算及其他运算都是通过数组完成的。

JDK也有类似的实现，那就是BigInteger和BigDecimal，BigInteger用来表示任意大小的整数，BigDecimal用来表示一个任意大小且精度完全准确的浮点数。

前面的章节中介绍过，由于计算机中保存的小数其实是十进制的小数的近似值，并不是准确值，所以，千万不要在代码中使用浮点数来表示金额等重要的指标。


而这种高精度的浮点数可以使用BigDecimal来表示。那么BigDecimal是如何表示一个数并保证精确的呢？

1. **BigDecimal如何精确计数**

如果看过BigDecimal的源码，可以发现，实际上BigDecimal是通过一个“无标度值”和一个“标度”来表示一个数的。


在BigDecimal中，标度是通过scale字段来表示的。

而无标度值的表示比较复杂。当unscaled value超过阈值（默认为Long.MAX_VALUE）时采用intVal字段存储unscaled value、intCompact字段存储Long.MIN_VALUE，否则对unscaled value进行压缩并存储到long型的intCompact字段中用于后续计算，intVal为空。

涉及的字段如下代码所示：
```java
public class BigDecimal extends Number implements Comparable<BigDecimal> {
    private final BigInteger intVal;
    private final int scale;
    private final transient long intCompact;
}
```

无标度值的压缩机制不是本章的重点，只需要知道BigDecimal是通过一个无标度值和标度表示的就行了。

2. **什么是标度**

除了scale这个字段，在BigDecimal中还提供了scale()方法，用来返回这个BigDecimal的标度。

```java
/**
 * Returns the <i>scale</i> of this {@code BigDecimal}. If zero
 * or positive, the scale is the number of digits to the right of
 * the decimal point. If negative, the unscaled value of the
 * number is multiplied by ten to the power of the negation of the
 * scale. For example, a scale of {@code -3} means the unscaled
 * value is multiplied by 1000.
 *
 * @return the scale of this {@code BigDecimal}.
 */
public int scale() {
    return scale;
}
```
scale到底表示的是什么呢？其实上面的注释已经说明了：

如果scale为零或正值，则该值表示这个数字小数点右侧的位数。如果scale为负数，则该数字的真实值需要乘以10的该负数的绝对值的幂。例如，scale为 - 3，则这个数需要乘以1000，即在末尾有3个0。

比如123.123，如果使用BigDecimal表示，那么它的无标度值为123123，标度为3。

而二进制无法表示的0.1，使用BigDecimal就可以表示了，即通过无标度值1和标度1来表示。

我们知道，创建一个对象，就需要使用该类的构造方法，在BigDecimal中一共有以下4个构造方法：
- BigDecimal(int)
- BigDecimal(double)
- BigDecimal(long)
- BigDecimal(String)

以上4个方法创建出来的BigDecimal的标度（scale）是不同的。

其中BigDecimal(int)和BigDecimal(long)比较简单，因为都是整数，所以它们的标度都是0。

BigDecimal(double)和BigDecimal(String)的标度复杂一些，下一节详细介绍BigDecimal(double)和BigDecimal(String)。

#### 21.2 为什么不能直接使用double创建一个BigDecimal
21.1节介绍了BigDecimal是如何精确表示一个浮点数的。很多人都知道，在金额表示、金额计算等场景，不能使用double、float等类型，而是要使用对精度支持得更好的BigDecimal。

所以，很多支付、电商、金融等业务中，BigDecimal的使用非常频繁。但是，如果误以为只要使用BigDecimal表示数字，结果就一定精确，那就大错特错了！

使用BigDecimal的第一步就是创建一个BigDecimal对象，如果这一步有问题，那么后面怎么算都是错的。

在创建BigDecimal时，为了不损失精度，一定要避免使用double直接创建BigDecimal。

1. **BigDecimal(double)有什么问题**

BigDecimal中提供了一个通过double创建BigDecimal的方法——BigDecimal(double)，但也给我们留了一个“坑”。


double表示的小数是不精确的，比如0.1这个数字，double只能表示它的近似值。

当我们使用new BigDecimal(0.1)创建一个BigDecimal时，其实创建出来的值并不是正好等于0.1，而是0.1000000000000000055511151231257827021181583404541015625。这是因为double自身表示的只是一个近似值。

如果我们在代码中使用BigDecimal(double)创建一个BigDecimal，那么是损失了精度的，这是极其严重的问题。

2. **使用BigDecimal(String)创建BigDecimal**

该如何创建一个精确的BigDecimal来表示小数呢？答案是使用String创建BigDecimal。

而对于BigDecimal(String)，当我们使用new BigDecimal("0.1")创建一个BigDecimal时，其实创建出来的值正好等于0.1，它的标度是1。

需要注意的是，new BigDecimal("0.10000")和new BigDecimal("0.1")这两个数的标度分别是5和1，如果使用BigDecimal的equals方法比较两个数，则得到的结果是false，这个问题在下一节讨论。

想要创建一个能精确表示0.1的BigDecimal，可以使用以下两种方式：
```java
BigDecimal recommend1 = new BigDecimal("0.1");
BigDecimal recommend2 = BigDecimal.valueOf(0.1);
```

#### 21.3 为什么不能使用BigDecimal的equals方法比较大小

21.2节介绍了在创建BigDecimal时需要注意的事项，使用BigDecimal时也有一个关键点需要注意，那就是对其进行相等判断。

关于这个知识点，在最新版的《阿里巴巴Java开发手册》中也有说明：

【强制】如上所示BigDecimal的等值比较应使用compareTo()方法，而不是equals()方法。

说明：equals()方法会比较值和精度（1.0与1.00返回结果为false），而compareTo()则会忽略精度。


笔者曾经见过以下这样的低级错误：
```java
if(bigDecimal == bigDecimal1){
    // 两个数相等
}
```
相信读者一眼就可以看出问题，因为BigDecimal是对象，所以不能用“==”来判断两个数字的值是否相等。

以下代码是否有问题呢？
```java
if(bigDecimal.equals(bigDecimal1)){
    // 两个数相等
}
```
以上这种写法的代码得到的结果很可能和我们预想的不一样。

先来做一个实验，运行以下代码：
```java
BigDecimal bigDecimal = new BigDecimal(1);
BigDecimal bigDecimal1 = new BigDecimal(1);
System.out.println(bigDecimal.equals(bigDecimal1));
BigDecimal bigDecimal2 = new BigDecimal(1);
BigDecimal bigDecimal3 = new BigDecimal(1.0);
System.out.println(bigDecimal2.equals(bigDecimal3));
BigDecimal bigDecimal4 = new BigDecimal("1");
BigDecimal bigDecimal5 = new BigDecimal("1.0");
System.out.println(bigDecimal4.equals(bigDecimal5));
```
以上代码的输出结果为：

true

true

false

1. **BigDecimal的equals方法**

通过以上代码示例我们发现，在使用BigDecimal的equals方法对1和1.0进行比较时，有的时候是true（当使用int和double定义BigDecimal时），有的时候是false（当使用String定义BigDecimal时）。


为什么会出现这样的情况呢？我们先来看一下BigDecimal的equals方法。

在BigDecimal的JavaDoc中其实已经解释了原因：

Compares this BigDecimal with the specified Object for equality. Unlike compareTo, this method considers two BigDecimal objects equal only if they are equal in value and scale (thus 2.0 is not equal to 2.00 when compared by this method)


大概意思就是，equals方法和compareTo并不一样，equals方法会比较两部分内容，分别是值（value）和标度（scale）。

![image](https://github.com/user-attachments/assets/dbff4a6a-de0b-4801-9f53-078e139f07ef)


对应的代码如下：
```java
public boolean equals(Object x) {
    if (!(x instanceof BigDecimal))
        return false;
    BigDecimal xDec = (BigDecimal) x;
    if (x == this)
        return true;
    if (scale != xDec.scale)
        return false;
    long s = this.intCompact;
    long xs = xDec.intCompact;
    if (s != INFLATED) {
        if (xs == INFLATED)
            xs = compactValFor(xDec.intVal);
        return xs == s;
    } else if (xs != INFLATED)
        return xs == compactValFor(this.intVal);
    return this.inflated().equals(xDec.inflated());
}
```
以上代码定义出来的两个BigDecimal对象（bigDecimal4和bigDecimal5）的标度是不一样的，所以使用equals比较的结果就是false了。

对代码进行“Debug”，在“Debug”的过程中我们也可以看到，bigDecimal4的标度是0，而bigDecimal5的标度是1。

之所以equals比较bigDecimal4和bigDecimal5的结果是false，就是因为标度不同。

为什么标度不同呢？为什么bigDecimal2和bigDecimal3的标度是一样的（当使用int和double定义BigDecimal时），而bigDecimal4和bigDecimal5却不一样（当使用String定义BigDecimal时）呢？

![image](https://github.com/user-attachments/assets/88726c5d-6532-4418-a4ff-df71ab5fbd07)


2. **为什么标度不同**

这就涉及BigDecimal的标度问题了。

- **BigDecimal(long)和BigDecimal(int)**

前面说过，BigDecimal(long)和BigDecimal(int)的标度是0。
```java
public BigDecimal(int val) {
    this.intCompact = val;
    this.scale = 0;
    this.intVal = null;
}
public BigDecimal(long val) {
    this.intCompact = val;
    this.intVal = (val == INFLATED)? INFLATED_BIGINT : null;
    this.scale = 0;
}
```

- **BigDecimal(double)**

前面说过，使用BigDecimal(double)创建一个BigDecimal时，创建出来的值是一个近似值。



例如，无论使用new BigDecimal(0.1)还是new BigDecimal(0.10)，它的近似值都是0.1000000000000000055511151231257827021181583404541015625，它的标度就是这个数字的位数，即55。



其他的浮点数也是同样的道理。对于new BigDecimal(1.0)这样的形式来说，因为本质上也是一个整数，所以它创建出来的数字的标度就是0。



因为BigDecimal(1.0)和BigDecimal(1.00)的标度是一样的，所以在使用equals方法比较两个数时，得到的结果就是true。

- **BigDecimal(string)**

前面说过，当我们使用BigDecimal( "0.1" )创建一个BigDecimal时，其实创建出来的值正好等于0.1，它的标度是1。


如果使用new BigDecimal("0.10000")，那么创建出来的数就是0.10000，标度是5。

因为BigDecimal("1.0")和BigDecimal("1.00")的标度不一样，所以在使用equals方法比较两个数时，得到的结果就是false。

3. **如何比较BigDecimal**

前面解释了BigDecimal的equals方法，其实不只比较数字的值，还会对比其标度。

所以，当我们使用equals方法判断两个数是否相等时，是极其严格的。

如果我们只想判断两个BigDecimal的值是否相等，那么该如何判断呢？

BigDecimal中提供了compareTo方法，这个方法就可以只比较两个数字的值，如果两个数相等，则返回0。

```java
BigDecimal bigDecimal4 = new BigDecimal("1");
BigDecimal bigDecimal5 = new BigDecimal("1.0000");
System.out.println(bigDecimal4.compareTo(bigDecimal5));
```
以上代码的输出结果如下：

0

其源码如下：
```java
/**
 * Compares this {@code BigDecimal} with the specified
 * {@code BigDecimal}. Two {@code BigDecimal} objects that are
 * equal in value but have a different scale (like 2.0 and 2.00)
 * are considered equal by this method. This method is provided
 * in preference to individual methods for each of the six boolean
 * comparison operators ({@literal <}, ==,
 * {@literal >}, {@literal >=}, !=, {@literal <=}). The
 * suggested idiom for performing these comparisons is:
 * {@code (x.compareTo(y) <i>op</i> 0)}, where
 * {@code <i>op</i>} is one of the six comparison operators.
 *
 * @param  val {@code BigDecimal} to which this {@code BigDecimal} is
 *         to be compared.
 * @return -1, 0, or 1 as this {@code BigDecimal} is numerically
 *         less than, equal to, or greater than {@code val}.
 */
public int compareTo(BigDecimal val) {
    // Quick path for equal scale and non-inflated case.
    if (scale == val.scale) {
        long xs = intCompact;
        long ys = val.intCompact;
        if (xs != INFLATED && ys != INFLATED)
            return xs != ys? ((xs > ys)? 1 : -1) : 0;
    }
    int xsign = this.signum();
    int ysign = val.signum();
    if (xsign != ysign)
        return (xsign > ysign)? 1 : -1;
    if (xsign == 0)
        return 0;
    int cmp = compareMagnitude(val);
    return (xsign > 0)? cmp : -cmp;
}
```

### 小结
BigDecimal是一个非常好用的表示高精度数字的类，其中提供了很多丰富的方法。但是，使用它的equals方法时需要谨慎，因为equals方法不仅比较两个数字的值，还会比较它们的标度，只要这两个因素有一个是不相等的，那么结果也是false。

如果想比较两个BigDecimal的数值，则可以使用compareTo方法。 
