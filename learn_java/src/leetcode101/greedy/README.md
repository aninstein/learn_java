# 贪心算法

---

---
## 1. 什么是贪心算法
贪心算法或贪心思想采用贪心的策略，保证每次操作都是局部最优的，从而使最后得到的结果是全局最优的。
## 2. 贪心算法策略和步骤
贪心算法的核心，在于需要确定“贪心策略”，贪心策略决定了题目代码的设计，而贪心算法的贪心策略，首先得区分它是哪一种类型的问题。

首先贪心算法先要考虑以下两个问题：
- 这个问题是否能够使用贪心策略
- 如何选择贪心标准，以得到问题的最优/较优解

贪心算法存在如下问题：
1. 不能保证解释最佳的。因为贪心算法总是从局部出发，并没有从整体考虑。
2. 贪心算法一般用来解决求最大或最小解。
3. 贪心算法只能确定某些问题的可行性范围。

因此贪心算法得出的结果很可能并不是最优解，而是一个相对可行的方案

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

只需要简单的两次遍历即可：把所有孩子的糖果数初始化为1；
先从左往右遍历一遍，如果右边孩子的评分比左边的高，则右边孩子的糖果数更新为左边孩子的糖果数加1；再从右往左遍历一遍，如果左边孩子的评分比右边的高，且左边孩子当前的糖果数不大于右边孩子的糖果数，则左边孩子的糖果数更新为右边孩子的糖果数加1。通过这两次遍历，分配的糖果就可以满足题目要求了。

贪心策略：在每次遍历中，只考虑并更新相邻一侧的大小关系
```java
public class HardAssignCandy {

    public static int assignCandy(int[] ratings) {
        if (ratings == null || ratings.length == 0) {
            return 0;
        }
        int dataLen = ratings.length;
        int[] candys = new int[dataLen];
        for (int i = 0; i < dataLen; i++) {
            candys[i] = 1;
        }

        for (int i = 1; i < dataLen; i++) {
            if (ratings[i] > ratings[i-1]) {
                candys[i] += candys[i-1];
            }
        }

        int sum = candys[dataLen-1];
        for (int i = dataLen-1; i > 0; i--) {
            if (ratings[i] < ratings[i-1]) {
                candys[i-1] = Math.max(candys[i-1], candys[i] + 1);  // 如果已经比前一个大了，这时候就不需要加了
            }
            sum += candys[i-1];
        }
        return sum;
    }

    public static void main(String[] args) {
        int[] data = {1, 0, 2};
        int res = assignCandy(data);
        System.out.println(res);
    }

}
```
## 4. 问题二：区间问题
### 4.1 无重叠区间
1. 题目链接：https://leetcode-cn.com/problems/non-overlapping-intervals/
2. 题目描述：给定多个区间，计算让这些区间互不重叠所需要移除区间的最少个数。起止相连不算重叠。
3. 输入输出
输入是一个数组，数组由多个长度固定为2的数组组成，表示区间的开始和结尾。
输出一个整数，表示需要移除的区间数量。
```linux
Input:[[1,2],[2,4],[1,3]]
Output:1
```

在这个样例中，我们可以移除区间[1,3]，使得剩余的区间[[1,2],[2,4]]互不重叠。

4. 题解

在选择要保留区间时，区间的结尾十分重要：选择的区间结尾越小，余留给其它区间的空间就越大，就越能保留更多的区间。因此，我们采取的贪心策略为，**优先保留结尾小且不相交的区间**。

具体实现方法为，先把区间按照结尾的大小进行增序排序，每次选择结尾最小且和前一个选择的区间不重叠的区间。

在样例中，排序后的数组为[[1,2],[1,3],[2,4]]。按照我们的贪心策略，首先初始化为区间[1,2]；由于[1,3]与[1,2]相交，我们跳过该区间；由于[2,4]与[1,2]不相交，我们将其保留。因此最终保留的区间为[[1,2],[2,4]]
```java
public class EraseOverlapIntervals {

    /*
    * 题目：无重叠区间
    * 题目链接：https://leetcode-cn.com/problems/non-overlapping-intervals/
    *
    * 题解：
    * 在选择要保留区间时，区间的结尾十分重要：
    * 选择的区间结尾越小，余留给其它区间的空间就越大，就越能保留更多的区间。
    *
    * 因此，我们采取的贪心策略为：优先保留结尾小且不相交的区间。
    *
    * 具体实现方法为，先把区间按照结尾的大小进行增序排序，每次选择结尾最小且和前一个选择的区间不重叠的区间。
    * 我们这里使用Lambda，进行自定义排序。
    *
    * 在样例中，排序后的数组为[[1,2],[1,3],[2,4]]。
    * 按照我们的贪心策略，首先初始化为区间[1,2]；由于[1,3]与[1,2]相交，我们跳过该区间；
    * 由于[2,4]与[1,2]不相交，我们将其保留。
    * 因此最终保留的区间为[[1,2],[2,4]]。
    * */
    public static int eraseOverlapIntervals(int[][] intervals) {
        if (intervals == null || intervals.length == 0) {
            return 0;
        }

        // lambda表达式，按照那结尾排序
        Arrays.sort(intervals, new Comparator<int[]>() {
            public int compare(int[] interval1, int[] interval2) {
                return interval1[1] - interval2[1];
            }
        });

        int dataLen = intervals.length;
        int removeNumber = 0;
        int prev = intervals[0][1];  // 第一个区间的结尾，当下一个区间的开头小于这个结尾的时候就表示有重叠
        for (int i = 1; i < dataLen; i++) {
            if (intervals[i][0] < prev) {
                removeNumber++;
            } else {
                prev = intervals[i][1];
            }
        }
        return removeNumber;
    }

    public static void main(String[] args) {
        int[][] data = {{1,2}, {2,3}, {3,4}, {1,3}};
        int res = eraseOverlapIntervals(data);
        System.out.println(res);
    }

}
```