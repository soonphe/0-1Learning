# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## 给定字符串的第K个按字典顺序最小的唯一子字符串

### 问题：给定字符串 s 。求按字典顺序打印第 k 个 s 的不同子字符串中最小的一个。
说明：s的子字符串是通过去除s中的非空连续部分而获得的字符串。
例如，如果s = ababc，则a，bab和ababc是s的子字符串，而ac，z和空字符串则不是。另外，我们说子字符串与原字符串不同时需要再判断。

- 给定 s = “aba”, k = 4 输出：b
- 给定 s = “geeksforgeeks”, k = 5 输出：eeksf

### 代码实现
```
/**
 * 最小/最大字典序
 *
 * @author soonphe
 * @since 1.0
 */
class StringTest {

    public static void main(String[] args) {
        String s = "geeksforgeeks";
        int k = 5;
        kThLexString(s, k);
    }

    /**
     * 按字典顺序打印第K个s的不同子字符串中最小的一个
     * @param st 输入的字符串
     * @param k 位
     */
    public static void kThLexString(String st, int k) {
        int n = st.length();
        // 用Set存储字符串，保证子串唯一
        Set<String> z = new HashSet<String>();
        for (int i = 0; i < n; i++) {
            // 字符串来创建每个子字符串
            String pp = "";
            // 控制字符串长度，不大于k，取值不超过n
            System.out.println("--");
            for (int j = i; j < i + k; j++) {
                if (j >= n) {
                    break;
                }
                pp += st.charAt(j);
                boolean add = z.add(pp);
                System.out.println(pp+"-"+add);
            }
        }
        //将集合转换为列表
        Vector<String> fin = new Vector<String>();
        fin.addAll(z);
        //对列表中的字符串按字典顺序排序
        Collections.sort(fin);
        //反向排序求最大字符串
//        Collections.reverse(fin);
        System.out.print(fin.get(k - 1));
    }
}
```
