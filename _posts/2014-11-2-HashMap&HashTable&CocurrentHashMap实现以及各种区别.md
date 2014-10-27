---
layout: default
title: HashMap HashTable CocurrentHashMap区别
---
HashMap
首先来看一下HashMap<K,V>实现的借口类, Serializable Cloneable Map<K,V>, 扩展自AbstractMap<K,V>
Serializable: 只是一个生命接口，代码这个类可以被序列化，具备这个属性了
Cloneable: 同Serializable，表明它有一个clone方法，可以按属性复制
Map<K,V>: 方法很多，有常用的size,get,put,remove,containsKey等。
AbstractMap<K,V>: 在HashMap中null可以用来作为键,值，key为null时和一般的key等同，只能允许一个存在。(和HashTable不同，HashTable的K,V均不能为null),如果要获取这个null作为Key的值需要用containsKey,不能用get来获取
get返回null时为，可能是不存在这个键，或者是这个键对应的值就为null
HashMap的初始化有两个参数initialCapacity loadFactor, initialCapacity就是初始时候HashMap的大小，默认为11，如果使用了一个map初始化一个HashMap则会根据Math.max(2*t.size(), 11)的大小对HashMap进行初始化； 然后这个factor就是允许这个HashMap数据存在的多少占大小的比例，可减小数据间的碰撞(hashcode是有可能重复的),然后这里还有一个threshold(capacity * loadFactor),就是允许的大小，用来对插入时判断数据是否超出允许的容量，如果大于了threshold就会进行rehash，它的大小为oldCapacity * 2 + 1

HashTable
扩展自Dictionary<K,V>, 实现接口有Serializable Cloneable Map<K,V>同HashMap一样的.
核心的Dictionary<K,V>，不允许Key，Value为null，这也是两个的区别

在HashMap中，可以看到各种synchronized，所以相对来说HashTable是线程安全的，而HashMap就不是，但是锁也导致HashTable的性能就比HashMap差(主要表现在put上)

CoccurentHashMap
ConcurrentHashMap capacity初始值为16, factor为0.75
而ConcurrentHashMap在put时使用的是lock，他的范围比synchronized大，lock后其他线程都无法访问，synchronized只是锁变量，然后通过内部的put(K key, int hash, V value, boolean onlyIfAbsent)将value插入进入(没有理解这个onlyIfAbsent什么意思，每次都是传死的false).

//添加单线程，多线程(插入，查找，删除10，100，10000个元素)性能测试
