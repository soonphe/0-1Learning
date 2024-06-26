# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## 数据结构和算法


### 数组与链表

#### 数组

创建

```
int c[] = {2,3,6,10,99};
int[] d = new int[10];
```

```
	/**
	 * 数组检索
	 * @param args
	 */
	public static void main(String[] args) {
		String name[];
		
		name = new String[5];
		name[0] = "egg";
		name[1] = "erqing";
		name[2] = "baby";
		
		for(int i = 0; i < name.length; i++){
			System.out.println(name[i]);
		}
	}
```
```
/**
	 * 插入
	 * 
	 * @param old
	 * @param value
	 * @param index
	 * @return
	 */
	public static int[] insert(int[] old, int value, int index) {  
        for (int k = old.length - 1; k > index; k--)  
            old[k] = old[k - 1];  
        old[index] = value;  
        return old;  
    }  

	/**
	 * 遍历
	 * 
	 * @param data
	 */
	public static void traverse(int data[]) {
		for (int j = 0; j < data.length; j++) {
			System.out.println(data[j] + " ");
		}
	}

	/**
	 * 删除
	 * 
	 * @param old
	 * @param index
	 * @return
	 */
	public static int[] delete(int[] old, int index) {
		for (int h = index; h < old.length - 1; h++) {
			old[h] = old[h + 1];
		}
		old[old.length - 1] = 0;
		return old;
	}
```

Tips:数组中删除和增加元素的原理：增加元素，需要将index后面的依次往后移动，然后将值插入index位置，删除则是将后面的值一次向前移动。

数组表示相同类型的一类数据的集合，下标从0开始。


#### 单链表

**链表的删除、插入、反向。**

链表反转
~~~~

    public static void main(String[] args) {
        Node node3 = new Node();
        node3.setVal(3);

        Node node2 = new Node();
        node2.setVal(2);
        node2.setNext(node3);

        Node node = new Node();
        node.setVal(1);
        node.setNext(node2);
        System.out.println(JSON.toJSONString(node));

        Node reverse = reverse(node);
        System.out.println(JSON.toJSONString(reverse));
    }
    
    @Data
    class Node{
        Node next;
        int val;
    }
    
public Node reverse(Node head) {
    if (head == null || head.next == null)
        return head;
    Node temp = head.next;
    Node newHead = reverse(head.next);
    temp.next = head;
    head.next = null;
    return newHead;
}
~~~~




**队列和栈，出栈与入栈。**

**字符串操作。**

**Hash表的hash函数，冲突解决方法有哪些。**

**各种排序：冒泡、选择、插入、希尔、归并、快排、堆排、桶排、基数的原理、平均时间复杂度、最坏时间复杂度、空间复杂度、是否稳定。**

**快排的partition函数与归并的Merge函数。**

**对冒泡与快排的改进。**

**二分查找，与变种二分查找。**

**二叉树、B+树、AVL树、红黑树、哈夫曼树。**

**二叉树的前中后续遍历：递归与非递归写法，层序遍历算法。**

二叉树的前序遍历：



**图的BFS与DFS算法，最小生成树prim算法与最短路径Dijkstra算法。**

**KMP算法。**

**排列组合问题。**

**动态规划、贪心算法、分治算法。（一般不会问到）**

**大数据处理：类似10亿条数据找出最大的1000个数.........等等**