### 继承关系图

![](D:\Java相关\笔记\img\集合.png)

### 迭代器

迭代器是一个内部类，所以持有集合的引用，List类迭代器通过下标来迭代，游标保存在AbstractList类中。

```java
protected transient int modCount = 0;//ArrayList属性
```

remove操作里涉及到的**expectedModCount = modCount**;

在网上查到说这是集合迭代中的一种“**快速失败**”机制，这种机制提供迭代过程中集合的安全性。

我们知道的是ArrayList是线程不安全的，如果在使用迭代器的过程中有其他的线程修改了List就会抛出ConcurrentModificationException，这就是**Fail-Fast**机制。

#### Iterable 与 Iterator

```java
//Iterable用来标识可以迭代 
public interface Iterable<T> { 
    //只有一个返回迭代器的方法    
    Iterator<T> iterator(); 
}
//Iterator迭代器接口
public interface Iterator<E> {
   //判断边界，是否还有下一个对象，即是否还能迭代
    boolean hasNext();
   //返回下一个对象
    E next();
   //移除当前指针指向的对象
    void remove();
}
```

迭代器的使用流程是：
1、通过hasNext()判断边界，是否可以继续迭代
2、通过next() 方法返回指针指向的对象
3、remove()方法移除指针指向的对象,remove之前必须先执行过next()

迭代器在不同的集合里有不同的实现。
下面来看下各种集合的迭代器实现，主要理解原理。

```java
LinkedList.Node<E> node(int index) {//LinkedList通过下标取就是链式后移计数取到指定位置的节点
    LinkedList.Node x;
    int i;
    if (index < this.size >> 1) {
        x = this.first;
        for(i = 0; i < index; ++i) {
            x = x.next;
        }
        return x;
    } else {
        x = this.last;
        for(i = this.size - 1; i > index; --i) {
            x = x.prev;
        }
        return x;
    }
}
```

### 线程安全

```java
Collections.synchronizedList(li);//实现原理如下
//返回一个包装对象，对每个方法单独加锁，是加的同一个对象锁
final List<E> list;
SynchronizedList(List<E> list) {
    super(list);
    this.list = list;
}
public E get(int index) {
    synchronized(this.mutex) {
        return this.list.get(index);
    }
}
```

### 使用注意项

Map类 putAll()可以合并两个MAP，如果有相同的key那么用后面的覆盖前面的