# 贪心算法
---
---
## 1. 什么是贪心算法
贪心算法或贪心思想采用贪心的策略，保证每次操作都是局部最优的，从而使最后得到的结果是全局最优的。
## 2. 贪心算法策略和步骤
## 3. 问题一：分配问题
### 3.1 分饼干（AssignCookies）
1. 相似题目链接：https://leetcode-cn.com/problems/assign-cookies/
2. 题目描述：
有一群孩子和一堆饼干，每个孩子有一个饥饿度，每个饼干都有一个大小。每个孩子只能吃最多一个饼干，且只有饼干的大小大于孩子的饥饿度时，这个孩子才能吃饱。求解最多有多少孩子可以吃饱

3. 输入输出：
输入两个数组，分别代表孩子的饥饿度和饼干的大小。输出最多有多少孩子可以吃饱的数量。
如，输入：
```linux
input: [1, 2] [1, 2, 3]
output: 2
```
4. 题解
因为**饥饿度最小的孩子最容易吃饱，所以我们先考虑这个孩子**。为了尽量使得剩下的饼干可以满足饥饿度更大的孩子，所以我们应该把**大于等于这个孩子饥饿度的、且大小最小的饼干**给这个孩子。满足了这个孩子之后，我们采取同样的策略，考虑**剩下孩子里饥饿度最小的孩子**，直到没有满足条件的饼干存在。

简而言之，这里的贪心策略是，**给剩余孩子里最小饥饿度的孩子分配最小的能饱腹的饼干**。

```java
public class AssignCookies {

    public static int findContentChildren(int[] g, int[] s) {
        Arrays.sort(g);
        Arrays.sort(s);
        int numOfChildren = g.length, numOfCookies = s.length;
        int count = 0;
        for (int i = 0, j = 0; i < numOfChildren && j < numOfCookies; i++, j++) {
            while (j < numOfCookies && g[i] > s[j]) {
                j++;
            }
            if (j < numOfCookies) {
                count++;
            }
        }
        return count;
    }

    public static void main(String[] args) {
        int[] g = {1, 2};
        int[] s = {1, 2, 3};
        int res = findContentChildren(g, s);
        System.out.println(res);
    }
}
```



### 3.2 分发糖果（Candy）
1. 相似题目链接：https://leetcode-cn.com/problems/candy/
2. 题目描述：一群孩子站成一排，每一个孩子有自己的评分。现在需要给这些孩子发糖果，规则是如果一个孩子的评分比自己身旁的一个孩子要高，那么这个孩子就必须得到比身旁孩子更多的糖果；所有孩子至少要有一个糖果。求解最少需要多少个糖果

3. 输入输出：
输入是一个数组，表示孩子的评分。输出是最少糖果的数量
```linux
input: [1,0,2]
output: 5
```
在这个样例中，最少的糖果分法是[2,1,2]

4. 题解：

只需要简单的两次遍历即可：把所有孩子的糖果数初始化为1；先从左往右遍历一遍，如果右边孩子的评分比左边的高，则右边孩子的糖果数更新为左边孩子的糖果数加1；再从右往左遍历一遍，如果左边孩子的评分比右边的高，且左边孩子当前的糖果数不大于右边孩子的糖果数，则左边孩子的糖果数更新为右边孩子的糖果数加1。通过这两次遍历，分配的糖果就可以满足题目要求了。

贪心策略：在每次遍历中，只考虑并更新相邻一侧的大小关系