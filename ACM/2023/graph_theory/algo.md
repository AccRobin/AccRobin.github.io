# 图论基础算法

@@

## review
...

---

# 树上信息维护

@@

## 子树和

> 静态版本：给出一个 $n(n\le 10^5)$ 个点的树，第 $i$ 个点有点权 $a_i$，请输出每个节点子树的点权和

- 在回溯时计入儿子的子树贡献
    ```cpp
    void dfs(int u, int fa) {
        f[u] = a[u];
        for (int v : G[u]) {
            if (v == fa) continue;
            dfs(v, u);
            f[u] += f[v];
        }
    }
    ```

- 当 $a_i=1$ 时退化为子树大小和

## 路径和



## 最近公共祖先（LCA）

---

# 最小生成树

@@

## 最小生成树

- 生成树：图 $G\langle V,E\rangle$ 的一个子图 $T\langle V',E'\rangle$ 满足 $V=V'$
- 最小生成树：边权和最小的生成树

@@

## Prim 算法

## Kruskal 算法

## Boruvka 算法

---

# 单源最短路

## Floyd 算法

## Bellman-Ford 算法

## Dijkstra 算法

---

# 拓扑排序