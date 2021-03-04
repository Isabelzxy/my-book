## JAVA Basic 

### 1.Object类中方法详解

Object类是Java中所有类的基类。位于java.lang包中，一共有13个方法

#### 方法一 Object() 即Object的构造方法

大部分情况下，Java中通过形如 new A(args..)形式创建一个属于该类型的对象。

其中A即是类名，A(args..)即此类定义中相对应的构造函数。通过此种形式创建的对象都是通过类中的构造函数完成。为体现此特性，Java中规定：在类定义过程中，对于未定义构造函数的类，默认会有一个无参数的构造函数，作为所有类的基类，Object类自然要反映出此特性，在源码中，未给出Object类构造函数定义，但实际上，此构造函数是存在的。

当然，并不是所有的类都是通过此种方式去构建，也自然的，并不是所有的类构造函数都是public。

#### 方法二 registerNatives

通常情况下，为了使JVM发现您的本机功能，他们被一定的方式命名。例如，对于java.lang.Object.registerNatives，对应的C函数命名为Java_java_lang_Object_registerNatives。通过使用registerNatives（或者更确切地说，JNI函数RegisterNatives），您可以命名任何你想要你的C函数。

#### 方法三 clone

注意：clone返回的对象为浅拷贝

clone()方法同样是一个被声明为native的方法，因此，我们知道了clone()方法并不是Java的原生方法，具体的实现是有C/C++完成的。clone英文翻译为"克隆"，其目的是创建并返回此对象的一个副本。形象点理解，这有一辆科鲁兹，你看着不错，想要个一模一样的。你调用此方法即可像变魔术一样变出一辆一模一样的科鲁兹出来。配置一样，长相一样。但从此刻起，原来的那辆科鲁兹如果进行了新的装饰，与你克隆出来的这辆科鲁兹没有任何关系了。你克隆出来的对象变不变完全在于你对克隆出来的科鲁兹有没有进行过什么操作了。Java术语表述为：clone函数返回的是一个引用，指向的是新的clone出来的对象，此对象与原对象分别占用不同的堆空间。

#### 方法4 getClass()

getClass()也是一个native方法，返回的是此Object对象的类对象/运行时类对象Class<?>。效果与Object.class相同。

首先解释下"类对象"的概念：在Java中，类是是对具有一组相同特征或行为的实例的抽象并进行描述，对象则是此类所描述的特征或行为的具体实例。作为概念层次的类，其本身也具有某些共同的特性，如都具有类名称、由类加载器去加载，都具有包，具有父类，属性和方法等。于是，Java中有专门定义了一个类，Class，去描述其他类所具有的这些特性，因此，从此角度去看，类本身也都是属于Class类的对象。为与经常意义上的对象相区分，在此称之为"类对象"。


#### 方法5 equals

==与equals在Java中经常被使用，大家也都知道==与equals的区别：

==表示的是变量值完成相同（对于基础类型，地址中存储的是值，引用类型则存储指向实际对象的地址）；

equals表示的是对象的内容完全相同，此处的内容多指对象的特征/属性。

实际上，上面说法是不严谨的，更多的只是常见于String类中。首先看一下Object类中关于equals()方法的定义：

```
public boolean equals(Object obj) {
     return (this == obj);
}
```

由此可见，Object原生的equals()方法内部调用的正是==，与==具有相同的含义。既然如此，为什么还要定义此equals()方法？

equlas()方法的正确理解应该是：判断两个对象是否相等。那么判断对象相等的标尺又是什么？

如上，在object类中，此标尺即为==。当然，这个标尺不是固定的，其他类中可以按照实际的需要对此标尺含义进行重定义。如String类中则是依据字符串内容是否相等来重定义了此标尺含义。如此可以增加类的功能型和实际编码的灵活性。当然了，如果自定义的类没有重写equals()方法来重新定义此标尺，那么默认的将是其父类的equals()，直到object基类。

如上重写equals方法表面上看上去是可以了，实则不然。因为它破坏了Java中的约定：**重写equals()方法必须重写hasCode()方法**。

#### 方法6 hashCode();

hashCode()方法返回一个整形数值，表示该对象的哈希码值。

hashCode()具有如下约定：

1).在Java应用程序程序执行期间，对于同一对象多次调用hashCode()方法时，其返回的哈希码是相同的，前提是将对象进行equals比较时所用的标尺信息未做修改。在Java应用程序的一次执行到另外一次执行，同一对象的hashCode()返回的哈希码无须保持一致；

2).如果两个对象相等（依据：调用equals()方法），那么这两个对象调用hashCode()返回的哈希码也必须相等；

3).反之，两个对象调用hasCode()返回的哈希码相等，这两个对象不一定相等。

即严格的数学逻辑表示为： 两个对象相等 <=>  equals()相等  => hashCode()相等。因此，重写equlas()方法必须重写hashCode()方法，以保证此逻辑严格成立，同时可以推理出：hasCode()不相等 => equals（）不相等 <=> 两个对象不相等。

可能有人在此产生疑问：既然比较两个对象是否相等的唯一条件（也是冲要条件）是equals，那么为什么还要弄出一个hashCode()，并且进行如此约定，弄得这么麻烦？

其实，这主要体现在hashCode()方法的作用上，其主要用于增强哈希表的性能。

以集合类中，以Set为例，当新加一个对象时，需要判断现有集合中是否已经存在与此对象相等的对象，如果没有hashCode()方法，需要将Set进行一次遍历，并逐一用equals()方法判断两个对象是否相等，此种算法时间复杂度为o(n)。通过借助于hasCode方法，先计算出即将新加入对象的哈希码，然后根据哈希算法计算出此对象的位置，直接判断此位置上是否已有对象即可。（注：Set的底层用的是Map的原理实现）

在此需要纠正一个理解上的误区：对象的hashCode()返回的不是对象所在的物理内存地址。甚至也不一定是对象的逻辑地址，hashCode()相同的两个对象，不一定相等，换言之，不相等的两个对象，hashCode()返回的哈希码可能相同。

因此，在上述代码中，重写了equals()方法后，需要重写hashCode()方法。

#### 方法7 toString();

toString()方法返回该对象的字符串表示。先看一下Object中的具体方法体：

```
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

toString()方法相信大家都经常用到，即使没有显式调用，但当我们使用System.out.println(obj)时，其内部也是通过toString()来实现的。

getClass()返回对象的类对象，getClassName()以String形式返回类对象的名称（含包名）。Integer.toHexString(hashCode())则是以对象的哈希码为实参，以16进制无符号整数形式返回此哈希码的字符串表示形式。

如上例中的u1的哈希码是638，则对应的16进制为27e，调用toString()方法返回的结果为：com.corn.objectsummary.User@27e。


因此：toString()是由对象的类型和其哈希码唯一确定，同一类型但不相等的两个对象分别调用toString()方法返回的结果可能相同。

#### wait(...) / notify() / notifyAll()方法

一说到wait(...) / notify() | notifyAll()几个方法，首先想到的是线程。确实，这几个方法主要用于java多线程之间的协作。先具体看下这几个方法的主要含义：

wait()：调用此方法所在的当前线程等待，直到在其他线程上调用此方法的主调（某一对象）的notify()/notifyAll()方法。

wait(long timeout)/wait(long timeout, int nanos)：调用此方法所在的当前线程等待，直到在其他线程上调用此方法的主调（某一对象）的notisfy()/notisfyAll()方法，或超过指定的超时时间量。

notify()/notifyAll()：唤醒在此对象监视器上等待的单个线程/所有线程。

#### 方法13 finalize();

finalize方法主要与Java垃圾回收机制有关。首先我们看一下finalized方法在Object中的具体定义：

```
protected void finalize() throws Throwable { }
```

我们发现Object类中finalize方法被定义成一个空方法，为什么要如此定义呢？finalize方法的调用时机是怎么样的呢？

首先，Object中定义finalize方法表明Java中每一个对象都将具有finalize这种行为，其具体调用时机在：JVM准备对此对形象所占用的内存空间进行垃圾回收前，将被调用。由此可以看出，此方法并不是由我们主动去调用的（虽然可以主动去调用，此时与其他自定义方法无异）。

### 2.HashMap的长度为什么设置为2的n次方

构造函数中控制长度必须为2的n次方.

首先在构造方法中, 有下面这段代码, 其中initialCapacity是我们传入的自定义map容量大小(如果不设置, 默认是16)

```
int capacity = 1;
while (capacity < initialCapacity)
    capacity <<= 1
```

initialCapacity = 8, 这样capacity = 1, 要向左移动3次, 刚开始移动之前capacity=1, 根据移位运算, 移动第1次, capacity=2, 移动第2次, capacity=4,移动第3次, capacity=8, 此时会跳出循环, 最终capacity = 8 = 2的3次方<br/>
initialCapacity = 5, 这样capacity = 1, 要向左移动3次, 刚开始移动之前capacity=1, 根据移位运算, 移动第1次, capacity=2, 移动第2次, capacity=4,移动第3次, capacity=8, 此时会跳出循环, 最终capacity = 8 = 2的3次方<br/>
所以同理, 如果输入是5, 6, 7, 8, 最终的capacity=8, 也就是对于某个数A, 找到大于等于A中最小的数B, 并且B=2的n次方 . 因为HashMap中要求Entry数组的长度是2的n次方, 后面我们会知道这种方式的好处 .<br/>
所以在这个地方, 就严格控制了长度必须是2的n次方 .

#### 这种方式好处.

上面说了构造函数中, 限制了capacity必须是2的n次方 . 并且在扩容的时候, 也是直接乘以2进行扩容, 保证还是2的n次方 .

好处 :
这里的h是”int hash = hash(key.hashCode());”, 也就是根据key的hashCode再次进行一次hash操作计算出来的 .length是Entry数组的长度 .<br/>
一般我们利用hash码, 计算出在一个数组的索引, 常用方式是”h % length”, 也就是求余的方式 .<br/>
可能是这种方式效率不高, SUN大师们发现, “当容量一定是2^n时，h & (length - 1) == h % length” . **按位运算特别快** .<br/>

对于length = 16, 对应二进制”1 0000”, length-1=”0 1111”<br/>
假设此时h = 17 .<br/>
(1) 使用”h % length”, 也就是”17 % 16”, 结果是1 .<br/>
(2) 使用”h & (length - 1)”, 也就是 “1 0001 & 0 1111”, 结果也是1 .<br/>
我们会发现, 因为”0 1111”低位都是”1”, 进行”&”操作, 就能成功保留”1 0001”对应的低位, 将高位的都丢弃, 低位是多少, 最后结果就是多少 .<br/>
刚好低位的范围是”0~15”, 刚好是长度为length=16的所有索引 .






