# 图论概念与存储结构

@@

## 图论

- 源自 Euler 对[七桥问题](https://en.wikipedia.org/wiki/Seven_Bridges_of_K%C3%B6nigsberg)的研究
- 图是由若干给定的顶点及连接两顶点的边所构成的图形，用来描述某些事物之间的某种特定关系
- 顶点用于代表事物，连接两顶点的边则用于表示两个事物间具有这种关系

---

# 图论的基础概念

@@

## 图的形式化定义
- 二元组 $G=\langle V,E\rangle$，其中 $V,E$ 是集合
    - 点集 $V$ 中的元素称为顶点/节点/点
    - 对于无向图，边集 $E$ 中的元素是无序二元组 $e=(u,v)$，称为无向边，$u,v\in V$ 称为 $e$ 的端点
    - 对于有向图，边集 $E$ 中的元素是有序二元组 $e=(u,v)$，称为有向边，$u\in V$ 称为 $e$ 的起点，$v\in V$ 称为 $e$ 的终点
- 不难理解为什么图能反映事物间的关系
- 简单图：没有自环和重边的图
    - 自环：$e=(u,u)\in E$
    - 重边：$e_1=e_2\in E$

若无歧义，$n=|V|,m=|E|$

@@

## 邻接
对于无向图
- 若 $v$ 是 $e$ 的一个端点，则称 $v$ 和 $e$ 是关联的；对于 $u,v\in V$，若存在 $e$ 与它们均关联，则称 $u,v$ 是相邻的
    - 点 $u$ 的邻域 $N(u)$：所有与 $u$ 相邻的点的集合
    - 点 $u$ 的度数 $d(u)$：与点 $u$ 关联的边的条数，特别地，边 $(u,u)$ 要对 $d(u)$ 产生 $2$ 的贡献（Well-Defined）

对于有向图
- 点 $u$ 的出度 $d^+(u)$：以 $u$ 为起点的边的条数
- 点 $u$ 的入度 $d^-(u)$：以 $u$ 为终点的边的条数

@@

## 邻接

对于无向简单图，有 $d(u)=|N(u)|$

对于任意无向图有
$$
\sum_{u\in V}d(u)=2|E|
$$

对于任意有向图有
$$
\sum_{u\in V}d^+(u)=\sum_{u\in V}d^-(u)=|E|
$$

@@

## 路
- 途径（walk）：一个点和边的交错序列，其中首尾是点：
    $$
    v_0,e_1,v_2,e_2,v_2,\cdots,e_k,v_k
    $$
    其中 $e_i$ 的两个端点为 $v_{i-1}$ 和 $v_i$，有时简写为 $v_0\to v_1\to\cdots\to v_k$
- 迹（trail）：满足边亮亮不同的途径
- 路径（path）/简单路径（simple paht）：除了 $v_0$ 和 $v_k$ 以外的点两两不同的迹
- 回路：满足 $v_0=v_k$ 的迹
- 环/圈（cycle）/简单回路（simple circuit）：满足 $v_0=v_k$ 的简单路径

@@

## 连通性
- 连通：对于 $u,v\in V$，若存在途径 $w$ 使得 $v_0=u,v_k=v$，则称 $u,v$ 是连通的
- 连通性：若 $G=\langle V,E\rangle$ 满足 $\forall u,v\in V$ 均连通，则称 $G$ 是连通图，具有连通性

对于有向图
- 连通
    - 若 $u$ 可达 $v$ 且 $v$ 可达 $u$，则称 $u,v$ 是强连通的
    - 若 $u$ 可达 $v$ 或 $v$ 可达 $u$，则称 $u,v$ 是弱连通的
- 连通性
    - 若 $\forall u,v$ 均强连通，则称 $G$ 是强连通图
    - 若 $\forall u,v$ 均弱连通，则称 $G$ 是弱连通图

@@

## 子图，补图与反图
- 子图：对于图 $G=\langle V,E\rangle, H=\langle V',E'\rangle$，若 $V'\subseteq V, E'\subseteq E$，则称 $H$ 是 $G$ 的子图，记作 $H\subseteq G$
    - 导出子图/诱导子图：$\forall u,v\in V', (u,v)\in E\implies (u,v)\in E'$，记作 $G[V']$
    - 闭合子图：对于有向图 $G$，若 $H=G[V^*]$ 满足 $\forall v\in V^*, (v,u)\in E\implies u\in V^*$
- 补图：无向简单图 $G=\langle V,E\rangle$ 的补图 $\bar G\langle V',E'\rangle$ 满足 $V=V'$ 且 $\forall e\in E\Longleftrightarrow e\notin E'$
- 反图：有向图 $G\langle V,E\rangle$ 的反图 $G'\langle V',E'\rangle$ 满足 $V=V'$ 且 $\forall (u,v)\in E\Longleftrightarrow (v,u)\in E'$

@@

## 特殊图
- 完全图：$\forall u,v\in V,u\neq v$ 有 $(u,v)\in E$
- 链：所有边恰好构成一条简单路径
- 环/圈图：所有边恰好构成一个圈
- 星/菊花图：存在 $v$ 与任意 $u\in V,u\neq v$ 连边，且除 $v$ 以外任意两点间无边
- 树：没有环的无向联通简单图
    - 非空的 $n$ 个点的树恰好有 $n-1$ 条边，这既是无环图的边数上界，也是连通图的边数下界
- 森林：若干不相交树的并
    - 无向简单图是森林 $\Longleftrightarrow$ 无环
- 基环树：$n$ 个点 $n$ 条边的连通图
- 基环树森林：$n$ 个点 $n$ 条边的图
- $k$ 分图：可以将点集划分为 $k$ 个部分，不同部分之间没有边相连

@@

## 特殊点/边集
- 团：$V'\subseteq V$ 使得 $\forall u,v\in V'$ 都相邻
- （点）独立集：$V'\subseteq V$ 使得 $\forall u,v\in V'$ 都不相邻
- 点覆盖：$V'\subseteq V$ 使得 $\forall (u,v)\in E$ 都有 $u\in V'\lor v\in V'$
- 匹配（边独立集）：$E'\subseteq E$ 使得 $\forall e_1,e_2\in E',e_1\neq e_2$ 无公共点
    - 边数最多的匹配称为最大匹配；若边带权，权值和最大的匹配称为最大权匹配
    - 完美匹配：$\forall u\in V$ 都是匹配的
- 边覆盖：$E'\subseteq E$ 使得 $\forall u\in V$ 都存在 $e\in E'$ 与 $u$ 关联

@@

## 特殊点/边集

对于图 $G$
- 任意一个团都是 $\overline G$ 的一个独立集，任意一个独立集都是 $\overline G$ 的团
- 任意一个点覆盖的补集都是 $G$ 的一个独立集，任意一个独立集的补集都是 $G$ 的点覆盖

判定一张图是否存在大小为 $k$ 的团/独立集/点覆盖/匹配/边覆盖都是 NPC 的

功能性版本：最大团/最大独立集/最小点覆盖/最大匹配/最小边覆盖

@@

## 树
指定一个节点作为这棵树的根，那么可以根据每个节点到根的距离对节点分层
- 节点的深度/高度：到根的距离，记作 $dep_v$
- 树高/树深：深度最大的节点的深度

对于树上的一条边 $e=(u,v)$，不妨设 $dep_u&lt;dep_v$
- $u$ 称为 $v$ 的父亲，$v$ 称为 $u$ 的儿子，记作 $fa_v=u,v\in son_u$
- $v$ 的祖先：从根到 $u$ 的所有节点都是 $v$ 的祖先
- $u$ 的子孙：所有祖先为 $u$ 的节点
- $u$ 的子树：$u$ 和 $u$ 的子孙
- 叶子：没有儿子的节点

树也是二分图
@@

## 树
- $k$ 叉树：所有节点的儿子个数不超过 $k$

特别地，当 $k=2$ 时有

- 二叉树：所有节点的儿子个数不超过 $2$
    - 一个节点有不超过两个儿子，分别称为左儿子和右儿子，类似地有左子树和右子树
    - 完全二叉树：除最后一层外每一层都是满的
    - 满二叉树：每一层都是满的
        深度为 $k$ 的满二叉树第 $i$ 层有 $2^{i-1}$ 个节点，一共有 $2^k-1$ 个节点

---

# 图的存储结构

@@

## 邻接矩阵

注意：有 $E\subseteq V^2$

这也是为什么图能用来抽象事物间的关系，并且维护许多信息（DSU，2-SAT，差分约束）

存储 $V^2$ 可以使用邻接矩阵，即一个 $n\times n$ 的矩阵 $G$
- 若 $G_{i,j}=1$ 则表示点 $i$ 向点 $j$ 连边
- 对于无向图，有 $G_{i,j}=G_{j,i}, G_{i,i}=1$
- 对于有权图，令 $G_{i,j}$ 表示边 $(i,j)$ 的边权

对于稀疏图（$n=10^5,m=5\times 10^5$，那么 $m\ll n^2$）造成空间和时间上的浪费

@@

## 邻接表

只关注邻接矩阵中有意义的位置：

- 对于节点 $i$ 存储所有 $i$ 连向的节点

我们需要某个数据结构能够支持：每个节点存储的信息不一样多，可能达到 $O(n)$，但是总大小不超过 $O(m)$

使用 STL 内置的变长数组 `vector`

@@

## 邻接表

设点数上界为 $N$

- 声明（全局）：`vector<int> G[N];`
- 加边 $(u,v)$：`G[u].push_back(v)`
    - 无向图需要双向建边
- 遍历 $u$ 的出边：
    ```cpp
    for(int i = 0; i < G[u].size(); ++i) {
        int v = G[u][i]; // u -> v
    }
    ```

@@

## 邻接表

若有边权，则存储的内容从 $v$ 变为 $(v,w)$：
```cpp
vector<pair<int, int>> G[N]; // 声明
G[u].push_back({v, w}) // 加边
for (int i = 0; i < G[u].size(); ++i) { // 遍历
    int v = G[u][i].first, w = G[u][i].second;
}
```


对于现代 C++
```cpp
G[u].emplace_back(v); // C++11
for (int v : G[u]) { // C++11
    //...
}
G[u].emplace_back(v, w); // C++11
for (auto[v, w] : G[u]) { // C++17
    //...
}
```

@@

## 链式前向星

本质与邻接表相同，不过数据结构部分使用链表实现

- 声明：`int head[N], nex[M*2], to[M*2], _w[M*2], cnt;`
- 加边：
    ```cpp
    void add(int u, int v, int w) {
        ++cnt;
        nex[cnt] = head[u]
        to[cnt] = v;
        _w[cnt] = w;
        head[u] = cnt;
    }
    ```
- 遍历
    ```cpp
    for (int i = head[u]; i; i = nex[i]) {
        int v = to[i], w = _w[i];
    }
    ```

@@

## 三种存储方式的比较

- 邻接矩阵
    - 优点：直观，方便查询两点间边的信息
    - 缺点：时空复杂度大，不便遍历
- 邻接表：
    - 优点：遍历复杂度低（$O(d(u))$）
    - 缺点：无法直接查询两点间信息（用哈希表/map 也可以做到）
- 链式前向星
    - 优点：直观展现边的信息（欧拉回路/网络流常用）
    - 缺点：代码稍长

@@

## 图的遍历

把 DFS/BFS 放在具体的图上即可

- 为了保证复杂度，记录每个节点是否访问过（`vis[]`）

```cpp
void dfs(int u) {
    if (vis[u]) return ;
    vis[u] = 1;
    for (int v : G[u]) {
        dfs(v);
    }
}
```

@@

## 图的遍历

```cpp
auto bfs = [](int s) { // C++11
    queue<int> q;
    q.emplace(s); // C++11, C++98 为 push
    vis[s] = 1;
    d[s] = 0;
    while (q.size()) {
        int u = q.front();
        q.pop();
        for (int v : G[u]) {
            if (vis[v]) continue;
            vis[v] = 1;
            d[v] = d[u] + 1;
            q.emplace(v);
        }
    }
};
```

@@

## 树的遍历

