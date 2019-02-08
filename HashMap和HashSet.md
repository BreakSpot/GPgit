## HashMap和HashSet

1、HashMap的工作原理

HashMap基于hash原理，通过put()和get()方法存储和获取对象。将键值对传递给put时，先调用hashCode方法来计算hashCode，让后找的bucket位置来存储Entry对象，当获取对象时，通过对象的equals方法找到正确的键值对，然后返回值对象。使用链表来解决碰撞问题，当发生碰撞了，对象将会储存在链表的下一个节点中。HashMap在每个链表节点中存储键值对对象。

2、键值保存

实现map接口，Map中不允许重复的键，Map下有两个实现，HashMap和TreeMap，TreeMap保存了对象的排列次序，而hashMap则不能，HashMap存储键值对，允许键和值为null。collection框架保证一个线程更改Map。

3、hashSet实现Set接口，它不允许集合中有重复的值，存储HashSet之前，确保对象重写equals和hashCode方法，保证比较对象相等。

4、两者区别

![](E:\gitProgram\VacationReview\img\20180811190835669.png)



5、HashMap的数据结构

哈希表结构（链表散列：数组+链表）实现，结合数组和链表的优点。

6、几种遍历方式

```java
Map map = new HashMap();
Iterator iter = map.entrySet().iterator();
while (iter.hasNext()) {
    Map.Entry entry = (Map.Entry) iter.next();
    Object key = entry.getKey();
    Object val = entry.getValue();
}
```

```java
Map map = new HashMap();
Iterator iter = map.keySet().iterator();
while (iter.hasNext()) {
    Object key = iter.next();
    Object val = map.get(key);
}
```

```java
Map<String, String> map = new HashMap<String, String>();
for (String key : map.keySet()) {
    map.get(key);
}
```

```java
Map<String, String> map = new HashMap<String, String>();
for (Entry<String, String> entry : map.entrySet()) {
    entry.getKey();
    entry.getValue();
}
```

7、map的线程安全

HashMap：线程不安全，why？https://blog.csdn.net/andy_budd/article/details/81413464

hashTable：线程安全，每个方法上都加上synchronized（同步锁）



