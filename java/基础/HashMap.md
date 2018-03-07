# HashMap

## hash 方法
```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

- hashCode 是 int 型，不可能直接作为下标访问 HashMap 主数组（内存放不下那么大的数组）
- 通过 hashCode 对数组长度取模作为访问数组的下标
- 上面的 hash 方法，将 hashCode 的低 16 位与高 16 位混合，用于加大低位的随机性
- 上面的运算就是减少碰撞用的

## get

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    if ((tab = table) == null || (n = tab.length) == 0)
        // 重新计算数组 size
        n = (tab = resize()).length;
    if ((p = tab[i = (n - 1) & hash]) == null)
        // 数组当前位置没有数据，插入新数据
        tab[i] = newNode(hash, key, value, null);
    else {
        // e 保存之前存在的数据，k 之前数据的 key
        Node<K,V> e; K k;
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            // 数组相关位置处有数据，但是 hash 值相同并且 key 相等
            e = p;
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            // 遍历链表
            for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {
                    // 没有当前 key 插入当前 key
                    p.next = newNode(hash, key, value, null);
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
        // key 存在
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                // 赋值
                e.value = value;
            afterNodeAccess(e);
            return oldValue;

        }
    }
    ++modCount;
    // 当前 size 大于阈值，重新计算 size
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

## 参考链接

- [JDK 源码中 HashMap 的 hash 方法原理是什么？](https://www.zhihu.com/question/20733617)
