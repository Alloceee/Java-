# 集合框架

按照存储结构可以分为两大类单列集合和双列集合

#### 1.单列集合java.util.Collection

- Collection是最基本的集合接口，一个Collection代表一组Object，即Collection元素（Elements）。Java JDK不提供直接继承自Collection的类，Java JDK提供的类都是继承自Collection的“子接口”如List和Set

- 所有实现Collection接口的类都必须提供两个标准的构造函数：无参的构造函数用于创建一个空的Collection，有一个Collection参数的构造函数用于创建一个新的Collection，这个新的Collection与传入的Collection有相同的元素，后一个构造函数允许用户复制一个Collection

- 用Iterator（迭代器）模式实现遍历集合

  - Collection有一个重要的方法：iterator()，返回一（Iterator）个迭代器，用于遍历集合的所有元素。Iterator模式可以把访问逻辑从不同的集合类中抽象出来，从而避免向客户端暴露集合的内部结构。典型的用法如下：

  - 

    ![img](https://img.mubu.com/document_image/b099a407-e9d5-44b5-9234-5512f451b027-3816968.jpg)

  - 不需要维护遍历结合的“指针”，所有的内部状态都是由Iterator来维护，而这个Iterator由集合类通过工厂方法生成

  - 每一种集合类返回的Iterator具体类型可能不同，但它们都实现了Iterator接口，因此，我们不需要关心到底是哪种Iterator，它只需要获得这个Iterator接口即可，这就是接口的好处，面向对象的威力

  - 要确保遍历过程顺利完成，必须保证遍历过程中不更改集合的内容（Iterator的remove()方法除外）。所以，确保遍历可靠的原则是：只在一个线程中使用这个集合，或者在多线程中对遍历代码进行同步

- 单列集合的根接口，用于存储一系列符合某种规则的元素，它有两个重要的子接口，分别是java.util.List和java.util.Set。

  - List：元素有序，元素可重复，实现类有java.util.ArrayList和java.util.LinkedList，可以通过索引来访问List中的元素。List还提供一个listIterator()方法，返回一个ListIterator接口，和标准的Iterator接口相比，ListIterator多了一些add()之类的方法，允许添加、删除、设定元素等，还能向前或向后遍历
    - LinkedList允许null元素，提供get,remove,insert的方法在LinkedList的首部或尾部，这些操作使LinkedList可被用作堆栈、队列或双向队列
    - 注意：LinkedList没有同步方法，如果多个线程同时访问一个List，则必须自己实现访问同步，一种解决方法是在创建List时构造一个同步的List：List list = Collections.synchronizedList(new LinkedList(...))
    - ArrayList实现了可变大小的数组，它允许所有元素，包括Null。ArrayList没有同步。每个ArrayList实例都有一个容量(Capacity)，即用于存储元素的数组的大小。这个容量可随着不断添加新元素而自动增加，但增加算法没有定义，当需要插入大量元素时，在插入前可调用ensureCapacity方法来增加ArrayList的容量以提高插入效率
    - Vector非常类似ArrayList，但是Vector是同步的。由Vector创建的Iterator，虽然和ArrayList创建的Iterator是同一个接口，但是，因为Vector是同步的，当一个Iterator被创建而且正在被使用，另一个线程改变了Vector的状态（例如，添加或删除了一些元素），这时调用Iterator的方法时抛出ConcurrentModificationExeception，因此必须捕获该异常
    - Stack继承自Vector，实现了一个后进先出的堆栈。Stack提供5个额外的方法使得Vector得以被当做堆栈使用。基本的push方法和Pop方法，还有peek方法得到栈顶的元素，empty方法测试堆栈是否为空，search方法检测一个元素在堆栈中的位置。Stack刚创建后是空栈
  - Set：元素无序，元素不可重复，用iterator()方法来区分重复与否，实现类有java.util.HashSet和java.util.TreeSet。Object.equals(Object) = false,Set最多有一个Null元素。很明显，set的构造函数有一个约束条件，传入的Collection参数不能包含重复元素。注意：必须小心操作可变对象(Mutable Object)。如果一个Set中的可变元素改变了自身状态导致Object.equals(Object) = true将导致一些问题。
    - HashSet去除重复元素的原理
      - 调用被添加元素的hashCode()和HashSet中已有元素的hashCode比较是否相同
      - 如果不相同，则直接存储
      - 如果相同，调用equals()方法比较是否相同
      - 不相同，直接存储元素；相同则认为是同一元素不存储

#### 2.双列集合java.util.Map

- Map提供key到value的映射，一个Map中不能包含相同的Key，每个key只能映射一个value。Map的内容可以被当做一个key集合，一个value集合，或者一组kay-value映射
- HashTable类
  - 任何非空的对象可以作为key或者value。由于作为key的对象将通过计算其散列函数来确定与之对应的位置，因此任何作为key的对象都必须实现hashCode()和equals()，hasCode()和equals()都必须继承自根类Object，如果你用自定义的类当做key的话，要相当小心，按照散列函数的定义，如果两个对象相同，obj1.equals(obj2)=true，则他们的hashCode必须相同，但是两个对象不同，则它们的hashCode不一定不同，如果两个不同对象的hashCode相同，这种现象称为冲突，冲突会导致操作哈希表的时间开销增大，所以尽量用定义好的hashCode()，能加快哈希表的操作
  - 避免这种问题：要同时重写equals方法和hashCode方法，而不要只写其中一个。
  - HashTable是同步的
- HashMap类
  - HashMap和HashTable类似，不同之处在于HashMap是非同步的，并且允许Null，即null value和null key。但是将HashMap视为Collection时（values()方法可返回Collection），其迭代器操作时间开销和HashMap的容量成比例。因此，如果迭代器操作的性能相当重要的话，不要将HashMap的初始化容量设的过高，或者load factor过低
- WeakHashMap类
  - WeakHashMap是一种改进的HashMap，它对key实现“弱引用”，如果一个Key不再被外部引用，那么该Key可以被GC回收
- TreeMap和HashMap有什么不同点
  - HashMap:基于哈希表实现，使用HashMap要求添加的键类明确定义了hashCode()和equals()可以重写hashCode()和equals()，为了优化HashMap空间的使用，可以调优初始化容量和负载因。TreeMap:基于红黑树实现。TreeMap没有调优选项，因为该树总处于平衡状态
  - HashMap通过hashCode对其内容进行快速查找，TreeMap中所有的元素都保持着某种固定的顺序，如果你需要得到一个有序的结果你就应该使用TreeMap(HashMap中元素的排列顺序是不固定的)
  - HashMap非线程安全，TreeMap线程安全
  - HashMap适用于在Map中插入、删除和定位元素，TreeMap适用于按自然排序或自定义排序遍历键（key）
  - 总结：HashMap通常比TreeMap快一点（树和哈希表的数据结构使然），建议多使用HashMap，在需要排序的Map时候才使用TreeMap
- HashMap和HashSet的区别

| HashMap                                                | HashSet                                                      |
| ------------------------------------------------------ | ------------------------------------------------------------ |
| 实现了Map接口                                          | 实现Set接口                                                  |
| 存储键值对                                             | 仅存储对象                                                   |
| 调用put()向Map中添加元素                               | 调用add()方法向Set中添加元素                                 |
| HashMap使用键(Key)计算HashCode                         | HashSet使用成员对象来计算hashcode值，对于两个对象来说hashcode可能相同，所以equals()方法用于判断对象的相等性，如果两个对象不同的话，那么返回false |
| HashMap相对于HashSet较快，因为它是使用唯一的键获取对象 | HashSet较HashMap来说比较慢                                   |

**15.HashMap和HashTable区别**

1）.HashTable的方法前面都有synchronized来同步，是线程安全的；HashMap未经同步，是非线程安全的。
2）.HashTable不允许null值(key和value都不可以) ；HashMap允许null值(key和value都可以)。
3）.HashTable有一个contains(Object
value)功能和containsValue(Object
value)功能一样。
4）.HashTable使用Enumeration进行遍历；HashMap使用Iterator进行遍历。
5）.HashTable中hash数组默认大小是11，增加的方式是old*2+1；HashMap中hash数组的默认大小是16，而且一定是2的指数。
6）.哈希值的使用不同，HashTable直接使用对象的hashCode； HashMap重新计算hash值，而且用与代替求模。

**16.ArrayList和vector区别**

ArrayList和Vector都实现了List接口，都是通过数组实现的。
Vector是线程安全的，而ArrayList是非线程安全的。
List第一次创建的时候，会有一个初始大小，随着不断向List中增加元素，当List 认为容量不够的时候就会进行扩容。Vector缺省情况下自动增长原来一倍的数组长度，ArrayList增长原来的50%。

**17.ArrayList和LinkedList区别及使用场景**

区别
ArrayList底层是用数组实现的，可以认为ArrayList是一个可改变大小的数组。随着越来越多的元素被添加到ArrayList中，其规模是动态增加的。
LinkedList底层是通过双向链表实现的， LinkedList和ArrayList相比，增删的速度较快。但是查询和修改值的速度较慢。同时，LinkedList还实现了Queue接口，所以他还提供了offer(),
peek(), poll()等方法。
使用场景
LinkedList更适合从中间插入或者删除（链表的特性）。
ArrayList更适合检索和在末尾插入或删除（数组的特性）。

**18.Collection和Collections的区别**

java.util.Collection 是一个集合接口。它提供了对集合对象进行基本操作的通用接口方法。Collection接口在Java 类库中有很多具体的实现。Collection接口的意义是为各种具体的集合提供了最大化的统一操作方式。
java.util.Collections 是一个包装类。它包含有各种有关集合操作的静态多态方法。此类不能实例化，就像一个工具类，服务于Java的Collection框架。


### 总结：

- 如果涉及到堆栈、队列等操作，应该考虑List，对于需要快速插入，删除元素，应该使用LinkedList，如果需要快速随机访问元素，应该使用ArrayList

- 如果程序在单线程环境中，或者访问仅仅在一个线程中进行，考虑非同步的类，其效率较高，如果多个线程可能同时操作一个类，应该使用同步的类

  ![img](https://img.mubu.com/document_image/ba88ccd5-1acd-4297-a296-f901ce280f0c-3816968.jpg)

- 要特别注意对哈希表的操作，作为Key的对象要正确复写equals和hasCode方法

- 尽量返回接口而非实际的类型，如返回List而非ArrayList，这样如果以后需要将ArrayList换成LinkedList时，客户端代码不用改变，这就是抽象编程



实现了RandomAccess接口的list，优先使用for循环、foreach

未实现RandomAccess接口的list，优先使用iterator遍历（foreach底层也是使用iterator实现），大size的数据不要使用for

| List |                     |                                                              |
| ---- | ------------------- | ------------------------------------------------------------ |
|      | ArrayList           | Object数组                                                   |
|      | Vector              | Object数组                                                   |
|      | LinkedList          | 双向链表（JDK1.6之前为循环链表，JDK1.7取消了循环）           |
| Set  |                     |                                                              |
|      | HashSet(无序，唯一) | 基于HashMap实现，底层采用HashMap来保存元素                   |
|      | LinkedHashSet       | LinkedHashSet继承于HashSet，并且其内部是通过LinkedHashMap来实现的 |
|      | TreeSet(有序，唯一) | 红黑树（自平衡的排序二叉树）                                 |
| Map  |                     |                                                              |
|      | HashMap             | JDK1.8之前HashMap由数组+链表组成的，数组是HashMap的主体，链表则主要是为了解决哈希冲突（“拉链法”解决冲突），JDK1.8以后在解决哈希冲突时有了较大的变卦，当链表长度大于阈值（默认为8）时，将链表转化为红黑树，以减少搜索时间 |
|      | LinkedHashMap       | LinkedHashMap继承自HashMap，所以它的底层仍然是基于拉链式散列结构即由数组和链表或红黑树组成。另外，LinkedHashMap在上面结构的基础上，增加了一条双向链表，使得上面的结构可以保存键值对的插入顺序。同时通过对链表进行相应的操作，实现了访问顺序相关逻辑。 |
|      | HashTable           | 数组+链表组成，数组是HashMap的主题，链表是为了解决哈希冲突   |
|      | TreeMap             | 红黑树（自平衡的排序二叉树）                                 |

