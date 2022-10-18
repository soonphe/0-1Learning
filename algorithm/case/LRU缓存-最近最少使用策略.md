# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")

## LRU缓存-最近最少使用策略

### 题目
> 算法来源：146. LRU 缓存(https://leetcode-cn.com/problems/lru-cache/)

请你设计并实现一个满足 LRU (最近最少使用) 缓存 约束的数据结构。

实现 LRUCache 类：

LRUCache(int capacity) 以 正整数 作为容量capacity 初始化 LRU 缓存

int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。

void put(int key, int value)如果关键字key 已经存在，则变更其数据值value ；如果不存在，则向缓存中插入该组key-value 。如果插入操作导致关键字数量超过capacity ，则应该 逐出 最久未使用的关键字。

函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。

示例：
```
输入：
LRUCache(2)
set(2, 1)
set(1, 1)
get(2)
set(4, 1)
get(1)
get(2)
输出：[1,-1,1]
解释：
cache上限为2，set(2,1)，set(1, 1)，get(2) 然后返回 1，set(4,1) 然后 delete (1,1)，因为 （1,1）最少使用，get(1) 然后返回 -1，get(2) 然后返回 1。
```

提示：

1 <= capacity <= 3000
0 <= key <= 10000
0 <= value <= 105
最多调用 2 * 105 次 get 和 put


### 思路

数据结构的设计题，最近最少使用——使用频率低的元素排在前面，按照题意需要底层结构能够移动元素到末尾，考虑使用链表来减少移动带来的开销，底层元素是(key, value)的二元组，可以考虑将二元组包装成一个类，再加上next指针，还需要支持get和set操作，由于链表的查找需要遍历，get和set都需要查找当前元素是否已经存在，为了减少耗时，考虑使用哈希表来支持get和set操作。

> 因为在移动节点到链表尾部的操作中，需要修改前一个节点的next指针，所以需要有从当前节点能够获取前一个节点的方式。本题底层数据结构可以使用单链表或者双链表，单链表需要用map映射来记录每个节点的前一节点；双链表用prev指针记录每个节点的前一节点。

- 时间复杂度

使用链表做为底层数据结构，get操作在哈希表中查找耗时O(1)，并移动链表中的一个节点耗时O(1)，所以get操作的时间复杂度为O(1)；set操作在哈希表中查找耗时O(1)，并移动链表中的一个节点耗时O(1)，所以set操作的时间复杂度也为O(1)。

由于哈希表的优势，所以大大降低了时间复杂度。

- 空间复杂度

使用了哈希表和链表数据结构，空间复杂度为O(n)。

### 解题代码

解法一：哈希表 + 双链表

在双向链表的实现中，使用一个伪头部（dummy head）和伪尾部（dummy tail），代码实现简单
```
public class LRUCache {
    class DLinkedNode {
        int key;
        int value;
        DLinkedNode prev;
        DLinkedNode next;
        public DLinkedNode() {}
        public DLinkedNode(int _key, int _value) {key = _key; value = _value;}
    }

    private Map<Integer, DLinkedNode> cache = new HashMap<Integer, DLinkedNode>();
    private int size;
    private int capacity;
    private DLinkedNode head, tail;

    public LRUCache(int capacity) {
        this.size = 0;
        this.capacity = capacity;
        // 使用伪头部和伪尾部节点
        head = new DLinkedNode();
        tail = new DLinkedNode();
        head.next = tail;
        tail.prev = head;
    }

    public int get(int key) {
        DLinkedNode node = cache.get(key);
        if (node == null) {
            return -1;
        }
        // 如果 key 存在，先通过哈希表定位，再移到头部
        moveToHead(node);
        return node.value;
    }

    public void put(int key, int value) {
        DLinkedNode node = cache.get(key);
        if (node == null) {
            // 如果 key 不存在，创建一个新的节点
            DLinkedNode newNode = new DLinkedNode(key, value);
            // 添加进哈希表
            cache.put(key, newNode);
            // 添加至双向链表的头部
            addToHead(newNode);
            ++size;
            if (size > capacity) {
                // 如果超出容量，删除双向链表的尾部节点
                DLinkedNode tail = removeTail();
                // 删除哈希表中对应的项
                cache.remove(tail.key);
                --size;
            }
        }
        else {
            // 如果 key 存在，先通过哈希表定位，再修改 value，并移到头部
            node.value = value;
            moveToHead(node);
        }
    }

    private void addToHead(DLinkedNode node) {
        node.prev = head;
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
    }

    private void removeNode(DLinkedNode node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    private void moveToHead(DLinkedNode node) {
        removeNode(node);
        addToHead(node);
    }

    private DLinkedNode removeTail() {
        DLinkedNode res = tail.prev;
        removeNode(res);
        return res;
    }
}

```