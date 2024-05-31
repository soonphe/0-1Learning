# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")

## algorithm-overview

### 时空复杂度（时间复杂度/空间复杂度）O(1)、O(n)、O(n^2)、O(log n)、O(n log n)
O后面的括号中有一个函数，指明某个算法的耗时/耗空间与数据增长量之间的关系。其中的n代表输入数据的量。

- O(1)：O(1)就是最低的时空复杂度了，也就是耗时/耗空间与输入数据大小无关，无论输入数据增大多少倍，耗时/耗空间都不变。哈希算法就是典型的O(1)时间复杂度，无论数据规模多大，都可以在一次计算后找到目标（不考虑冲突的话），冲突的话很麻烦的，指向的value会做二次hash到另外一快存储区域。
- O(n)：时间复杂度为O(n)，就代表数据量增大几倍，耗时也增大几倍。比如常见的遍历算法。
- O(n^2)：时间复杂度O(n^2)，就代表数据量增大n倍时，耗时增大n的平方倍，这是比线性更高的时间复杂度。比如冒泡排序，就是典型的O(n^2)的算法，对n个数排序，需要扫描n×n次。
- O(log n)：当数据增大n倍时，耗时增大log n倍（这里的log是以2为底的，比如，当数据增大256倍时，耗时只增大8倍，是比线性还要低的时间复杂度）。二分查找就是O(log n)的算法，每找一次排除一半的可能，256个数据中查找只要找8次就可以找到目标。
- O(n log n)：就是n乘以log n，当数据增大256倍时，耗时增大256*8=2048倍。这个复杂度高于线性低于平方。归并排序就是O(n log n)的时间复杂度。

### 冒泡排序：

#### 原理
（相邻交换n×n）

重复地走访过要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端。----- 来自 [wikipedia](https://zh.wikipedia.org/wiki/%E5%86%92%E6%B3%A1%E6%8E%92%E5%BA%8F)

#### 算法规则：
我们可以将待排序集合(0...n)看成两部分，一部分为(k..n)的待排序unsorted集合，另一部分为(0...k)的已排序sorted集合，
每一次都在unsorted集合从前往后遍历，选出一个数，如果这个数比其后面的数大，则进行交换。
完成一轮之后，就肯定能将这一轮unsorted集合中最大的数移动到集合的最后，并且将这个数从unsorted中删除，移入sorted中。

#### 代码实现
```
public void sort(int[] args) 
        {
        	//第一层循环从数组的最后往前遍历
    		for (int i = args.length - 1; i > 0 ; --i) {
                //这里循环的上界是 i - 1，在这里体现出 “将每一趟排序选出来的最大的数从sorted中移除”
    			for (int j = 0; j < i; j++) {
                    //保证在相邻的两个数中比较选出最大的并且进行交换(冒泡过程)
    				if (args[j] > args[j+1]) {
    					int temp = args[j];
    					args[j] = args[j+1];
    					args[j+1] = temp;
    				}
    			}
    		}
	    }
```

### 选择排序：

#### 原理
（比较交换n×n）

选择排序（Selection sort）是一种简单直观的排序算法。它的工作原理如下。首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。 ----- 来自 [wikipedia](https://zh.wikipedia.org/wiki/%E9%80%89%E6%8B%A9%E6%8E%92%E5%BA%8F)

#### 算法规则：
将待排序集合(0...n)看成两部分，在起始状态中，一部分为(k..n)的待排序unsorted集合，另一部分为(0...k)的已排序sorted集合,
在待排序集合中挑选出最小元素并且记录下标i，若该下标不等于k，那么 unsorted[i] 与 sorted[k]交换 ，
一直重复这个过程，直到unsorted集合中元素为空为止。

#### 代码实现（Java版本）
```
public void sort(int[] args) 
{
        int len = args.length;
        for (int i = 0,k = 0; i < len; i++,k = i) {
            // 在这一层循环中找最小
            for (int j = i + 1; j < len; j++) {
                // 如果后面的元素比前面的小，那么就交换下标，每一趟都会选择出来一个最小值的下标
                if (args[k] > args[j]) k = j;
    		}
    
    		if (i != k) {
    			int tmp = args[i];
    			args[i] = args[k];
    			args[k] = tmp;
    		}
    	}
    }
```


### topN堆排序 

#### 原理
topk问题：寻找一数组中前K个最大的数——使用堆排序n×logk

堆排序是借助堆来实现的选择排序，思想同简单的选择排序，以下以大顶堆为例。
注意：如果想升序排序就使用大顶堆，反之使用小顶堆。(原因是堆顶元素需要交换到序列尾部。)

它重复地走访过要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端。

#### 算法规则：
选出K个值，排序，找出最小值，新数字如果大于最小的。删除换最小值。排序。重复。（如果有数字重复咋办——每一次都有排序已解决）

首先，实现堆排序需要解决两个问题：
1. 如何由一个无序序列键成一个堆？
2. 如何在输出堆顶元素之后，调整剩余元素成为一个新的堆？

第一个问题，可以直接使用线性数组来表示一个堆，由初始的无序序列建成一个堆就需要自底向上从第一个非叶元素开始挨个调整成一个堆。
第二个问题，怎么调整成堆？首先是将堆顶元素和最后一个元素交换。然后比较当前堆顶元素的左右孩子节点，因为除了当前的堆顶元素，左右孩子堆均满足条件，这时需要选择当前堆顶元素与左右孩子节点的较大者（大顶堆）交换，直至叶子节点。我们称这个自堆顶自叶子的调整成为筛选。
从一个无序序列建堆的过程就是一个反复筛选的过程。若将此序列看成是一个完全二叉树，则最后一个非终端节点是n/2取底个元素，由此筛选即可。

#### 代码实现（Java版本）
```
public class HeapSort {

    /**
     * 堆筛选，除了start之外，start~end均满足大顶堆的定义。
     * 调整之后start~end称为一个大顶堆。
     * @param arr 待调整数组
     * @param start 起始指针
     * @param end 结束指针
     */
    public static void heapAdjust(int[] arr, int start, int end) {
        int temp = arr[start];

        for(int i=2*start+1; i&lt;=end; i*=2) {
            //左右孩子的节点分别为2*i+1,2*i+2

            //选择出左右孩子较小的下标
            if(i &lt; end &amp;&amp; arr[i] &lt; arr[i+1]) {
                i ++; 
            }
            if(temp &gt;= arr[i]) {
                break; //已经为大顶堆，=保持稳定性。
            }
            arr[start] = arr[i]; //将子节点上移
            start = i; //下一轮筛选
        }

        arr[start] = temp; //插入正确的位置
    }

    public static void heapSort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;

        //建立大顶堆
        for(int i=arr.length/2; i&gt;=0; i--) {
            heapAdjust(arr, i, arr.length-1);
        }

        for(int i=arr.length-1; i&gt;=0; i--) {
            swap(arr, 0, i);
            heapAdjust(arr, 0, i-1);
        }

    }

    public static void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }

}
```


### 快速排序

#### 原理
快排（先总在分nlogn）：任选一个数据（通常选用数组的第一个数）作为关键数据，
然后将所有比它小的数都放到它左边，所有比它大的数都放到它右边，这个过程称为一趟快速排序
然后再分解数组，直到数组不能再分解为止（只有一个数据）

例：
```
5 6 2 7 3 8 9（选择第一个数组元素作为key=5，i=0，j=6，比key小在左，比key大在右）

j不断--，i不断++

3 6 2 7 5 8 9（从右开始，先找比5小的数，i=0 j=4）这里的i已经判断并交换，可以置为1，从下一个数开始
3 5 2 7 6 8 9（从左开始，再找比5大的数，i=1 j=4）道理同理，这里的j也可以++，置为3
3 2 5 7 6 8 9（从右继续，再找比5小的数，i=1 j=2）道理同理，这里的也可以++，置为2
当i=j时，本次循环停止
```
在平均状况下，排序n个项目要Ο(n log n)次比较。在最坏状况下则需要Ο(n2)次比较，但这种状况并不常见。
事实上，快速排序通常明显比其他Ο(n log n)算法更快，因为它的内部循环（inner loop）可以在大部分的架构上很有效率地被实现出来

#### 算法规则： 本质来说，快速排序的过程就是不断地将无序元素集递归分割，一直到所有的分区只包含一个元素为止。 
由于快速排序是一种分治算法，我们可以用分治思想将快排分为三个步骤：
1.分：设定一个分割值，并根据它将数据分为两部分
2.治：分别在两部分用递归的方式，继续使用快速排序法 
3.合：对分割的部分排序直到完成

#### 代码实现（Java版本）
```
    public static void main(String[] args) {
        int[] arr = new int[]{5,6,2,7,3,8,9};
        sort(arr,0,6);
        System.out.println(Arrays.toString(arr));
    }

    public static void sort(int[] args, int start, int end)
    {
        //当分治的元素大于1个的时候，才有意义
        if ( end - start > 1) {
            int mid = 0;
            mid = dividerAndChange(args, start, end);
            // 对左部分排序
            sort(args, start, mid);
            // 对右部分排序
            sort(args, mid + 1, end);
        }
    }

    public static int dividerAndChange(int[] args, int start, int end)
    {
        //标准值
        int pivot = args[start];
        while (start < end) {
            // 从右向左寻找，一直找到比参照值还小的数值，进行替换
            // 这里要注意，循环条件必须是 当后面的数 小于 参照值的时候
            // 我们才跳出这一层循环
            while (start < end && args[end] >= pivot)
                end--;

            if (start < end) {
                swap(args, start, end);
                start++;
            }

            // 从左向右寻找，一直找到比参照值还大的数组，进行替换
            while (start < end && args[start] < pivot)
                start++;

            if (start < end) {
                swap(args, end, start);
                end--;
            }
        }
        //重置指针为标准值
        args[start] = pivot;
        return start;
    }

    //每次只会有一个值重复
    private static void swap(int[] args, int fromIndex, int toIndex)
    {
        args[fromIndex] = args[toIndex];
    }
```

一次排序打印：
```
{5,6,2,7,3,8,9}
[3, 6, 2, 7, 3, 8, 9]
[3, 6, 2, 7, 6, 8, 9]
[3, 2, 2, 7, 6, 8, 9]

排序完
[3, 2, 5, 7, 6, 8, 9]
```


### 归并排序：

#### 原理
（先分在总n log n） ：也称二路归并——递归调用
```
8
0-4	5-8
0-2 3-4	5-6 7-8
0-1 1 3-4	5-6 7-8
```

#### 算法规则： 像快速排序一样，由于归并排序也是分治算法，因此可使用分治思想：
1. 申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列
2. 设定两个指针，最初位置分别为两个已经排序序列的起始位置 
3. 比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动此指针到下一位置，另一指针不动 
4. 重复步骤3直到某一指针到达序列尾   5.将另一序列剩下的所有元素直接复制到合并序列尾

#### 代码实现（Java版本）
```   
 public static int[] mergeSort(int[] nums, int l, int h) {
         if (l == h)
             return new int[] { nums[l] };
          
         int mid = l + (h - l) / 2;
         int[] leftArr = mergeSort(nums, l, mid); //左有序数组
         int[] rightArr = mergeSort(nums, mid + 1, h); //右有序数组
         int[] newNum = new int[leftArr.length + rightArr.length]; //新有序数组
          
         int m = 0, i = 0, j = 0; 
         while (i < leftArr.length && j < rightArr.length) {
             newNum[m++] = leftArr[i] < rightArr[j] ? leftArr[i++] : rightArr[j++];
         }
         while (i < leftArr.length)
             newNum[m++] = leftArr[i++];
         while (j < rightArr.length)
             newNum[m++] = rightArr[j++];
         return newNum;
     }
```


### 插入排序
#### 原理
插入排序不是通过交换位置而是通过比较找到合适的位置插入元素来达到排序的目的的。
例如打扑克，就是拿到一张牌，找到一个合适的位置插入。

例子：
对5,3,8,6,4这个无序序列进行简单插入排序，首先假设第一个数的位置时正确的，想一下在拿到第一张牌的时候，没必要整理。
然后3要插到5前面，把5后移一位，变成3,5,8,6,4.想一下整理牌的时候应该也是这样吧。然后8不用动，6插在8前面，8后移一位，4插在5前面，从5开始都向后移一位。 注意在插入一个数的时候要保证这个数前面的数已经有序。
简单插入排序的时间复杂度也是O(n^2)。
#### 代码
```
public class InsertSort {
    public static void insertSort(int[] arr) {
        if(arr == null || arr.length == 0)
            return ;
        for(int i=1; i <= arr.length; i++) { //假设第一个数位置时正确的；要往后移，必须要假设第一个。
            int j = i;
            int target = arr[i]; //待插入的
            //后移
            while(j >= 0 && target <= arr[j-1]) {
                arr[j] = arr[j-1];
                j --;
            }
            //插入 
            arr[j] = target;
        }
    }
}
```
