# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Java进阶-随机数


### 将一个正整数随机拆分
题目：给定每个随机数sum，拆分成n份，最小值m
条件：sum>1，n>1，m>1，sum>=n*m.

实现思路：
在给定最小值的情况下，那么要拆分的就是sum-n*m，所以，随机范围选择 temp = sum-n*m 就可以了，最后统一加上最小值m就行。

#### 常规思路
思路1，每次在temp中随机，然后重新赋值剩余的值给temp，直到不可分为止。

*请注意，随机因子random为0.0-1.0，即有50%的概率，第一次就能分一半以上，这就不够随机了，或者说不够均匀了，谁越靠前，能得到的数就越多，越靠后连汤都捞不着，对其他数不公平。*

如果第一次随机因子random为1.0，那这个temp第一次就被分光了，其他数就都是最小值。（当然这是小部分情况）

思路2，对temp等分，取值temp/n，或者加上一些小的random，但是这样过于均匀了，没有随机概念出现了。


#### 思路
要符合随机分布的特性，我们就不能对temp做重新赋值，随机的temp不能变，那应该如何达到呢，我们可以考虑新的计算方式。

说来说去要切分就是temp，随机返回还是 temp = sum-n*m，但是我们可以固定两个数，0和temp，分别是最大值和最小值，中间我们再找n-1个数就行了。

结果集为：0...random(temp)...temp。

每次取值都是后一个数减去前一个数，将此作为`随机增量`，不管这里的`random(temp)`有多少个，不管`random(temp)`多大，最后总的要切分的值的总和都是temp。

如n为2：
```
0，random(temp)，temp
```
第一个值：random(temp)-0
第一个值：temp-random(temp)

如n为3：
```
0，random1(temp)，random2(temp)，temp
```
第一个值：random1(temp)-0
第三个值：random2(temp)-random1(temp)
第二个值：temp-random2(temp)

*记住，要对整个数组从低到高进行排序，不然如果第一个值为最大值，后面的就到分不到了*。

总和永远都是temp，每个数之间的差值作为随机增量，random随机切分的次数越多，0-temp之间就分布的越均匀，随机增量也就几乎稳定在一个值附近了。

最后的结果值就是 `最小值m+随机增量`.

理想的结果当然是得到如阶乘一般的结果，至少要保证各阶段的数出现的频率是相近的，不要出现极值的情况。

如10的阶乘：sum=55，拆分成n=10份，最小值m=1；
理想切分结果：
```
1，2，3，4，5，6，7，8，9，10
```


#### 实现代码

示例：随机数10，拆分成3份，最小值1
```
中间态：
0   0   0   0
0   0   1   7
0   6   2   7
7   7   7   7

实际结果：
1   1   2   8
1   7   2   1
8   2   6   1
```

```
  /**
   * 把一个正整数随机拆分成count个正整数
   *
   * @param n   拆分个数
   * @param sum 被拆数
   * @param m   最小数不能小于的数
   * @return
   */
  public static List<Integer> getRandomBonus(int n, int sum, int m) {
    //随机抽取n-1个小于sum的数
    List<Integer> list = new ArrayList();
    //将0和sum加入到里list中
    list.add(0);
    //每个数至少为m
    sum = sum - n * m;
    list.add(sum);

    //生成n-1个小于sum的随机数
    int temp = 0;
    for (int i = 0; i < n - 1; i++) {
      temp = (int) (Math.random() * sum);
      list.add(temp);
    }
    Collections.sort(list);
    List nums = new ArrayList();
    for (int i = 0; i < n; i++) {
      //每份最少为m
      nums.add(list.get(i + 1) - list.get(i) + m);

    }
    return nums;
  }
```

### 生成不重复随机数组
生成不重复随机数 根据给定的最小数字和最大数字，以及随机数的个数，产生指定的不重复的数组

创建最小到最大数字之间长度n的数组，i=0，随机范围为n-i，取值为数组[Random.nextInt(n-i)];

使用交换机制，每轮将被使用的数与位置在 `n-i` 的数交换，i++，即随机数范围-1。目的就是将被使用的数排除在下一轮随机范围外。

这样在n-i的随机范围内，数字都是没有被使用过的。
```
/**
	 * 生成不重复随机数 根据给定的最小数字和最大数字，以及随机数的个数，产生指定的不重复的数组
	 *
	 * @param begin 最小数字（包含该数）
	 * @param end   最大数字（不包含该数）
	 * @param size  指定产生随机数的个数
	 * @return 随机int数组
	 */
	public static int[] generateRandomNumber(int begin, int end, int size) {
		if (begin > end) {
			int temp = begin;
			begin = end;
			end = temp;
		}
		// 加入逻辑判断，确保begin<end并且size不能大于该表示范围
		if ((end - begin) < size) {
			throw new UtilException("Size is larger than range between begin and end!");
		}
		// 种子你可以随意生成，但不能重复
		int[] seed = new int[end - begin];

		for (int i = begin; i < end; i++) {
			seed[i - begin] = i;
		}
		int[] ranArr = new int[size];
		Random ran = new Random();
		// 数量你可以自己定义。
		for (int i = 0; i < size; i++) {
			// 得到一个位置
			int j = ran.nextInt(seed.length - i);
			// 得到那个位置的数值
			ranArr[i] = seed[j];
			// 将最后一个未用的数字放到这里
			seed[j] = seed[seed.length - 1 - i];
		}
		return ranArr;
	}
```