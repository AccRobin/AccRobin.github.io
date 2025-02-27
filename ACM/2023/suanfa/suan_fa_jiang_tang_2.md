# 算法讲堂第二期
$$
\text{贪心，二分，前缀和与差分，快速幂，动态规划基础}
$$

---
## 贪心

- 贪心算法即是在对于问题的每一步行动，都采取当前状态下的最优策略，以期望达到最终的全局最优
- 在竞赛中，贪心算法更多依靠直觉和经验
- 证明：通常采用反证法和归纳法

@@

## 贪心实例 1：Songs Compression

> 现有 $n$（$1 \le n \le {10}^5$）个物品，第 $i$ 个物品的体积为 $a_i$，可以进行 $1$ 次操作使 $a_i$ 变为 $b_i$（保证 $1 \le b_i<a_i \le {10}^9$）。
> 问最少多少次操作后能将所有物品装入空间为 $m$（$1 \le m \le {10}^9$）的背包。
> 若无论如何也无法完成，请输出 `-1`。

- 每次操作将差最大的进行交换即可

@@

## 贪心实例 2：排序不等式
> 给定两个长度为 $n$ 的数组 $a$ 和 $b$，对 $a$ 和 $b$ 重新排序
> 
> 要求最小化 $\sum\limits_{i=1}^na_ib_i$ 

排序不等式：顺序积 $\leq$ 乱序积 $\leq$ 逆序积 

@@

## 贪心实例 2：排序不等式

简要证明：
- 首先固定 $b$ 的顺序从小到大，只改变 $a$ 的顺序对结果无影响
- 假设 $a$ 为乱序，每次交换 $a[i], a[j](a[i] > a[j])$ 两数组积的的变化 
$$
\Delta = (a_i - a_j)(b_i - b_j)
$$
- 因为 $a[i] \geq a[j], b[i] \leq b[j]$ 所以可知 $\Delta \leq 0$，故每次交换一组逆序对会让结果更优
- 当无法继续交换，此时 $a$ 从小到大排列，逆序积 $\geq$ 乱序积同理

@@

## 贪心实例 3：Roman Master
> 合法罗马数字：I=1, II=2, III=3, IV=4, V=5, VI=6, VII=7, VIII=8
> 给定一个只包含 `V` 和 `I` 的字符串 $s$，要求对 $s$ 划分成若干个区域，每个区域必须为上述罗马数字中的一个，其中第 $i$ 个区域的值等于其对应的十进制数，使得组成的十进制数最小。

- 约束1：划分需要让构成的十进制数尽可能短 
- 约束2：对于等长的十进制数，其字典序要尽可能小

贪心策略：从后往前划分，每次划分的段尽可能长

@@

## 贪心实例 3：Roman Master
简要证明：
- 定义 $f(s)$ 为 $s$ 串划分成的最短十进制数，$g(s)$ 为 $s$ 串划分成的最小十进制数
- 对于约束1，假设 $s[1 , r]$ 未划分，若 $s[l-1 , r]$ 和 $s[l, r]$ 都可以构成一个合法罗马数字。考虑划分 $s[l , r]$，则对于 $s[1 , l-2]$ 来说，多出来的 $s[l - 1]$ 要么可以和前一个区域合并，要么单独成为一个区域，即 $f(s[1 , l - 1]) \geq f(s[1 , l - 2])$ ，故划分 $s[l - 2 , r]$ 比划分 $s[l - 1 , r]$ 更优
- 对于约束2，同样考虑划分 $s[l-1 , r]$ 还是 $s[l , r]$。若划分 $s[l , r]$，则只需考虑 $s[l-1]$ 和前一个区域合并会产生什么效果。当 $s[l-1] = I$，则由表可知多一个 'I' 会使得对应的值更大；当 $s[l-1] = V$ 同样如此。即 $g(s[1, l - 1]) \geq g(s[1, l - 2])$

---

## 二分

- 每次通过询问中间点来对查找区间进行缩小
- 要求查找空间对于询问具有单调性
- 二分查找（`lower_bound`, `upper_bound`），二分答案
- “最大值最小”，“最小值最大”

```cpp
// 查找最小满足条件的值
while (l < r) {
    int mid = l + r >> 1;
    if (check(mid)) r = mid;
    else l = mid + 1;
}
// 查找最大满足条件的值
while (l < r) {
    int mid = l + r + 1 >> 1;
    if (check(mid)) l = mid;
    else r = mid - 1;
}
```

@@

## 二分答案实例1：跳石头
> “跳石头”比赛在一条笔直的河道中进行，河道中分布着一些巨大岩石。
> 
> 组委会已选择了两块岩石作为比赛起点和终点。而在起点和终点之间有 $N$ 块岩石（不含起点和终点的岩石），选手们将从起点出发，跳向相邻的岩石，直至到达终点。
>
> 为了提高比赛难度，组委会将从起点和终点之间移走不超过 $M$ 块岩石（不能移走起点和终点的岩石），使得选手们在比赛过程中的最短跳跃距离尽可能长。

- 二分枚举答案，二分区间即 $[1, L]$
- 单调性：若最短跳跃距离为 $x$，移除的石头数 $y \leq M$，则对于所有的 $x' < x$，都有 $y' \leq y \leq M$，即说明在该询问上单调。

@@

## 二分答案实例2：Max Median
> 给定一个长度为 $n$ 的序列 $a$，求所有长度 $\ge k$ 的连续子序列中，中位数的最大值。定义中位数是一个长度为 $x$ 的序列升序排序后的第 $\left\lfloor\frac{x+1}{2}\right\rfloor$ 位的值。

- 二分区间为 $[1, n]$，查找的是符合条件的中位数的下界最大值
- 这里并不满足：对于中位数 $x$ 满足条件则 $x-1$ 一定符合条件。但是若存在 $median \geq x$ 满足条件，则一定存在 $median \geq x - 1$
- 故我们的询问需要判断：对于 $mid$，是否存在大于等于 $mid$ 的中位数满足条件。

---

## 前缀和/差分
- 前缀和可以简单理解为用一个长度为 $n$ 的 $sum$ 数组记录原数组 $a$ 前 $i$ 项和
- 计算 $\sum\limits_{i=l}^{r}a[i] = sum[r] - sum[l - 1]$
- 而差分是一种和前缀和相对的策略，可以当做是求和的逆运算。这种策略是令
`$$
b_i = \begin{cases}
a_i - a_{i - 1} & i \in [2, n] \\
a_1 & i = 1
\end{cases}
$$`
- 差分数组可以将原数组用 $a[i] = \sum\limits_{j=1}^{i}b[j]$

@@ 

## 前缀和实例1：[蓝桥杯 2022 省 A] 求和

> 给定 $n$ 个整数 $a_{1}, a_{2}, \cdots, a_{n}$, 求它们两两相乘再相加的和，即
> `$$
\begin{aligned}
S=a_{1} \cdot a_{2} +a_{1} \cdot a_{3}+\cdots+a_{1}\cdot a_{n}&\\
                    +a_{2} \cdot a_{3}+\cdots+a_2\cdot a_n&\\
                    \cdots\\
                    +a_{n-1} \cdot a_{n}&\\
\end{aligned}
$$`

- $S = \sum\limits_{i=1}^{n - 1}a[i]\sum\limits_{j=i + 1}^{n}a[j]$
- 可以使用前缀和将式子转化为 $S = \sum\limits_{i=1}^{n - 1}a[i]\times(s[n] - s[i])$

@@

## 差分实例2：语文成绩

> 第一行有两个整数 $n$，$p$，代表学生数与增加分数的次数。
> 第二行有 $n$ 个数，$a_1 \sim a_n$，代表各个学生的初始成绩。
> 接下来 $p$ 行，每行有三个数，$x$，$y$，$z$，代表给第 $x$ 个到第 $y$ 个学生每人增加 $z$ 分。

- 每次操作相当于让原数组 $[x,y]$ 位置上加 $z$，考虑对差分数组进行操作
- 对于差分数组 $b$，想要实现同样的效果，只需让 $b[x] + z, b[y + 1] - z$
---

## 分治

- 分而治之，首先将原问题转换为两个或更多个相同或相似的子问题
- 直到最后的子问题可以简单求解
- 原问题的解可以通过子问题合并解决

@@

## 分治实例1：归并排序
> 给定一个长度为 $n$ 的数组 $a$，将其从小到大排序

- 分解问题，考虑每次解决长度减半的子问题
- 解决最小子问题，当子问题长度为1时，显然是一个排好序的数组
- 合并子问题，对于已经排好序的两个数组，考虑如何将其合并为一个有序数组
- 只需创建一个辅助数组c，每次比较两个子数组中最小的未选数即可

@@

## 分治实例2：最大子段和
>  给出一个长度为 $n$ 的序列 $a$，选出其中连续且非空的一段使得这段和最大。
- 分解问题，考虑每次解决长度减半的子问题
- 解决最小子问题，最小子问题的最大子段和显然为其本身
- 合并子问题，对于一个数组 $a[l,r]$，划分出左子问题 $a[l, mid]$，右子问题 $a[mid + 1, r]$ 其最大子段和有几种情况：
`$$
ans = 
\begin{cases}
ansl \\
ansr \\
rpre + lsuf
\end{cases}
$$`
- 需要维护最大子段和ans，最大前缀pre，最大后缀suf

---

## 快速幂
快速计算 $a^n$
- 可以用分治理解，也可以用二进制理解
- 二进制方法即是将 $n$ 转换成二进制数 $(x_{m - 1}x_{m - 2}\dots x_0)_2, x_i \in [0,1]$

`$$
a ^ n = a ^ {(x_{m - 1}x_{m - 2}\dots x_0)_2} = a^{\sum\limits_{i=0}^{m-1}x_i2^{i}} = \prod\limits_{i=0}^{m-1}a^{x_i2^i} = \prod\limits_{i=0}^{m-1}\left(a^{2^i}\right)^{x_i}
$$`

- 其中 $a^{2^i}$ 可以递推得到：
`$$
a^{2^i} = a^{2^{i - 1}} \times a^{2^{i - 1}}
$$`

@@

## 快速幂实例1：【模板】快速幂

>给你三个整数 $a,b,p$，求 $a^b \bmod p$。

- 模板题
```cpp
ll quick(ll a, ll b, ll p) {
    ll res = 1
    while (b != 0) {
        if (b & 1) res = res * a % p
        a = a * a % p
        b >>= 1;
    }
    return res
}
```

@@

## 快速幂实例2：斐波那契数列
> 大家都知道，斐波那契数列是满足如下性质的一个数列：
>
> `$$F_n = \left\{\begin{aligned} 1 \space (n \le 2) \\ F_{n-1}+F_{n-2} \space (n\ge 3) \end{aligned}\right.$$`
>
> 请你求出 $F_n \bmod 10^9 + 7$ 的值。

- 斐波那契数列是一个线性求和式，可以写成矩阵乘形式 
$$
\begin{bmatrix}
f_{n}\\
f_{n-1}\\
\end{bmatrix} = 
\begin{bmatrix}
1&1\\
1&0\\
\end{bmatrix}
\begin{bmatrix}
f_{n - 1}\\
f_{n-2}\\
\end{bmatrix} =
\begin{bmatrix}
1&1\\
1&0\\
\end{bmatrix} ^ {n - 2}
\begin{bmatrix}
f_{2}\\
f_{1}\\
\end{bmatrix}
$$
- 计算 $\begin{bmatrix}
1&1\\
1&0\\
\end{bmatrix} ^ {n - 2}$ 即可

---
## 什么是动态规划？

- 通过将原问题分解为相对简单的子问题来解决复杂问题
- 最优子结构，无后效性，子问题重叠
- 状态设计和状态转移

@@

## 例：数字三角形
> 给定一个 $r$ 行的数字三角形（$r \leq 1000$），需要找到一条从最高点到底部任意处结束的路径，使路径经过数字的和最大。每一步可以走到当前点左下方的点或右下方的点。
> ```
>        7 
>      3   8 
>    8   1   0 
>  2   7   4   4 
>4   5   2   6   5 
> ```
- 暴力方法 $O(2 ^ r)$
- 状态设计：令 $dp_{i, j}$ 表示走到第 $i$ 行第 $j$ 列的最小代价
- 转移方程：$dp[i][j] = min(dp[i - 1][j - 1], dp[i - 1][j]) + a[i][j]$
- $O(r ^ 2)$ 
@@

## 状态设计

- 状态设计即定义 $dp$ 的意义；一般可以从问题出发。如例题中，$dp_{i,j}$表示走到第 $i$ 行第 $j$ 个点的最小代价
- 状态设计还应当考虑初值的设置，例题中对应就是设置 $dp_{1,1}$ 的值
- 从一个暴力的状态开始，逐渐优化设计
- 从数据范围考虑，对数据范围敏感

@@

## 状态转移

- 顾名思义，就是考虑该状态如何转移过来
- 思考我们定义的状态应该如何求得，如例题中，第 $i$ 行 $j$ 列的位置只能由 $i - 1$ 行 $j$ 列和第 $i - 1$ 行 $j - 1$ 列走到
- 状态转移的设计依赖于状态的定义

@@

---

## 线性dp
- 线性DP是动态规划问题中的一类问题，指状态之间有线性关系的动态规划问题。
- 即存在一个线性的递推式，如上述例题中的数字三角形
- 定义过于抽象可以不用深究

@@ 

## 线性dp实例：最长上升子序列
> 这是一个简单的动规板子题。
>
> 给出一个由 $n(n\le 5000)$ 个不超过 $10^6$ 的正整数组成的序列。请输出这个序列的**最长上升子序列**的长度。
>
> 最长上升子序列是指，从原序列中**按顺序**取出一些数字排在一起，这些数字是**逐渐增大**的。

- 状态设计：从问题出发，要求的是最长上升子序列，可以定义 $dp_i$ 表示以 $a[i]$ 结尾的最长上升子序列，初值 $dp[1]$ 可以设为1
- 状态转移：要求 $dp_i$，很自然想到从前面选一个比它小的即可。$dp_i = \max\limits _{j = 1}^{i-1}(dp[j] + 1)(a[j] < a[i])$
```cpp
for (int i = 1; i <= n; i ++) {
    dp[i] = 1;
    for (int j = 1; j < i; j ++)
        if (a[j] < a[i]) dp[i] = max(dp[i], dp[j] + 1);  
}
```
@@

---

## 背包问题一：01背包
> 有 $n(n \leq 1000)$ 个物品和一个容量为 $W(W \leq 1000)$ 的背包，每个物品有重量 $w_{i}(w_{i} \leq W)$ 和价值 $v_{i}(v_{i} \leq 1e9)$ 两种属性，要求选若干物品放入背包使背包中物品的总价值最大且背包中物品的总重量不超过背包的容量。
- 暴力枚举，每个物品选或者不选，$O(2 ^ n)$ 
- 状态设计(要求什么？最大价值！)
- $dp_{i, j}$ 表示前 $i$ 个物品中选了重量为 $j$ 的物品最大价值，要求问题答案即 $dp[n][W]$
- 状态转移
- $dp[i][j] = max(dp[i - 1][j] + dp[i - 1][j - w[i]] + v[i])$

```cpp
for (int i = 1; i <= n; i ++) {
    for (int j = 0; j <= W; j ++) {
        dp[i][j] = dp[i - 1][j];
        if (j - w[i] >= 0) dp[i][j] = max(dp[i][j], dp[i - 1][j - w[i]] + v[i]);
    }
}
```

@@

---

## 背包问题二：完全背包
> 有 $n(n \leq 1000)$ 种物品和一个容量为 $W(W \leq 1000)$ 的背包，每种物品有重量 $w_{i}(w_{i} \leq W)$ 和价值 $v_{i}(v_{i} \leq 1e9)$ 两种属性，**个数无限**。要求选若干物品放入背包使背包中物品的总价值最大且背包中物品的总重量不超过背包的容量
- 状态设计与01背包一致($dp_{i, j})，难点在于如何转移
- 状态转移方程如何书写？
- 朴素方法：$dp[i][j] = \max\limits_{k=0}^{\infty}(dp[i - 1][j - k\times w[i]] + k \times v[i])$
- $dp[i][j] = max(dp[i - 1][j], dp[i][j - w[i]] + v[i])$

```cpp
for (int i = 1; i <= n; i ++) {
    for (int j = 0; j <= W; j ++) {
        dp[i][j] = dp[i - 1][j];
        if (j - w[i] >= 0) dp[i][j] = max(dp[i][j], dp[i][j - w[i]] + v[i]);
    }
}
```

@@

## 背包问题三：多重背包
> 有 $n(n \leq 1000)$ 种物品和一个容量为 $W(W \leq 1000)$ 的背包，每种物品有重量 $w_{i}(w_{i} \leq W)$ ，价值 $v_{i}(v_{i} \leq 1e9)$ 和**个数** $k_{i}(k_{i} \leq 1e9)$三种属性。要求选若干物品放入背包使背包中物品的总价值最大且背包中物品的总重量不超过背包的容量
- 本质上可以当做01背包，将每个物品拆开看成一种，共 $K(K = \sum\limits_{i = 1}^{n}k_i)$ 种。 
- 时间复杂度 $O(KW)$
- 二进制优化：将每种物品拆分成 $\log k_i$ 个物品, 第 $j$ 种物品拆分成的第 $i$ 个物品的重量为 $2^{i - 1}w_j$，价值为 $2^{i - 1}v_j$。时间复杂度 $O(nW\log k)$
- 拆分时多出来的部分怎么办？直接用即可。如6拆分成1，2，3；8拆分成1，2，4，1
- 原理：拆分出来的物品组合可以表示任意个数该种类物品
```cpp
vector<int> newW;
vector<int> newV;
for (int i = 1; i <= n; i ++) {
    for (int j = 1; j <= k[i]; j *= 2) {
        k[i] -= j;
        newW.push_back(j * w[i]);
        newV.push_back(j * v[i]);
    }
    if (k[i]) {
        newW.push_back(k[i] * w[i]);
        newV.push_back(k[i] * v[i]);
    }
}
```

@@

## 背包例题一：质数和分解
>任何大于 $1$ 的自然数 $n$ 都可以写成若干个大于等于 $2$ 且小于等于 $n$ 的质数之和表达式(包括只有一个数构成的和表达式的情况)，并且可能有不止一种质数和的形式。例如，$9$ 的质数和表达式就有四种本质不同的形式：
>
>$9 = 2 + 5 + 2 = 2 + 3 + 2 + 2 = 3 + 3 + 3 = 2 + 7$ 。
>
> 这里所谓两个本质相同的表达式是指可以通过交换其中一个表达式中参加和运算的各个数的位置而直接得到另一个表达式。
>
> 试编程求解自然数 $n(2 \leq n \leq 200)$ 可以写成多少种本质不同的质数和表达式。

- 可以发现 $n$ 相当于背包容量，质数个数相当于物品种类，质数的值相当于物品的重量
- 每个物品有无穷多种，所以相当于是一个完全背包问题
- 设状态 $dp_{i,j}$ 表示前 $i$ 种质数中和 $j$ 的本质不同质数和表达式数量
- 状态转移 $dp_{i, j} = dp_{i - 1, j} + dp_{i, j - w[i]}$
- tips: 多组数据最好提前预处理出所有质数，以免被卡

@@

---

## 区间dp
- 区间dp是线性dp的拓展。顾名思义，区间dp的划分一般以一段区间的待求解值作为状态
- $dp_{i, j}$ 一般表示为从 $i$ 到 $j$ 内的带求解值
- 多适用于可以合并的问题。
- 状态转移一般会枚举合并点，将问题分为两个部分，通过合并两个部分的最优值得到原问题的最优值
- 状态转移方程多是以下形式 $dp_{i, j} = \max\limits _{k = i} ^ {j - 1}f(dp_{i, k}, dp_{k + 1, j})$

@@ 

## 区间dp实例1：石子合并（弱化版）
> 设有 $N(N \le 300)$ 堆石子排成一排，其编号为 $1,2,3,\cdots,N$。每堆石子有一定的质量 $m_i\ (m_i \le 1000)$。现在要将这 $N$ 堆石子合并成为一堆。每次只能合并相邻的两堆，合并的代价为这两堆石子的质量之和，合并后与这两堆石子相邻的石子将和新堆相邻。合并时由于选择的顺序不同，合并的总代价也不相同。试找出一种合理的方法，使总的代价最小，并输出最小代价。

- 区间dp模板题
- 考虑 $dp_{i, j}$ 表示合并区间 $i$ 到 $j$ 的最小代价
- 合并时只需枚举最后合并的两堆是以哪个中心点划分即可
- 状态转移方程 $dp_{i, j} = \min\limits _{k = i}^{j - 1}(dp_{i, k} + dp_{k + 1, j} + w_{i, k} + w_{k + 1, j})$
- 使用前缀和可以快速算出 $w_{i, k}$ 和 $w_{k + 1, j}$
- 时间复杂度 $O(n^3)$
