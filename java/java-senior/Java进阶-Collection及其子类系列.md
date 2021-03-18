# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Java进阶知识

### 数据结构
####线性表，链表，哈希表是常用的数据结构，在进行Java开发时，JDK已经为我们提供了一系列相应的类来实现基本的数据结构。这些类均在java.util包中。

* Java集合工具包位于Java.util包下，包含了很多常用的数据结构，如数组、链表、栈、队列、集合、哈希表等。
* 组成：
    * Collection
        * List列表
        * Set集合
    * Map映射
    * 迭代器（Iterator、Enumeration）
    * 工具类（Arrays、Collections）

#### Java集合类的整体框架如下：
![](../../static/java/java_senior_collection.png)
* 集合类主要分为两大类：
    * Collection：继承了超级接口Iterable，是List、Set等集合高度抽象出来的接口，它包含了这些集合的基本操作
        * List: List接口通常表示一个列表（数组、队列、链表、栈等），其中的元素可以重复，常用实现类为ArrayList和LinkedList，另外还有不常用的Vector。另外，LinkedList还是实现了Queue接口，因此也可以作为队列使用。
        * Set: Set接口通常表示一个集合，其中的元素不允许重复（通过hashcode和equals函数保证），常用实现类有HashSet和TreeSet，HashSet是通过Map中的HashMap实现的，而TreeSet是通过Map中的TreeMap实现的。另外，TreeSet还实现了SortedSet接口，因此是有序的集合（集合中的元素要实现Comparable接口，并覆写Compartor函数才行）。
        * 说明：抽象类AbstractCollection、AbstractList和AbstractSet分别实现了Collection、List和Set接口，这就是在Java集合框架中用的很多的适配器设计模式，用这些抽象类去实现接口，在抽象类中实现接口中的若干或全部方法，这样下面的一些类只需直接继承该抽象类，并实现自己需要的方法即可，而不用实现接口中的全部抽象方法。
    * Map
        * Map是一个映射接口，其中的每个元素都是一个key-value键值对，同样抽象类AbstractMap通过适配器模式实现了Map接口中的大部分函数，TreeMap、HashMap、WeakHashMap等实现类都通过继承AbstractMap来实现，另外，不常用的HashTable直接实现了Map接口，它和Vector都是JDK1.0就引入的集合类。

* 迭代器：Iterator是遍历集合的迭代器（不能遍历Map，只用来遍历Collection），Collection的实现类都实现了iterator()函数，它返回一个Iterator对象，用来遍历集合，ListIterator则专门用来遍历List。而Enumeration则是JDK1.0时引入的，作用与Iterator相同，但它的功能比Iterator要少，它只能再Hashtable、Vector和Stack中使用。
* 工具类：Arrays和Collections是用来操作数组、集合的两个工具类，例如在ArrayList和Vector中大量调用了Arrays.Copyof()方法，而Collections中有很多静态方法可以返回各集合类的synchronized版本，即线程安全的版本，当然了，如果要用线程安全的结合类，首选Concurrent并发包下的对应的集合类。

---
#### Collection
1. 是一个最基本的集合接口，也是一个泛型的接口
2. 继承了超级接口Iterable，子接口：List、Set、Queue等
3. 每个Collection的对象包含了一组对象
4. 所有的实现类都有两个构造方法，一个是无参构造方法，第二个是用另外一个Collection对象作为构造方法的参数
5. 遍历Collection使用Iterator迭代器实现
6. retainAll(collection),AddAll(),removeAll(c)分别对应了集合的交并差运算
7. 没有具体的直接实现，但提供了更具体的子接口，如Set、List等

##### Collection简介
* 一个Collection代表一组Object，即Collection的元素（Elements），一些Collection允许相同的元素而另一些不行。一些能排序而另一些不行。
* Java SDK不提供直接继承自Collection的类，Java SDK提供的类都是继承自Collection的“子接口”如List和Set。

##### 如何遍历Collection中的每一个元素？
* 不论Collection的实际类型如何，它都支持一个iterator()的方法，该方法返回一个迭代子，使用该迭代子即可逐一访问Collection中每一个元素，代码如下：
```

	Iterator it = collection.iterator(); // 获得一个迭代子
    while(it.hasNext())　　
    {
        Object obj = it.next(); // 得到下一个元素
    }
```
* 其他方法
    * retainAll(Collection<? extends E\> c)**：保留，交运算  
    * addAll(Collection<? extends E\> c)**：添加，并运算  
    * removeAll(Collection<? extends E\> c)**：移除，减运算  

---
#### List
* 是一个接口
* 继承的接口：Collection
* 间接继承的接口：Iterable
* List是有序的**Collection**
	- 可以对每个元素的插入位置进行精准控制
	- 可以根据索引访问元素
* 和Set接口的最大区别是，List允许重复值，Set不能
* 它的直接实现类有ArrayList，LinkedList，Vector等
* List有自己的迭代器ListIterator，可以通过这个迭代器进行逆序的迭代，以及用迭代器设置元素的值
* 如果元素包含自身，equals()和hashCode()不再是良定义的
* 实现类：ArrayList、LinkedList、Vector等

##### List方法
**boolean add(E e)**: 添加到末尾  
**void add(int index, E e)**: 添加到指定位置  

**E set(int index, E e)**: 设置指定位置的元素,返回一个E  
**get(int index)**: 获得指定位置的元素  

**Iterator iterator()**   
**ListIterator listIterator()**  
**ListIterator listIterator(int index)**  

**int indexOf(E e)**  
**int lastIndexOf(E e)**

**List<E> subList(int fromIndex, int toIndex)**

##### 子类介绍
1. ArrayList
    * 实现的接口：List、Collectin、Iterable、Serializable、Cloneable、RandomAccess
    * 子类：AttributeList、RoleList、RoleUnresolvedList
    * ArrayList是一个顺序表，大小可变。当更多的元素加入到ArrayList中时，其大小将会动态的增长
    * 内部的元素可以直接通过get与set方法进行访问，因为ArrayList本质上就是一个数组
    * 但速度较快，ArrayList相比LinkedList在查找和修改元素上比较快，但是在添加和删除上比LinkedList慢
    * ArrayList和LinkedList一样，不具备线程同步的安全性，Vector线程安全
    * ArrayList、Vector底部都是采用数组实现的
    * 第一次定义的时候没有指定数组的长度则长度是0，在第一次添加的时候判断如果是空则追加10。
    * 扩容机制：ArrayList每次对size增长50%.
2. LinkedList 
    * 是一个双链表
    * 它还是队列、双端队列，可以用作堆栈
    * 和ArrayList一样，不具有线程安全性
    * 在添加和删除元素时具有比ArrayList更好的性能.但在get与set方面弱于ArrayList.当然,这些对比都是指数据量很大或者操作很频繁的情况下的对比,如果数据和运算量很小,那么对比将失去意义。
    * LinkedList还实现了Queue接口，所以它还是一个双端队列，该接口比List提供了更多的方法，包括offer(),peek(),poll等
3. Vector（线程安全）
    * 线程安全
    * 和ArrayList类似,但属于强同步类。
    * ArrayList、Vector默认初始容量为10
    * 如果你的程序本身是线程安全的(thread-safe,没有在多个线程之间共享同一个集合/对象),那么使用ArrayList是更好的选择。
    * 扩容增量：原容量的 1倍，如 Vector的容量为10，一次扩容后是容量为20

#####ArrayList容量是如何变化的
在源码中：

```
private transient Object[] elementData;
```
用于保存对象。它会随着元素的添加而改变大小。在下面的叙述中，容量指的是elementData.length，而不是size。

>size不是容量，ArrayList对象占用的内存不是由size决定的。
>size的大小在每次调用add(E e)方法时加1。
>如果是调用ArrayList(Collection<? extends E> c)构造方法，则size的初始值为c

* 如果在初始化时，没有指定大小，则容量大小为0。
* 当大小为0时，第一次添加元素容量大小变为10。
* 之后每次添加时，会先确保容量是否够用
* 如果不够用，每次增加的容量为 newCapacity = oldCapacity + (oldCapacity >> 1)；即，每次增加为原来的1.5倍。


##### 扩容方法
**void ensureCapacity(int minCapacity)**
设置容量能容纳下minCapacity个元素

---
#### Set
* 是一个泛型接口，继承了接口Collection，元素无序的、不可重复。
* 子接口：NavigableSet、SortedSet
* 子类：EnumSet、HashSet、LinkedHashSet、TreeSet、AbstractSet等
* 两个注意点:
    1. Set中的元素的类，必须有一个有效的equals方法。
    2. 对Set的构造方法，传入的Collection对象中重复的元素会只留下一个

HashSet：线程不安全，存取速度快
　　　　　底层实现是一个HashMap（保存数据），实现Set接口
　　　　　默认初始容量为16（为何是16，见下方对HashMap的描述）
　　　　　加载因子为0.75：即当 元素个数 超过 容量长度的0.75倍 时，进行扩容
　　　　　扩容增量：原容量的 1 倍，如 HashSet的容量为16，一次扩容后是容量为32


#### Map
HashMap：默认初始容量为16
　　　　　（为何是16：16是2^4，可以提高查询效率，另外，32=16<<1       -->至于详细的原因可另行分析，或分析源代码）
　　　　　加载因子为0.75：即当 元素个数 超过 容量长度的0.75倍 时，进行扩容
　　　　　扩容增量：原容量的 1 倍，如 HashSet的容量为16，一次扩容后是容量为32
    
---
#### Queue
* 是个队列,也是是一个泛型接口
* 父接口：Collection
* 子接口：Deque
* 插入、提取、检查操作都存在两种形式，一种在操作失败后返回特殊值，一种抛出异常
#### Queue方法
1. boolean add(E e)和 boolean offer(E e)添加元素
失败时，add()抛出异常，offer()返回false
2. E element()和E peek()获取但不移除
失败时，element抛出异常，peek()返回null
3. E remove()和E poll()获取并移除
失败时，remove()抛出异常，poll()返回null

#### Queue子类介绍
* Deque
    * 双端队列
    * 可以实现队列。也可以用作栈



