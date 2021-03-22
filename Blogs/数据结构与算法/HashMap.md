# HashMap

## 概要
HashMap 是一个关联数组、哈希表，它是线程不安全的，允许key为null,value为null。遍历时无序。  
其底层数据结构是数组称之为哈希桶，每个桶里面放的是链表，链表中的每个节点，就是哈希表中的每个元素。  
在JDK8中，当链表长度达到8，会转化成红黑树，以提升它的查询、插入效率，它实现了Map<K,V>, Cloneable, Serializable接口。
### 构造函数
```
    //最大容量 2的30次方
    static final int MAXIMUM_CAPACITY = 1 << 30;
    //默认的加载因子
    static final float DEFAULT_LOAD_FACTOR = 0.75f;

    //哈希桶，存放链表。 长度是2的N次方，或者初始化时为0.
    transient Node<K,V>[] table;

    //加载因子，用于计算哈希表元素数量的阈值。  threshold = 哈希桶.length * loadFactor;
    final float loadFactor;
    //哈希表内元素数量的阈值，当哈希表内元素数量超过阈值时，会发生扩容resize()。
    int threshold;

    public HashMap() {
        //默认构造函数，赋值加载因子为默认的0.75f
        this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
    }
    public HashMap(int initialCapacity) {
        //指定初始化容量的构造函数
        this(initialCapacity, DEFAULT_LOAD_FACTOR);
    }
    //同时指定初始化容量 以及 加载因子， 用的很少，一般不会修改loadFactor
    public HashMap(int initialCapacity, float loadFactor) {
        //边界处理
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        //初始容量最大不能超过2的30次方
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        //显然加载因子不能为负数
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);
        this.loadFactor = loadFactor;
        //设置阈值为  》=初始化容量的 2的n次方的值
        this.threshold = tableSizeFor(initialCapacity);
    }
    //新建一个哈希表，同时将另一个map m 里的所有元素加入表中
    public HashMap(Map<? extends K, ? extends V> m) {
        this.loadFactor = DEFAULT_LOAD_FACTOR;
        putMapEntries(m, false);
    }

```

## 几个重要方法

## 对比HashTable、ConcurrentHashMap、LinkedHashMap

## 扩容机制
当HashMap的容量达到threshold域值时，就会触发扩容。扩容前后，哈希桶的长度一定会是2的次方。  
这样在根据key的hash值寻找对应的哈希桶时，可以用位运算替代取余操作，更加高效。

## 哈希碰撞


## 扰动函数

## 查询速度与插入速度


