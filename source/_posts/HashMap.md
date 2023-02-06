---
title: HashMap
tags: [HashMap, Java]
categories:
  - 后端
  - Java
index_img: /img/default.jpeg
date: 2022-03-02 19:06:26
---

## HashMap

### 基本属性

#### 一、核心常量

```java
// 默认容量：即2的4次方，16
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;

// 最大容量：即2的30次方，1073741824
static final int MAXIMUM_CAPACITY = 1 << 30;

// 默认负载因子：0.75
static final float DEFAULT_LOAD_FACTOR = 0.75f;

// 链表树化的条件之一：达到8个节点
static final int TREEIFY_THRESHOLD = 8;

// 解树化的条件：节点退化为6时
static final int UNTREEIFY_THRESHOLD = 6;

// 链表树化的条件之一：数组容量达到64
static final int MIN_TREEIFY_CAPACITY = 64;
```

#### 二、构造方法

```java
// 空构造方法
public HashMap()
    
// 可设置容量构造方法
public HashMap(int initialCapacity)
    
// 容量和负载因子构造方法
public HashMap(int initialCapacity, float loadFactor)
    
// 可设置初始数据构造方法
public HashMap(Map<? extends K, ? extends V> m)
```

#### 三、核心变量

**loadFactor**：负载因子变量

**threshold**：调整大小的下一个大小值（容量负载因子）

**modCount**：这个HashMap被结构修改的次数，结构修改是那些改变HashMap中的映射数量或以其他方式修改其内部结构（例如：rehash）的修改。该字段用于使用hashmap的集合视图上的迭代器快速失败。

**size**：使用数

**Set<Map.Entry<K,V>>**：持有缓存的entrySet()。请注意：AbstactMap字段用于keySet()和values()。

**Node<K,V>[]**：HashMap最上层的数据结构

### 常见问题

#### （1）为什么使用HashMap？

Hashmap是用来存放键值对的。同时因为它基于hash表的实现，它可以实现快速的增删查改。在理想情况下，可以达到O（1）的查询速度。当然这只是理想状况。

#### （2）装载因子为什么是0.75？

```java
static final float DEFAULT_LOAD_FACTOR = 0.75f;
```

- 加载因子越大，填满的元素越多，空间利用率越高，但发生冲突的机会变大了；-> **底层红黑树复杂，时间成本高**

- 加载因子越小，填满的元素越少，冲突发生的机会减小，但空间浪费了更多了，而且还会提高扩容rehash操作的次数；-> **空间成本高。**

总结：和泊松分布无关（泊松分布是链表树化计算出来的概率），千万不要被一些博客误解，就一句话：0.75是为了时间和空间上的权衡。

![装载因子0.75与泊松分布没有关系](https://gitee.com/yu980219/picture-warehouse/raw/master/images/hashmap/image-20220929135428498.png)

#### （3）为什么长度是2的次幂？

- ##### 与“取余”等价的算法

  取余操作中如果除数是2的幂次则等价于与其除数减一的与操作 ，也就是说hash%length==hash&(length-1）的前提是length是2的 n 次方，并且采用二进制位操作 ,相对于取余操作能够提高运算效率。

- ##### 扩容时方便定位

  扩容时rehash步骤：
  

举个例子：

  原数组长度为16，现需要将其扩容到32

  **16-1的二进制：0000 1111**

**32-1的二进制：0001 1111**

------------

计算数组下标公式不变：hash(key) & (table.length-1)

假设某一元素的hash为**52**

**52的二进制：0011 0100**

----------

扩容前，该元素下标：

**52&(16-1)：**

  ​		**0011 0100**

  **&	 0000 1111**

  **————————**

  ​		**0000 0100 = 4**

------

  扩容后，该元素下标：

  **52&(32-1)：**

  ​		**001`1` 0100**

  **&	 000`1` 1111**

  **————————**

  ​		**000`1` 0100 = 20**

---------------------

  规律就是，16-1到32-1的变化，只发生在最高位上，每次扩容2倍，只需要在原容量的高位前面补1。

  key的hash值不变，长度每次在最高位变化1。

  也就是说，我们每次只需要对比扩容后key的hash和length-1对应的最高位上的数值就好了，与运算的逻辑为，**同1得1，其他是0**。

  - 如果这个位置是0，余数计算的结果将保持不变，意味着扩容后此元素还在这个槽中（槽编号没发生改变）
  
  - 如果这个位置是1，余数计算结果就变成了`原槽索引 + 原array.length`。

  **总结：**

  1. 能利用 & 操作代替 % 操作，提升性能
  2. 数组扩容时，仅仅关注 “特殊位” 就可以重新定位元素

#### （4）如何解决哈希冲突？

HashMap使用**拉链法**解决冲突：

**所谓“拉链法” 就是：将链表和数组相结合。也就是说创建一个链表数组，数组中每一格就是一个链表。若遇到哈希冲突，则将冲突的值加到链表中即可。**

![hashmap的结构示意图](https://gitee.com/yu980219/picture-warehouse/raw/master/images/hashmap/20200612002351185.png)

#### （5）为什么阈值为8树化，6再退化，8和6怎么来的？

- 阈值>=8，链表转红黑树。

这里就要用到泊松分布了

```java
* Because TreeNodes are about twice the size of regular node
* use them only when bins contain enough nodes to warrant us
* (see TREEIFY_THRESHOLD). And when they become too small (d
* removal or resizing) they are converted back to plain bins
* usages with well-distributed user hashCodes, tree bins are
* rarely used.  Ideally, under random hashCodes, the frequen
* nodes in bins follows a Poisson distribution
* (http://en.wikipedia.org/wiki/Poisson_distribution) with a
* parameter of about 0.5 on average for the default resizing
* threshold of 0.75, although with a large variance because 
* resizing granularity. Ignoring variance, the expected
* occurrences of list size k are (exp(-0.5) * pow(0.5, k) /
* factorial(k)). The first values are:
*
* 0:    0.60653066
* 1:    0.30326533
* 2:    0.07581633
* 3:    0.01263606
* 4:    0.00157952
* 5:    0.00015795
* 6:    0.00001316
* 7:    0.00000094
* 8:    0.00000006
* more: less than 1 in ten million
```

上面这段话的意思是，如果 hashCode 分布良好，也就是 hash 计算的结果离散好的话，那么红黑树这种形式是很少会被用到的，因为各个值都均匀分布，很少出现链表很长的情况。在理想情况下，**链表长度符合泊松分布，各个长度的命中概率依次递减，当长度为 8 的时候，概率仅为 0.00000006**。这是一个小于千万分之一的概率，通常我们的 Map 里面是不会存储这么多的数据的，所以通常情况下，并不会发生从链表向红黑树的转换。

- 阈值<=6，红黑树转链表。

红黑树和链表的转换也需要性能，如果等于8就树化，小于8就转链表，hashmap就会在这中间反复横跳，影响性能，预留了一个缓冲。

#### （6）为什么使用红黑树而不用B树 ？

如果用 B/B+ 树 的话，在数据量不是很多的情况下，数据都会 “挤在” 一个结点里面，这个时候遍历效率就**退化**成了**链表**。

#### （7）**为什么数组容量要大于64才进行树形化操作呢？**

 假设数组容量是16初始容量，也发送了8次的哈希冲突，但是哈希冲突产生的原因是因为数组的容量太小，在进行数组运算时，将下标落在了一个位置上，也就是说并不是因为哈希冲突造成的链表长度过长，而是因为数组长度太小导致取余冲突问题，所以hashmap会优先进行容量扩容，直到达到64还是发生8次哈希冲突，就排除了由于数组长度太小造成的冲突问题。

### 源码篇

#### 一、hash()：计算在桶中的位置

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}

// 这里的 (n-1)&hash 就是跟hash计算出来的值和size-1进行与运算
if ((p = tab[i = (n - 1) & hash]) == null)
tab[i] = newNode(hash, key, value, null);

/*
 	1. h = key.hashCode()					取hashcode值
    2. h ^ (h >>> 16)						将hashcode右移16位，再和原结果进行异或运算：高位参与运算（异或：同为0，异为1）
    3. index = (n - 1) & hash				取模运算（按位与运算：同1得1，其他是0）
*/
```

![索引计算流程图](https://gitee.com/yu980219/picture-warehouse/raw/master/images/hashmap/20200612005610198.png)

总结：

1. 为什么要右移16位而不是直接返回hashcode的值？

   目的是让高位参与运算

   int有32位，h>>>16再异或key.hashcode()，被称为扰动函数，让高16位也参与到hashcode的取值中，**混合原始哈希码的高位和低位，以此来加大低位的随机性**。

   ![索引计算样例](https://gitee.com/yu980219/picture-warehouse/raw/master/images/hashmap/16390890-8018e0b694b486d7.png)

   *Q：扰动为什么是异或，而不是别的呢？*

   A：因为异或的计算法则是，同为0，异为1，00 11 01 10，数字0和1的四种组合情况，结果为1和0的各占50%，结果最平均。

2. 为什么使用按位与&运算而不是取模运算？

   因为按位与运算要比取模运算的效率高的多，取模运算要进行进制转换，转换成10进制，而位运算是直接进行二进制操作。

3. key是可以为null的，为null的key会被放在索引为0的槽位上。

#### 二、put()：放入元素

```java
public static void main(String[] args) {
	// 新建一个hashmap，并向map里放入key为字符串“k”，value为字符串“v”的一个键值对。
    HashMap<String,String> map = new HashMap<>();
    map.put("k","v");
}

// 进入put()方法
public V put(K key, V value) {
    // 这里hash(key)把已经计算好的hashcode带入
    return putVal(hash(key), key, value, false, true);
}

// put核心方法（重要）
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    // 让tab指向该hashmap的底层数组，n指向该hashmap的容量
    // 如果数组空或者容量是0，说明map是刚刚创建，要先进行一次初始化操作。
    if ((tab = table) == null || (n = tab.length) == 0)
        // 第一次进行初始化操作，也就是扩容操作，并返回长度赋值给n。
        n = (tab = resize()).length;
    // 初始化好以后，i = (n - 1) & hash，是关键，这一步就是确定数组索引位置i。
    // 如果发现这个槽位空了
    if ((p = tab[i = (n - 1) & hash]) == null)
        // 新建一个节点，next指向null，并赋值到当前下标位置。
        tab[i] = newNode(hash, key, value, null);
    // 如果这个不空，说明冲突了，下面就是拉链法解决冲突，或者树化，或者覆盖值的操作。
    else {
        // e代表当前节点，k代表key值
        Node<K,V> e; K k;
        // 如果tab[i]是一个Node节点，
        // 并且key的hash和我们要插入的hash一样，并且key也一样。
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            // 直接覆盖即可，当前节点e直接指向该位置。
            e = p;
        // 如果tab[i]是一颗树
        // 这里完全可以用instanceof判断，因为TreeNode是Node的子类。
        else if (p instanceof TreeNode)
            // 就按照树的方式，插入节点或覆盖值。
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        // 上面两种都不是，那就剩链表了。
        else {
            // 这里用了遍历，一直遍历到链表的尾部，进行尾插。
            for (int binCount = 0; ; ++binCount) {
                // 如果被碰撞节点tab[i]节点的下一个节点空了，说明到尾部了。
                if ((e = p.next) == null) {
                    // 直接尾插。
                    p.next = newNode(hash, key, value, null);
                    // 如果遍历发现，长度>=8了，说明到达了链表树化的阈值。
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        // 要树化链表，转为红黑树
                        treeifyBin(tab, hash);
                    // 如果一直到最后e还是空，说明没有相同的key，这是一个插入操作，直接跳出循环
                    break;
                }
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                	// 如果key相同，直接break，当前节点e直接指向当前位置就好了，跳出循环。
                    break;
                p = e;
            }
        }
        
        // 注意，一直到这里，发生冲突时，还没有真正的覆盖value，只是改变了指针的指向
        // 下面才是真正覆盖值，并且返回老值的操作，因为，put方法的返回值就是返回旧值。
        // 如果e不空，说明，要覆盖；否则走下面 return null;
        if (e != null) { // existing mapping for key
            // 老值赋给oldValue
            V oldValue = e.value;
            // onlyIfAbsent默认false，!onlyIfAbsent就是true，继续走。
            if (!onlyIfAbsent || oldValue == null)
                // 把新值赋给该节点，把老元素覆盖掉。
                e.value = value;
            afterNodeAccess(e);
            // 返回值
            return oldValue;
        }
    }
    
    // 如果走到这里，说明上面 return oldValue; 没走
    // 也就说明，这一次put不是覆盖，而是新增
    // 所以要计算修改的次数，并判断是不是需要扩容
    ++modCount;
    if (++size > threshold)
        // 如果到达阈值要进行扩容
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

总结：

![put流程图](https://gitee.com/yu980219/picture-warehouse/raw/master/images/hashmap/84561e1f9026499294f72b8b85004ae6.png)

#### 三、get()：获取元素

```java
public static void main(String[] args) {
	// 新建一个hashmap，并向map里放入key为字符串“k”，value为字符串“v”的一个键值对。
    HashMap<String,String> map = new HashMap<>();
    map.put("k","v");
    map.get("k");
}

// 进入get()方法
public V get(Object key) {
    Node<K,V> e;
    // 这里hash(key)把已经计算好的hashcode带入
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}

// get核心方法（重要）
final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    // 如果数组不空，并且容量>0，并且计算出来的桶索引位置上的元素不空，进行取值
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        // 如果hash值相同，并且key也是一样的，直接返回。
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        // 如果到这一步，说明首节点判断完了，接下来该是链表或者红黑树的了。
        if ((e = first.next) != null) {
            // 如果是树形结构，进入树的取值流程
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            // 如果是链表，则遍历链表即可。
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

#### 四、resize()：扩容

> 这段代码比较难理解，要认真反复的看。
>
> resize()的有两个作用：
>
> 1. 初始化
> 2. 扩容
>
> 这里有几个概念：
>
> ![resize时的一些局部变量介绍](https://gitee.com/yu980219/picture-warehouse/raw/master/images/hashmap/ff43c854c6b84604bc07d6cfc2ed24aa.png)

```java
final Node<K,V>[] resize() {
    // 原数组
    Node<K,V>[] oldTab = table;
    // oldCap：原容量
    // 如果tab压根不存在，说明是第一次创建，原容量默认为0，否则就是原容量。
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    // oldThr用来存储当前阈值，也就是原阈值。
    int oldThr = threshold;
    // 创建了新容量，和新阈值，默认都为0
    int newCap, newThr = 0;
    // 如果原容量大于0，说明这是初始化完毕的hashmap，准备扩容。
    if (oldCap > 0) {
        /**
        <极限的扩容流程>
        例：
        容量：2^30 -> 2^30
        阈值：2^29 -> Integer.MAX_VALUE
       	**/
        
        // 容量到达最大值了吗？到了就不能再扩容了，直接返回。
        if (oldCap >= MAXIMUM_CAPACITY) {
            // 把int的最大值赋给阈值，这样，永远都达不到这个数，就永远不会扩容了。
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        
        /**
        <通常正常的扩容流程>
        该分支正常扩容，例：
        容量：16 -> 32
        阈值：12 -> 24
       	**/
        
        // oldCap << 1：相当于乘以2，也就是我们说的扩容2倍
        // 扩容2倍赋值给newCap并且看看容量有没有超过最大阈值 并且 旧容量是否大于默认容量16
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            // 如果都满足，新阈值变为原来的2倍
            newThr = oldThr << 1; // double threshold
    }
    
    // 走到这一步，说明map还没被初始化，因为上面才是初始化之后的扩容流程。
    // 没初始化，为什么原阈值还会大于0呢？
    // 那只有三种情况，就是使用了hashmap的有参构造方法：
    // public HashMap(int initialCapacity, float loadFactor)
    // public HashMap(int initialCapacity)
    // public HashMap(Map m)
    // 说明，初始化hashmap的时候，使用了构造方法指定了容量大小，构造方法里的tableSizeFor()方法，设定了阈值的大小，但因为数组还没有创建，所以容量是0，阈值则为自修正的2的整数倍。
    
    /**
        <初始化流程>
        该分支走了有参构造，例：
        新阈值：空缺
        原阈值：16 -> 新容量：16
       	**/
    else if (oldThr > 0) // initial capacity was placed in threshold
        // 把原阈值赋值给新容量。
        newCap = oldThr;
    
    /**
        <初始化流程>
        该分支走了无参构造，例：
        新容量：默认16
        新阈值：默认16*0.75=12
       	**/
    // 如果到了这一步，说明原容量<=0。
    // 也就是调用了无参构造方法，没真正的初始化hashmap，
    else {               // zero initial threshold signifies using defaults
        // 默认容量 16
        newCap = DEFAULT_INITIAL_CAPACITY;
        // 默认阈值 12。
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    
    /**
        <初始化流程>
        填补上面的新阈值空缺
        新阈值：新容量16*0.75=12
        新容量：16
       	**/
    
    // 如果新阈值=0，
   	// 说明走了上面的 else if (oldThr > 0) 
    // 因为只有这个分支里 没设置newThr
    // 那就是说hashmap使用了有参构造方法初始化了
    if (newThr == 0) {
        // 计算新阈值。这就填补上上面的新阈值空缺的情况了
        float ft = (float)newCap * loadFactor;
        // 这里也要判断容量是否大于容量上限
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    
    // 至此，扩容之前的所有准备工作已经完毕
    // 无论这次是扩容操作，还是初始化操作。
    // 新阈值和新容量都已经被设置好。
    // 接下来就是要进行rehash，将旧数组的元素移交到新数组。
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    // 创建一个新数组，容量为新容量
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    // 再赋值给当前全局变量
    table = newTab;
    
    // 下面就是扩容的核心流程
    // 说到这里，必须知道一个前置知识，就是jdk8中扩容的hash计算方式
    // 在上文《常见问题》中《为什么长度是2的次幂》有讲到，一定要看完再回来。
    // 如果旧数组 == null，则不涉及数组迁移等操作，在最后直接返回即可。
    if (oldTab != null) {
        // 遍历整个旧数组，把数组上面的节点取出一个一个处理
        for (int j = 0; j < oldCap; ++j) {
            // 创建一个临时变量e，后续都是把旧数组位置上的node指向e
            Node<K,V> e;
            // 如果这个位置上的元素空了，就不进行处理。有数据则处理。
            if ((e = oldTab[j]) != null) {
                // 把旧数组的当前位置设为null
                oldTab[j] = null;
                // 如果e的next为空，说明该槽位并没有链表或者树，只有一个节点
                if (e.next == null)
                    // 直接rehash，重新计算下标位置，并赋值到新元素的位置上即可。
                    newTab[e.hash & (newCap - 1)] = e;
                // 如果是树节点
                else if (e instanceof TreeNode)
                    // 调用树节点的方法处理
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                // 最后是链表结构的情况
                else { // preserve order
                    // lo：low的缩写，低位
                    // hi：high的缩写，高位
                    // 暂时不用知道他们什么意思，看下面。
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    /** 遍历链表
                     上文《为什么长度是2的次幂》读完，下面读起来就会很顺畅
                     扩容后，
                     如果与运算结果=1，槽位变化。
                     如果与运算结果=0，槽位不变。
                     那么上面的4个参数中的loHead，loTail代表，所有槽位不变的node节点的指针，hiHead和hiTail代表所有槽位变化的node节点的指针。
                     这样，就可以在这一个链表里，把所有变化的，和没变化的拆分成两条链。所有没发生变化的自然就在原槽位呆着不动，所有变化的再拿出来做处理。下文有图。
                     或者可以看看这篇文章https://www.codenong.com/jsa0e02fce06d2/
                    **/
                    do {
                        next = e.next;
                        // 与运算结果=0，槽位不变。
                        if ((e.hash & oldCap) == 0) {
                            // 看当前节点是不是第一个节点
                            if (loTail == null)
                                // 如果是，则把该节点放到头部
                                loHead = e;
                            else
                                // 如果不是，把该节点链接到尾部
                                loTail.next = e;
                            // 最后当前节点指向尾部
                            loTail = e;
                        }
                        // 与运算结果=1，槽位变化。
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    
                    // 不用变动槽位的，把lo这条链取出来，放在原槽位即可
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    
                    // 需要变动槽位的，把hi这条链取出来，放在（原槽索引 + 原capSize）的位置即可。
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    // 完成扩容
    return newTab;
}
```


![loHead，loTail，在遍历链表时的流程，hiHead，hiTail同理](https://gitee.com/yu980219/picture-warehouse/raw/master/images/hashmap/1260967-cc6df182e8a54ebe.jpg)

### **文章引用**

[【源】终于明白JDK8 HashMap底层数组长度，取值2次幂的原因](https://segmentfault.com/a/1190000017509668)

[面试官：Hashmap链表长度为8时转换成红黑树，你知道为什么是8吗](https://blog.csdn.net/kyle_wu_/article/details/113578055)

[带你搞清楚HashMap扩容中的loTail.next = e和loTail = e](https://www.codenong.com/jsa0e02fce06d2/)