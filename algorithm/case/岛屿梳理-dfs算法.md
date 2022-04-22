# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## 独一无二的出现次数

### 题目
给你一个由 ‘1’（陆地）和 ‘0’（水）组成的的二维网格，请你计算网格中岛屿的数量。岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。此外，你可以假设该网格的四条边均被水包围。

示例 1:

输入:
```
11110
11010
11000
00000
```
输出: 1

示例 2:

输入:
```
11000
11000
00100
00011
```
输出: 3

解释: 每座岛屿只能由水平和/或竖直方向上相邻的陆地连接而成。

### 思路：

遍历岛这个二维数组，如果当前数为1，即为岛，则执行dfs函数向四周探查，进入探测并将岛个数+1：dfs函数就是一个递归标注的过程，它会将所有相连的1都标注成2（也可以标注其他数字，目的是标记此处，以防止重复遍历），这样就避免了遍历过程中的重复计数的情况，也大大降低了dfs的次数。

### Java代码实现：
```
class Solution {
    public int numIslands(char[][] grid) {
    	//定义一个统计岛屿的变量
        int islandNum = 0;
        //循环遍历岛
        for(int i = 0; i < grid.length; i++){
            for(int j = 0; j < grid[0].length; j++){
            	//如果为1，则进行dfs探测，找出周围相连的所有岛，构成一个岛屿
                if(grid[i][j] == '1'){
                    dfs(grid, i, j);
                    islandNum++;
                }
            }
        }
        return islandNum;
    }
    //探测函数
    public void dfs(char[][] grid, int i, int j){
    	//判断当前岛的边界及是否为岛的条件
        if(i < 0 || i >= grid.length ||
           j < 0 || j >= grid[0].length || grid[i][j] != '1'){
            return;
        }
        //如果是岛，则设置标记为2，防止重复遍历
        grid[i][j] = '2';
        //探测当前岛的下面是否为岛
        dfs(grid, i + 1, j);
        //探测当前岛的上面是否为岛
        dfs(grid, i - 1, j);
        //探测当前岛的右边面是否为岛
        dfs(grid, i, j + 1);
        //探测当前岛的左面是否为岛
        dfs(d, i, j - 1);
    }
}
```

总结：
此题为经典算法之一，dfs解决迷宫问题。难点在于如何确定岛的周围有多少相连的岛，我们采用dfs算法，从一个岛发散到周围所有相连的岛，递归出所有可能的岛。
