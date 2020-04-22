---
title: LinkedList源码学习
date: 2020-03-29 12:54:59
categories: 源码学习
tags:
  - Java
  - LinkedList
---

# LinkedList源码学习

## 内部结构

`LinkedList`是Java集合中`List`接口的一个重要实现类，其底层使用的是双向链表，所以不支持随机访问。基本结点定义如下：

```java
    private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```

`LinkedList`类实现了`List`接口和`Deque`接口，所以可以当成一个队列使用。定义如下：

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
    transient int size = 0;
{
    /**
     * Pointer to first node.
     * Invariant: (first == null && last == null) ||
     *            (first.prev == null && first.item != null)
     */
    transient Node<E> first;

    /**
     * Pointer to last node.
     * Invariant: (first == null && last == null) ||
     *            (last.next == null && last.item != null)
     */
    transient Node<E> last;
    
    ......
        
}
```

<!--more-->

## 源码分析

#### LinkedList构造器

- 默认构造器

  ```java
      /**
       * Constructs an empty list.
       */
      public LinkedList() {
      }
  ```

- 传入集合的构造器

  ```java
      /**
       * Constructs a list containing the elements of the specified
       * collection, in the order they are returned by the collection's
       * iterator.
       *
       * @param  c the collection whose elements are to be placed into this list
       * @throws NullPointerException if the specified collection is null
       */
      public LinkedList(Collection<? extends E> c) {
          this();
          addAll(c);
      }
  ```

  构造一个列表包含传入集合的所有元素，且按集合迭代器遍历的顺序构造。

  其中调用的`addAll(c)`方法如下：

  ```java
      /**
       * Appends all of the elements in the specified collection to the end of
       * this list, in the order that they are returned by the specified
       * collection's iterator.  The behavior of this operation is undefined if
       * the specified collection is modified while the operation is in
       * progress.  (Note that this will occur if the specified collection is
       * this list, and it's nonempty.)
       *
       * @param c collection containing elements to be added to this list
       * @return {@code true} if this list changed as a result of the call
       * @throws NullPointerException if the specified collection is null
       */
      public boolean addAll(Collection<? extends E> c) {
          return addAll(size, c);
      }
  ```

  这会调用与其构成重载的`addAll(int index,Collection<? extends E> c)`方法，具体如下：

  ```java
      /**
       * Inserts all of the elements in the specified collection into this
       * list, starting at the specified position.  Shifts the element
       * currently at that position (if any) and any subsequent elements to
       * the right (increases their indices).  The new elements will appear
       * in the list in the order that they are returned by the
       * specified collection's iterator.
       *
       * @param index index at which to insert the first element
       *              from the specified collection
       * @param c collection containing elements to be added to this list
       * @return {@code true} if this list changed as a result of the call
       * @throws IndexOutOfBoundsException {@inheritDoc}
       * @throws NullPointerException if the specified collection is null
       */
      public boolean addAll(int index, Collection<? extends E> c) {
          //检查index合法性
          checkPositionIndex(index);
  
          Object[] a = c.toArray();
          int numNew = a.length;
          if (numNew == 0)
              return false;
          //succ为第index个位置的节点，pred为succ的前一个节点
          Node<E> pred, succ;
          if (index == size) {
              succ = null;
              pred = last;
          } else {
              succ = node(index);
              pred = succ.prev;
          }
  
          //按集合迭代顺序添加
          for (Object o : a) {
              @SuppressWarnings("unchecked") E e = (E) o;
              Node<E> newNode = new Node<>(pred, e, null);
              if (pred == null)
                  first = newNode;
              else
                  pred.next = newNode;
              pred = newNode;
          }
  
          //将末尾元素连接起来
          if (succ == null) {
              last = pred;
          } else {
              pred.next = succ;
              succ.prev = pred;
          }
  
          //更新size
          size += numNew;
          modCount++;
          return true;
      }
  ```



#### List操作

##### 添加

- 添加头元素

  ```java
      /**
       * Links e as first element.
       对用户不可见
       */
      private void linkFirst(E e) {
          final Node<E> f = first;
          final Node<E> newNode = new Node<>(null, e, f);
          first = newNode;
          if (f == null)
              last = newNode;
          else
              f.prev = newNode;
          size++;
          modCount++;
      }
  
  
      /**
       * Inserts the specified element at the beginning of this list.
       *对用户可见
       * @param e the element to add
       */
      public void addFirst(E e) {
          linkFirst(e);
      }
  ```

- 添加尾元素

  ```java
      /**
       * Links e as last element.
       *对用户不可见
       */
      void linkLast(E e) {
          final Node<E> l = last;
          final Node<E> newNode = new Node<>(l, e, null);
          last = newNode;
          if (l == null)
              first = newNode;
          else
              l.next = newNode;
          size++;
          modCount++;
      }
  
      /**
       * Appends the specified element to the end of this list.
       *对用户可见
       * <p>This method is equivalent to {@link #add}.
       *
       * @param e the element to add
       */
      public void addLast(E e) {
          linkLast(e);
      }
  ```

- 在某个非空节点前插入元素

  ```java
      /**
       * Inserts element e before non-null Node succ.
       */
      void linkBefore(E e, Node<E> succ) {
          // assert succ != null;
          final Node<E> pred = succ.prev;
          final Node<E> newNode = new Node<>(pred, e, succ);
          succ.prev = newNode;
          if (pred == null)
              first = newNode;
          else
              pred.next = newNode;
          size++;
          modCount++;
      }
  ```



##### 查询

- 查询头节点与尾节点

  ```java
      /**
       * Returns the first element in this list.
       *
       * @return the first element in this list
       * @throws NoSuchElementException if this list is empty
       */
      public E getFirst() {
          final Node<E> f = first;
          if (f == null)
              throw new NoSuchElementException();
          return f.item;
      }
  
      /**
       * Returns the last element in this list.
       *
       * @return the last element in this list
       * @throws NoSuchElementException if this list is empty
       */
      public E getLast() {
          final Node<E> l = last;
          if (l == null)
              throw new NoSuchElementException();
          return l.item;
      }
  ```

- 根据索引获取元素

  ```java
      public E get(int index) {
          checkElementIndex(index);
          return node(index).item;
      }
  ```

  **其中`node(index)`根据下标进行二分搜索，尽管`LinkedList`进行了优化，但尽量不要使用随机访问的方法**。

  ```java
      /**
       * Returns the (non-null) Node at the specified element index.
       */
      Node<E> node(int index) {
          // assert isElementIndex(index);
  
          if (index < (size >> 1)) {
              Node<E> x = first;
              for (int i = 0; i < index; i++)
                  x = x.next;
              return x;
          } else {
              Node<E> x = last;
              for (int i = size - 1; i > index; i--)
                  x = x.prev;
              return x;
          }
      }
  ```

  



##### 删除

- 删除头结点与尾节点

  ```java
      /**
       * Removes and returns the first element from this list.
       *
       * @return the first element from this list
       * @throws NoSuchElementException if this list is empty
       */
      public E removeFirst() {
          final Node<E> f = first;
          if (f == null)
              throw new NoSuchElementException();
          return unlinkFirst(f);
      }
  
      /**
       * Removes and returns the last element from this list.
       *
       * @return the last element from this list
       * @throws NoSuchElementException if this list is empty
       */
      public E removeLast() {
          final Node<E> l = last;
          if (l == null)
              throw new NoSuchElementException();
          return unlinkLast(l);
      }
  ```



#### 队列操作

- 查看首元素，如果队首为空不会抛出异常

  ```java
      public E peek() {
          final Node<E> f = first;
          return (f == null) ? null : f.item;
      }
  
  //查看队尾元素
      public E peekLast() {
          final Node<E> l = last;
          return (l == null) ? null : l.item;
      }
  ```

- 查看首元素，如果队首为空会抛出异常

  ```java
  public E element() {
     return getFirst();
  }
  ```

- 删除首元素，队列为空不会抛出异常

  ```java
      public E poll() {
          final Node<E> f = first;
          return (f == null) ? null : unlinkFirst(f);
      }
  ```

- 删除首元素，队列为空则抛出异常

  ```java
      public E remove() {
          return removeFirst();
      }
  ```

- `offer`插入元素

  ```java
  //插入队尾    
  public boolean offer(E e) {
          return add(e);
      }
  //插入队首
      public boolean offerFirst(E e) {
          addFirst(e);
          return true;
      }
  //插入队尾
      public boolean offerLast(E e) {
          addLast(e);
          return true;
      }
  ```

  