---
title: HashMap源码学习
date: 2020-04-07 11:39:59
categories: 源码学习
tags:
  - Java
  - HashMap
---

# HashMap源码学习

### HashMap集合简介

`HashMap`的类定义如下：

```java
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable 
```

`HashMap`继承自`AbstractMap`抽象类，实现了`Map`接口、`Cloneable`接口和`Serializable`接口。

`    HashMap`是基于哈希表的`Map`接口实现，是以`key-value`存储形式存在，主要用来存储键值对。`HashMap`的实现不是同步的，即意味着不是线程安全的。`HashMap`的键、值都可以为`null`。此外，`HashMap`的映射不是有序的。

  JDK1.8以前，`HashMap`由数组+链表组成，数组是`HashMap`的主体，链表则是为了解决哈希冲突(**两个对象调用的`hashCode()`方法计算得到的哈希值一致导致计算的数组索引相同**)，(**使用“拉链法”解决冲突**)。

JDK1.8以后解决哈希冲突时发生了较大变化。**当链表长度大于阈值(或者红黑树边界值，默认为8)并且当前数组长度大于64时，此时此索引上的所有数据采用红黑树存储。**

Map接口继承关系如下：

![Map接口继承关系.png](https://f1bu920.github.io/images/Map接口继承关系.png)

<!--more-->

补充：将链表转为红黑树时会进行判断，当链表长度大于阈值但数组长度小于64时，并不会转化为红黑树，而是对数组进行扩容。这样做的目的是因为数组较小，尽量避开红黑树结构，强行转化为红黑树反而会降低效率。，因为红黑树需要进行左旋、右旋、变色等操作来保持平衡。具体参考`treeifyBin()`方法。

```java
static final int MIN_TREEIFY_CAPACITY = 64;
    /**
     * Replaces all linked nodes in bin at index for given hash unless
     * table is too small, in which case resizes instead.
     */
    final void treeifyBin(Node<K,V>[] tab, int hash) {
        int n, index; Node<K,V> e;
        //判断数组长度小于64时采用扩容
        if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
            resize();
        else if ((e = tab[index = (n - 1) & hash]) != null) {
            TreeNode<K,V> hd = null, tl = null;
            do {
                TreeNode<K,V> p = replacementTreeNode(e, null);
                if (tl == null)
                    hd = p;
                else {
                    p.prev = tl;
                    tl.next = p;
                }
                tl = p;
            } while ((e = e.next) != null);
            if ((tab[index] = hd) != null)
                hd.treeify(tab);
        }
    }
```

特点：

1. 存取无序
2. 键和值都可以为`null`，但键位置只能有一个`null`
3. 键位置是惟一的，底层的数据结构控制键的
4. **jdk1.8以前数据结构是数组+链表，1.8之后是数组+链表+红黑树**
5. **阈值大于8且数组长度大于64时，才会将链表转换为红黑树**



### HashMap集合底层数据结构

`HashMap`底层数据结构：**jdk1.8以前数据结构是数组+链表，1.8之后是数组+链表+红黑树**。

```java
HashMap hashMap = new HashMap();
```

在jdk1.8之前，上面这行代码会直接创建一个默认长度为16，类型为`Map.Entry`的数组`table[]`；

而在**jdk1.8之后，是在第一次调用`put`方法时才会创建数组`Node[] table`，这与`ArrayList`不指定初始容量时一致**。

**`HashMap`通过`key`的`hashCode`经过扰动函数处理过后得到`hash`值，然后通过`(n-1)&hash`计算当前元素存放位置(n指数组长度)，如果当前位置存在元素，就比较已存在元素和要存放元素的`key`的`hash`值是否一致，如果不一致，就采用拉链法解决冲突，如果一致，就会发生哈希碰撞，然后会调用`key`所属类的`equals()`方法，如果相等，直接覆盖，如果不相等，遍历链表，直到找到相等的，否则采用拉链法新建一个节点存储数据。**

注：不同`key`的`hashCode()`方法得到的值可能会相等。例：

```java
public class Test {
    public static void main(String[] args) {
        System.out.println("重地".hashCode());
        System.out.println("通话".hashCode());
    }
}
//结果为：
//1179395
//1179395
```

注：**从上述过程可以看出，一个类中`hashCode()`和`equals()`方法一定要一致。**

这里扰动函数就是`HashMap`的`hash()`方法，使用`hash`方法就是为了防止一些实现比较差的`hashCode()`方法从而减少碰撞。



### HashMap集合类的成员

#### 成员变量

1. 序列化版本号

   ```java
   private static final long serialVersionUID = 362498820763181265L;
   ```

   `HashMap`实现了`Serializable`接口。

2. 集合的初始化容量(**必须为2的n次幂**)

   ```java
       /**
        * The default initial capacity - MUST be a power of two.
        */
       static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
   ```

   默认的初始化容量必须为2的n次幂，是**为了让`HashMap`存取高效，尽量减少碰撞，也就是尽量把数据分配均匀。**

   **根据`key`计算得到的`hash`值数值很大，不能当成内存地址，将其转化为数组下标的计算方法为`(n-1)&hash`。所以`HashMap`的长度必须为2的n次幂。**

   **因为取余操作中如果除数是2的幂次则等价于与其除数减一的与(&)操作，即`hash%length == hash&(length-1)`的前提是`length`是2的n次幂。而计算机中采用二进制操作&效率远远大于%，所以`HashMap`的长度必须为2的n次幂。**

   **如果我们传入的初始容量`initCapacity`不为2的n次幂，`HashMap`会自动将容量扩为比`initCapacity`大的最近的2的n次幂**。这个过程是通过调用`tableSizeFor(initCapacity)`方法实现的。源码如下：

   ```java
       /**
        * Returns a power of two size for the given target capacity.
        */
       static final int tableSizeFor(int cap) {
           //为了防止传入的容量恰好为2的n次幂，所以减1
           int n = cap - 1;
           n |= n >>> 1;
           n |= n >>> 2;
           n |= n >>> 4;
           n |= n >>> 8;
           n |= n >>> 16;
           return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
       }
   ```

 3. 默认的负载因子, 默认值为0.75

   ```java
       /**
        * The load factor used when none specified in constructor.
        */
       static final float DEFAULT_LOAD_FACTOR = 0.75f;
   ```

   当元素个数达到数组长度*负载因子时，就会扩容。例：当长度为16的数组中有12个元素时，就会触发扩容。

4. 集合最大容量

   ```java
       /**
        * The maximum capacity, used if a higher value is implicitly specified
        * by either of the constructors with arguments.
        * MUST be a power of two <= 1<<30.
        */
       static final int MAXIMUM_CAPACITY = 1 << 30;
   ```

5. **当链表元素个数超过8就会转为红黑树(1.8新增)**

   ```java
       /**
        * The bin count threshold for using a tree rather than list for a
        * bin.  Bins are converted to trees when adding an element to a
        * bin with at least this many nodes. The value must be greater
        * than 2 and should be at least 8 to mesh with assumptions in
        * tree removal about conversion back to plain bins upon
        * shrinkage.
        *当桶(bucket)上结点数大于这个值时才会转为红黑树
        */
       static final int TREEIFY_THRESHOLD = 8;
   ```

   *为什么是8？*

   > 红黑树的节点占用空间比链表节点大，所以我们只在包含足够的节点时 (链表长度大于8)才使用树节点。当他们变得太小时 (链表长度小于6) 就会变成普通节点。根据统计学中的泊松分布，长度为8时概率很小，所以这是为了权衡时间与空间！

6. **当链表元素小于6就会从红黑树转为普通链表**

   ```java
       /**
        * The bin count threshold for untreeifying a (split) bin during a
        * resize operation. Should be less than TREEIFY_THRESHOLD, and at
        * most 6 to mesh with shrinkage detection under removal.
        */
       static final int UNTREEIFY_THRESHOLD = 6;
   ```

7. **桶中结构转化为红黑树对应的数组长度最小值**

   ```java
       /**
        * The smallest table capacity for which bins may be treeified.
        * (Otherwise the table is resized if too many nodes in a bin.)
        * Should be at least 4 * TREEIFY_THRESHOLD to avoid conflicts
        * between resizing and treeification thresholds.
        */
       static final int MIN_TREEIFY_CAPACITY = 64;
   ```

8. **存储元素的数组table**

   ```java
       /**
        * The table, initialized on first use, and resized as
        * necessary. When allocated, length is always a power of two.
        * (We also tolerate length zero in some operations to allow
        * bootstrapping mechanics that are currently not needed.)
        */
       transient Node<K,V>[] table;
   ```

   jdk1.8之前`table`是`Map.Entry`类型，1.8之后为`Node`类型。

9. 用于存放缓存

   ```java
       /** 存放具体元素的集合
        * Holds cached entrySet(). Note that AbstractMap fields are used
        * for keySet() and values().
        */
       transient Set<Map.Entry<K,V>> entrySet;
   ```

10. **HashMap中存放元素的个数**

  ```java
      /**
       * The number of key-value mappings contained in this map.
       */
      transient int size;
  ```

11. 用来记录HashMap的修改次数

    ```java
        /**
         * The number of times this HashMap has been structurally modified
         * Structural modifications are those that change the number of mappings in
         * the HashMap or otherwise modify its internal structure (e.g.,
         * rehash).  This field is used to make iterators on Collection-views of
         * the HashMap fail-fast.  (See ConcurrentModificationException).
         */
        transient int modCount;
    ```

12. 用来调整大小下一个容量的计算方式为(容量*负载因子)

    ```java
    //临界值 当实际大小超过临界值(容量*负载因子)时，会扩容
    int threshold;
    ```

    **是衡量数组容量是否需要扩容的标准**。扩容容量是原来的2倍。

13. **哈希表的加载因子**

    ```java
        /**
         * The load factor for the hash table.
         * @serial
         */
        final float loadFactor;
    ```

    1. `loadFactor`用来衡量`HashMap`满的程度，**表示`HashMap`疏密程度，影响`hash`操作到同一数组位置的概率**，计算`HashMap`的实时加载因子的方法为`size / capacity`，而不是占用桶的数量除以`capacity`。`capacity`是桶的数量，也就是数组长度。

       **`loadFactor`太大会导致元素查找效率低，太小会导致数组空间浪费。默认值0.75f是较好的临界值。**

       **当`HashMap`中容纳的元素达到数组长度的75%时，表示`HashMap`已经太挤了，需要扩容，而扩容涉及到`rehash`、复制数据等非常消耗性能的操作，所以要尽可能的减少扩容次数，可以通过指定初始容量来尽量避免。同时也可以指定`loadFactor`。**

       构造一个指定初始容量和加载因子的空`HashMap`。

#### 构造方法

- 构造一个空的`HashMap`，默认初始容量为16，默认加载因子为0.75f

  ```java
      /**
       * Constructs an empty <tt>HashMap</tt> with the default initial capacity
       * (16) and the default load factor (0.75).
       */
      public HashMap() {
          this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
      }
  ```

- 构造一个指定初始容量和默认加载因子的空`HashMap`

  ```java
      /**
       * Constructs an empty <tt>HashMap</tt> with the specified initial
       * capacity and the default load factor (0.75).
       *
       * @param  initialCapacity the initial capacity.
       * @throws IllegalArgumentException if the initial capacity is negative.
       */
      public HashMap(int initialCapacity) {
          this(initialCapacity, DEFAULT_LOAD_FACTOR);
      }
  ```

  尽量使用这个构造方法，为了减少扩容。指定的初始容量必须为2的n次幂，如果不为，会调用`tableSizeFor`方法，使初始容量变为比指定值大的最小2的n次幂。


- 构造一个指定初始容量和负载因子的空`HashMap`

  ```java
      /**
       * Constructs an empty <tt>HashMap</tt> with the specified initial
       * capacity and load factor.
       *
       * @param  initialCapacity the initial capacity
       * @param  loadFactor      the load factor
       * @throws IllegalArgumentException if the initial capacity is negative
       *         or the load factor is nonpositive
       */
      public HashMap(int initialCapacity, float loadFactor) {
          if (initialCapacity < 0)
              throw new IllegalArgumentException("Illegal initial capacity: " +
                                                 initialCapacity);
          if (initialCapacity > MAXIMUM_CAPACITY)
              initialCapacity = MAXIMUM_CAPACITY;
          if (loadFactor <= 0 || Float.isNaN(loadFactor))
              throw new IllegalArgumentException("Illegal load factor: " +
                                                 loadFactor);
          this.loadFactor = loadFactor;
          this.threshold = tableSizeFor(initialCapacity);
      }
  ```

- 包含另一个“Map”的构造方法

  ```java
      /**
       * Constructs a new <tt>HashMap</tt> with the same mappings as the
       * specified <tt>Map</tt>.  The <tt>HashMap</tt> is created with
       * default load factor (0.75) and an initial capacity sufficient to
       * hold the mappings in the specified <tt>Map</tt>.
       *
       * @param   m the map whose mappings are to be placed in this map
       * @throws  NullPointerException if the specified map is null
       */
  	//构造一个映射关系与指定“Map”相同的 HashMap
      public HashMap(Map<? extends K, ? extends V> m) {
          this.loadFactor = DEFAULT_LOAD_FACTOR;
          putMapEntries(m, false);
      }
  ```

  最后调用了`putMapEntries`方法，我们来看一下它的源码：

  ```java
      /**
       * Implements Map.putAll and Map constructor
       *
       * @param m the map
       * @param evict false when initially constructing this map, else
       * true (relayed to method afterNodeInsertion).
       */
      final void putMapEntries(Map<? extends K, ? extends V> m, boolean evict) {
          //获取参数集合的长度
          int s = m.size();
          if (s > 0) {
              if (table == null) { // pre-size
                  //这里+1是为了防止扩容的发生，使容量尽量大点，更大的容量会减少resize调用的次数
                  float ft = ((float)s / loadFactor) + 1.0F;
                  int t = ((ft < (float)MAXIMUM_CAPACITY) ?
                           (int)ft : MAXIMUM_CAPACITY);
                  if (t > threshold)
                      threshold = tableSizeFor(t);
              }
              else if (s > threshold)
                  resize();
              for (Map.Entry<? extends K, ? extends V> e : m.entrySet()) {
                  K key = e.getKey();
                  V value = e.getValue();
                  putVal(hash(key), key, value, false, evict);
              }
          }
      }
  ```



#### 常用成员方法

##### 确定哈希桶数组索引位置

```java
    static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
```

`HashMap`中利用`hash`方法得到`key的`散列码，再通过`hash&(n-1)`得到哈希桶数组的索引位置。

注意，**因为`HashMap`底层数组的长度总为2的n次幂，所以`hash&(n-1)`就等价于对`length`取模，而在计算机中`&`比`%`效率更好。**

##### 增加方法(put)

1. 判断键值对数组`table[i]`是否为空或为`null`，是就执行`resize()`进行扩容；
2. 根据`(n - 1) & hash`得到插入的数组`i`，如果此位置为空，直接插入新节点，转到步骤6；如果此位置已有元素，发生碰撞，执行步骤3。
3. 判断`table[i]`的首个元素是否和`key`一样，如果相同直接覆盖`value`，否则转向④，这里的相同指的是`hashCode`以及`equals`； 
4. 解决哈希冲突，如果当前`table[i]`已经是`treeNode`，即已经是红黑树结构，则直接在树中插入键值对，否则转向5；
5. 此时可以确定是链表结构了，遍历整个链表，判断链表长度是否大于8，如果是将链表转化为红黑树，在红黑树中执行插入；遍历过程中若发现`key`已经存在，直接覆盖即可。
6. 插入成功后，判断实际存在的键值对数量是否多于最大容量`threshold`，如果是，进行扩容。

`put`方法源码如下：

```java

  public V put(K key, V value) {
      // 对key的hashCode()做hash
      return putVal(hash(key), key, value, false, true);
  }
  
  final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                 boolean evict) {
      Node<K,V>[] tab; Node<K,V> p; int n, i;
      // 步骤①：tab为空则创建
     if ((tab = table) == null || (n = tab.length) == 0)
         n = (tab = resize()).length;
     // 步骤②：计算index，并对null做处理 
     if ((p = tab[i = (n - 1) & hash]) == null) 
         tab[i] = newNode(hash, key, value, null);
     else {
         Node<K,V> e; K k;
         // 步骤③：节点key存在，直接覆盖value
         if (p.hash == hash &&
             ((k = p.key) == key || (key != null && key.equals(k))))
             e = p;
         // 步骤④：判断该链为红黑树
         else if (p instanceof TreeNode)
             e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
         // 步骤⑤：该链为链表
         else {
             for (int binCount = 0; ; ++binCount) {
                 if ((e = p.next) == null) {
                     p.next = newNode(hash, key,value,null);
                        //链表长度大于8转换为红黑树进行处理
                     if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st  
                         treeifyBin(tab, hash);
                     break;
                 }
                    // key已经存在直接覆盖value
                 if (e.hash == hash &&
                     ((k = e.key) == key || (key != null && key.equals(k)))) 
							break;
                 p = e;
             }
         }
         
         if (e != null) { // existing mapping for key
             V oldValue = e.value;
             if (!onlyIfAbsent || oldValue == null)
                 e.value = value;
             afterNodeAccess(e);
             return oldValue;
         }
     }

     ++modCount;
     // 步骤⑥：超过最大容量 就扩容
     if (++size > threshold)
         resize();
     afterNodeInsertion(evict);
     return null;
 }      
```

##### 扩容方法

![HashMap扩容.png](https://f1bu920.github.io/images/HashMap扩容.png)

`HashMap`扩容后长度会变为原来两倍，而且**元素的位置要么在原位置，要么在原位置再移动2次幂的位置。**

元素在重新计算hash之后，因为n变为2倍，那么n-1的mask范围在高位多1bit。因此，我们在扩充HashMap的时候，不需要像JDK1.7的实现那样重新计算hash，只需要看看原来的hash值新增的那个bit是1还是0就好了，是0的话索引没变，是1的话索引变成“原索引+oldCap” 。上图为16扩容成32的`resize`示意图。

JDK1.7中rehash的时候，旧链表迁移新链表的时候，如果在新表的数组索引位置相同，则链表元素会倒置，但是从上图可以看出，JDK1.8不会倒置。 

JDK1.8中`resize`方法的源码如下：

```java
    final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold;
        int newCap, newThr = 0;
        if (oldCap > 0) {
            // 超过最大值就不再扩充了，就只好随你碰撞去吧
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            // 没超过最大值，就扩充为原来的2倍
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold
        }
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        else {               // zero initial threshold signifies using defaults
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        // 计算新的resize上限
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
            Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
        if (oldTab != null) {
            // 把每个bucket都移动到新的buckets中
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        // 链表优化重hash的代码块
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            // 原索引
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            // 原索引+oldCap
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                         // 原索引放到bucket里
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        // 原索引+oldCap放到bucket里
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
```



##### get方法

```java
    public V get(Object key) {
        Node<K,V> e;
        return (e = getNode(hash(key), key)) == null ? null : e.value;
    }

    final Node<K,V> getNode(int hash, Object key) {
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (first = tab[(n - 1) & hash]) != null) {
            if (first.hash == hash && // always check first node
                ((k = first.key) == key || (key != null && key.equals(k))))
                return first;
            if ((e = first.next) != null) {
                if (first instanceof TreeNode)
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                do {
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        return null;
    }
```

`get`方法会传递`key`的散列码到`getNode`方法中，再根据`(n - 1) & hash`得到桶数组中的下标，如果此位置为空直接返回`null`，否则判断第一个节点的`key`是否等于要查找的`key`，如果相等返回结果，否则判断该结点是否为`TreeNode`树节点，是则按照红黑树查找，不是则按照链表方式查找。

### 多线程场景下的HashMap

在多线程使用场景中，应该尽量避免使用线程不安全的`HashMap`，而使用线程安全的`ConcurrentHashMap`。因为在并发的情况下，使用`HashMap`有可能造成死循环。 