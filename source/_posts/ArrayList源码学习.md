---
title: ArrayList源码学习
date: 2020-03-26 12:06:27
categories: 源码学习
tags:
  - Java
  - ArrayList
---

# ArrayList源码学习

### ArrayList的构造函数

- 默认的构造函数

  ```java
  /**
       * Constructs an empty list with an initial capacity of ten.
       */
      private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
  
      public ArrayList() {
          this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
      }
  ```

  由上述代码可知，当我们创建一个`ArrayList`时，**如果不传入参数，会使用初始容量10构造一个空数组，只有当真正对数组进行添加元素操作时才会分配容量。即向数组中添加第一个元素时，数组容量扩容为10.**

  <!--more-->

- 传入初始容量的构造函数

  ```java
    /**
       * Constructs an empty list with the specified initial capacity.
       *
       * @param  initialCapacity  the initial capacity of the list
       * @throws IllegalArgumentException if the specified initial capacity
       *         is negative
       */
      transient Object[] elementData; // non-private to simplify nested class access
  
      public ArrayList(int initialCapacity) {
          if (initialCapacity > 0) {
              this.elementData = new Object[initialCapacity];
          } else if (initialCapacity == 0) {
              this.elementData = EMPTY_ELEMENTDATA;
          } else {
              throw new IllegalArgumentException("Illegal Capacity: "+
                                                 initialCapacity);
          }
      }
  ```

  传入容量大于0时，创建`initialCapacity`大小的数组；传入容量等于0时，创建空数组；小于0时抛出异常。

- 传入集合的构造函数

  ```java
     /**
       * Constructs a list containing the elements of the specified
       * collection, in the order they are returned by the collection's
       * iterator.
       *
       * @param c the collection whose elements are to be placed into this list
       * @throws NullPointerException if the specified collection is null
       */
      public ArrayList(Collection<? extends E> c) {
          elementData = c.toArray();
          if ((size = elementData.length) != 0) {
              // c.toArray might (incorrectly) not return Object[] (see 6260652)
              if (elementData.getClass() != Object[].class)
                  elementData = Arrays.copyOf(elementData, size, Object[].class);
          } else {
              // replace with empty array.
              this.elementData = EMPTY_ELEMENTDATA;
          }
      }
  ```

  构造包含指定collection元素的列表，这些元素利用该集合的迭代器按顺序返回，如果指定的集合为`null`，抛出`throws NullPointerException`。



## ArrayList扩容机制

以无参数构造函数为例。

1. **`add`方法**

   ```java
      /**
        * Appends the specified element to the end of this list.
        *
        * @param e element to be appended to this list
        * @return <tt>true</tt> (as specified by {@link Collection#add})
        */
       public boolean add(E e) {
           ensureCapacityInternal(size + 1);  // Increments modCount!!
           elementData[size++] = e;
           return true;
       }
   ```

   `add`方法将指定的元素添加到列表的末尾，添加元素之前先调用`ensureCapacityInternal`方法，再为数组赋值。

2. **`ensureCapacityInternal`方法**

   ```java
       //获取最小需要容量
   	private void ensureCapacityInternal(int minCapacity) {
           if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
               minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
           }
   
           ensureExplicitCapacity(minCapacity);
       }
   ```

   `add`方法调用了`ensureCapacityInternal(size+1)`方法，**当要add第一个元素时，`minCapacity=1`，在`Math.max()`方法比较后，`minCapacity=10`。**

3. **`ensureExplicitCapacity`方法**

   ```java
       //判断是否需要扩容
       private void ensureExplicitCapacity(int minCapacity) {
           modCount++;
   
           // overflow-conscious code
           if (minCapacity - elementData.length > 0)
               grow(minCapacity);
       }
   ```

   当最小需要容量大于数组长度时，需要扩容。

   - 当我们`add`第一个元素进`ArrayList`时，`elementData.length=0`，但执行了`ensureCapacityInternal`方法后`minCapacity=10`，所以会执行`grow(minCapacity)`方法，数组扩容为10；
   - 当`add`第二个元素时，传入的`minCapacity=size+1=2`，而`elementData.length=10`，所以不会调用`grow`方法。
   - 当`add`第三个、第四个、...第十个元素时，依然不会执行`grow`方法
   - 直到`add`第十一个元素时，`minCapacity=size+1=11`，`elementData.length = 10`，所以会进行扩容。

4. **`grow`方法**

   ```java
        /**
        * 要分配的最大数组大小
        */
       private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;    
        /**
        * Increases the capacity to ensure that it can hold at least the
        * number of elements specified by the minimum capacity argument.
        *
        * @param minCapacity the desired minimum capacity
        */
       private void grow(int minCapacity) {
           // overflow-conscious code
           int oldCapacity = elementData.length;
           int newCapacity = oldCapacity + (oldCapacity >> 1);
           if (newCapacity - minCapacity < 0)
               newCapacity = minCapacity;
           if (newCapacity - MAX_ARRAY_SIZE > 0)
               newCapacity = hugeCapacity(minCapacity);
           // minCapacity is usually close to size, so this is a win:
           elementData = Arrays.copyOf(elementData, newCapacity);
       }
   ```

   `int newCapacity = oldCapacity + (oldCapacity >> 1)`，所以新容量是旧容量的1.5倍左右，(`oldCapacity`为偶数时为1.5倍，为奇数时为1.5倍左右)。

   > ">>"（移位运算符）：>>1 右移一位相当于除2，右移n位相当于除以 2 的 n 次方。这里 oldCapacity 明显右移了1位所以相当于oldCapacity /2。对于大数据的2进制运算,位移运算符比那些普通运算符的运算要快很多,因为程序仅仅移动一下而已,不去计算,这样提高了效率,节省了资源 　

   - 当`add`第一个元素时，`oldCapacity=0`，第一个判断成立，`newCapacity = minCapacity = 10`，第二个判断不成立，不会进入`hugeCapacity`方法。数组容量为10，`size=1`，添加成功。
   - 当`add`第11个元素时，`oldCapacity=10`，`newCapacity=15`，第一个判断不成立，第二个判断也不成立。数组容量扩为15，`size=11`，添加成功。
   - ......

5. **`hugeCapacity`方法**

   ```java
   	    /**
        * The maximum size of array to allocate.
        * Some VMs reserve some header words in an array.
        * Attempts to allocate larger arrays may result in
        * OutOfMemoryError: Requested array size exceeds VM limit
        */
       private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;    
   	private static int hugeCapacity(int minCapacity) {
           if (minCapacity < 0) // overflow
               throw new OutOfMemoryError();
           return (minCapacity > MAX_ARRAY_SIZE) ?
               Integer.MAX_VALUE :
               MAX_ARRAY_SIZE;
       }
   ```

   在`grow`方法中，若`newCapacity>MAX_ARRAY_SIZE`就会执行`hugeCapacity`来比较`minCapacity`和`MAX_ARRAY_SIZE`，若大于，则新容量为`Integer.MAX_vALUE`，否则为`MAX_ARRAY_SIZE=Integer.MAX_VALUE-8`。



#### `System.arraycopy()`与`Arrays.copyOf()`方法

阅读源码后，可以发现`ArrayList`中大量使用了这两个方法。

1. `System.arraycopy()`方法

   ```java
          /**
          *在指定位置插入集合中的所有元素
          *
          /
   	public boolean addAll(int index, Collection<? extends E> c) {
           rangeCheckForAdd(index);
   
           Object[] a = c.toArray();
           int numNew = a.length;
           ensureCapacityInternal(size + numNew);  // Increments modCount
   
           int numMoved = size - index;
           if (numMoved > 0)
               System.arraycopy(elementData, index, elementData, index + numNew,
                                numMoved);
   
           System.arraycopy(a, 0, elementData, index, numNew);
           size += numNew;
           return numNew != 0;
       }
   ```

   `System.arraycopy(a, 0, elementData, index, numNew)`中`a`为源数组，`0`为源数组起始位置，`elementData`为目标数组，`index`为目标数组中的起始位置，`numNew`为要复制的数组元素的数量。

2. `Arrays.copyOf()`方法

   ```java
      /**
        以正确的顺序返回一个包含此列表中所有元素的数组（从第一个到最后一个元素）; 返回的数组的运行时类型是指定数组的运行时类型。 
        */
       public Object[] toArray() {
       //elementData：要复制的数组；size：要复制的长度
           return Arrays.copyOf(elementData, size);
       }
   ```

3. 区别

   `arraycopy()` 需要目标数组，将原数组拷贝到你自己定义的数组里或者原数组，而且可以选择拷贝的起点和长度以及放入新数组中的位置 `copyOf()` 是系统自动在内部新建一个数组，并返回该数组。 



### **`ensureCapacity`方法**

```java
    /**
    如有必要，增加此 ArrayList 实例的容量，以确保它至少可以容纳由minimum capacity参数指定的元素数。
     *
     * @param   minCapacity   所需的最小容量
     */
    public void ensureCapacity(int minCapacity) {
        int minExpand = (elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
            // any size if not default element table
            ? 0
            // larger than default for default empty table. It's already
            // supposed to be at default size.
            : DEFAULT_CAPACITY;

        if (minCapacity > minExpand) {
            ensureExplicitCapacity(minCapacity);
        }
    }
```

**最好在 add 大量元素之前用 ensureCapacity 方法，以减少增量重新分配的次数** 

