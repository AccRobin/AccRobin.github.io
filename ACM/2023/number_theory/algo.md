# 数论基础算法

---

## 素数筛

### $O(\sqrt{n})$ 判断素数

根据定义遍历 $d \in [2, n - 1]$ 判断 $d$ 是否为 $n$ 的因数.

- 注意到如果 $d \mid m$, 那么 $\frac{n}{d} \mid m$, 即因数 $d$ 与 $\frac{n}{d}$ 成对出现.
- 那么只需要在 $d \in [2, \lfloor\sqrt{n}\rfloor]$ 判断 $d$ 是否为 $n$ 的因数即可.

时间复杂度从 $O(n)$ 降到 $O(\sqrt{n})$.

@@

```cpp
bool check (int n) {
    if (n == 1) return false;
    for (int i = 2; i * i < n; i++) {
        if (n % i == 0) return false;
    }
    return true;
}
```

@@

什么是素数筛? 理解 "筛".

@@

求 $[2, n]$ 内所有素数?

暴力做法 $O(n \sqrt{n})$. 如果要求 $[2, 10^5]$ 以内的素数呢?

### 埃氏筛

![image-20240125094550144](https://s2.loli.net/2024/01/25/bajnxD7BVQe3WG9.png)

对所有数从小到大遍历, 对于每一个新遍历到的数, 将它的所有倍数 (除去它本身) 删除, 最后留下的为所求.

@@

Wikipedia 上的 GIF.

![Animation_Sieve_of_Eratosth](https://s2.loli.net/2024/01/25/U5v1eMpWXo9ikm7.gif)

@@

```cpp
std::vector<int> prime, is_prime(n + 1, 1);
is_prime[0] = is_prime[1] = 0;
for (int i = 2; i <= n; i++) {
    if (!is_prime[i]) continue;
    prime.push_back(i);
    if (1ll * i * i > n) break;
    for (int j = i * i; j <= n; j += i) {
        is_prime[j] = 0;
    }
}
```

时间复杂度 $n \log \log n$.

@@

思考一个问题: 一个数被删了几次?

例如 $60$, $2, 3, 5$ 各删一次.

改进? 一个数只被删一次?

@@

### 欧拉筛

用每个数的最小的素因子把它删掉.

```cpp
std::vector<int> prime, is_prime(n + 1, 1);
is_prime[0] = is_prime[1] = 0;
for (int i = 2; i <= n; i++) {
    if (!is_prime[i]) prime.push_back(i);
    for (int p : prime) {
        if (i * pri_j > n) break;
        is_prime[i * pri_j] = false;
        if (i % pri_j == 0) break;
    }
}
```

时间复杂度 $O(n)$.

---

## 素因数分解

思想和优化完全类似判断素数.

```cpp
for (int i = 2; 1ll * i * i < n; i++) {
    if (n % i) continue;
    int cnt = 0;
    while (n % i == 0) {
        n /= i;
        cnt++;
    }
    fact.emplace_back(i, cnt);
}
if (n != 1) fact.emplace_back(n, 1);
```

时间复杂度 $O(\sqrt{n})$.

@@

或者借助素数筛稍微优化一点点.

```cpp
for (auto i : prime) {
    if (1ll * i * i > n) break;
    if (n % i) continue;
    int cnt = 0;
    while (n % i == 0) {
        n /= i;
        cnt++;
    }
    fact.emplace_back(i, cnt);
}
if (n != 1) fact.emplace_back(n, 1);
```

---

## 逆元

### 费马小定理

**Theorem** $p$ 为素数, $\forall a$, 均有 $a^p \equiv a \bmod p$.

进一步, 如果 $\gcd(a, p) = 1$ (即 $a$ 不为 $p$ 的倍数) 则 $a^{-1}$ 在 $\operatorname{mod} p$ 的意义下存在, 在上式两边同时乘以 $a^{-2} = (a^{-1})^2$ 有
$$
a^{p-2} \equiv a^{-1} \bmod p.
$$
计算逆元等价转换为计算 $a^{p-2}$.

@@

### 快速幂

举个例子.
$$
a^{13} = a^{(1101)_2} = a^8 \times a^4 \times a.
$$
计算出 $a^1, a^2, a^4, a^8 \cdots$, 并同时在其中挑选需要的凑成 $a^n$.

```cpp
int quickPower (int a, int n, int mod) {
    int ans = 1;
    while (n) {
        if (n & 1) ans = 1ll * ans * a % mod;
        a = 1ll * a * a % mod;
        n >>= 1;
    }
    return ans;
}
```

时间复杂度 $O(\log n)$.

@@

计算逆元的方式:

1. $p$ 为素数, 调用快速幂计算 $a^{p-2} \bmod p$ 即可.
2. 不论 $p$ 是否为素数, 调用 exGCD 计算线性组合中 $a$ 的系数即可.

@@

如果要求多个数的逆元怎么办?

快于 $O(n \log n)$?

假设现在要求 $i \in [2, p - 1]$ 的逆元.

根据带余除法有 $p = k \times i + j$, 其中 $k = \lfloor \frac{p}{i} \rfloor, j = p \bmod i < i$.

@@
$$
\begin{aligned}
k \times i + j &= p \\
k \times i &\equiv -j \bmod p \\
i &\equiv k^{-1} \times (-j) \bmod p \\
i^{-1} &\equiv k \times (-j)^{-1} \bmod p \\
&\equiv -\left\lfloor \frac{p}{i} \right\rfloor \times (p \bmod i) ^{-1} \bmod p. \\
\end{aligned}
$$
@@

初始条件为 $1^{-1} \equiv 1 \bmod p$, 递推即可.

```cpp
inv[1] = 1;
for (int i = 2; i <= n; i++) {
    inv[i] = 1ll * (p - p / i) * inv[p % i] % p;
}
```

---

## 中国剩余定理

一些古人的智慧:

1. 有物不知其数，三三数之剩二，五五数之剩三，七七数之剩二。问物几何？
2. 三人同行七十希，五树梅花廿一支，七子团圆正半月，除百零五便得知。

数学语言:

解同余方程组
$$
\begin{cases}
x \equiv a_1 \bmod m_1, \\
x \equiv a_2 \bmod m_2, \\
\cdots \\
x \equiv a_n \bmod m_n.
\end{cases}
$$
灵魂三问: 何时有解? 几个解? 怎么解?

@@

一个例子: 除以 $3$ 余 $2$, 除以 $5$ 余 $3$, 除以 $7$ 余 $2$.

很容易猜出一个解为 $23$. 还有没有更多解?

$23 + (3 \times 5 \times 7) t, t \in \mathbb{Z}$?

@@

### 一个简单情形: $m_i$ 为互不相同的素数

构造一个解形如
$$
A = \sum_{i=1}^{n} A_i \times M_i,
$$
满足
$$
a_i \times M_i \equiv a_i \bmod m_i \text{ and } a_i \times M_i \equiv 0 \bmod m_j (i \neq j).
$$
取
$$
M_i = m_1 m_2 \cdots m_{i-1} m_i^{-1} m_{i+1} \cdots m_n,
$$
其中 $m_i^{-1}$ 为 $m_i$ 在模  $m_1 m_2 \cdots m_{i-1}m_{i+1} \cdots m_n$ 意义下的乘法逆元.

符合要求.

@@

令 $M = \prod_{i=1}^{n} a_i \bmod p, M_i = \frac{M}{m_i}$, 构造的结果为
$$
A = \sum_{i=1}^{n} a_i \times \operatorname{inv}(m_i, M_i) \bmod M.
$$
Attention!

- $M_i$ 需要精确值 (要因为要求在 $M_i$) 意义下的逆元.
- 最后的结果只在模 $M$ 意义下的, 也就是说有解且有无数多解.

@@

### 一般情形

考虑
$$
\begin{cases}
x \equiv a_1 \bmod m_1, \\
x \equiv a_2 \bmod m_2.
\end{cases}
$$
可以得到
$$
\begin{aligned}
x = p_1 m_1 + a_1 = p_2 m_2 + a_2
\Rightarrow -p_1 m_1 + p_2 m_2 = a_1 - a_2.
\end{aligned}
$$
转换为 exGCD 求解 $p_1, p_2$.

