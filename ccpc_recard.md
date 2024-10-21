### A 军训 I
- 分类讨论 + 构造
*****
### B 军训 II
- 猜测: 当序列有序时不整齐度最低
*****
### C 种树
- 存在 `DP` 做法和贪心做法
  - `树形 DP`
  ![O4nZcq.png](https://ooo.0x0.ooo/2024/09/17/O4nZcq.png)

  - 贪心
  ![O4nyMc.png](https://ooo.0x0.ooo/2024/09/17/O4nyMc.png)

```cpp
#include <bits/stdc++.h>
using i64 = long long;
#define int i64
#define pb push_back
#define ep emplace
#define eb emplace_back
using std::max, std::min, std::swap;
using std::cin, std::cout, std::string, std::vector;
int read(int x = 0, int f = 0, char ch = getchar()) {
    while (ch < 48 or 57 < ch) f = ch == 45, ch = getchar();
    while(48 <= ch and ch <= 57) x = x * 10 + ch - 48, ch = getchar();
    return f ? -x : x;
}

void solve() {
    int n = read(), m = read();

    vector<int> vis(n + 1);
    for (int i = 1; i <= m; i++) {
        vis[read()] = 1;
    }

    vector<vector<int>> g(n + 1);
    for (int i = 1; i < n; i++) {
        int u = read(), v = read();
        g[u].pb(v), g[v].pb(u);
    }

    int ans = 0;
    vector<int> siz(n + 1);

    std::function<void(int, int)> dfs = [&](int u, int fa) {
        for (auto v : g[u]) {
            if (v == fa) continue;
            dfs(v, u);
            siz[u] += siz[v];
        }

        siz[u] += !vis[u]; // 计算未被种树的子树大小

        if (vis[u]) {
            ans += (siz[u] + 1) / 2;
            if (siz[u] & 1) vis[fa] = 1;  // 如果是奇数 存在
            siz[u] = 0;
        }
    };

    for (int i = 1; i <= n; i++) {
        if (vis[i]) {
            dfs(i, 0);
            break;                 // 找到一个即可
        }
    }

    cout << ans << '\n';
}

signed main() {
    for (int T = read(); T--; solve());
    // solve();
    return 0;
}
```
*****
### D 编码-解码器
- 存在 转换成矩阵角度计算，和区间`DP`方法
  - 区间DP
  ![O4iEDx.png](https://ooo.0x0.ooo/2024/09/16/O4iEDx.png)

  - 矩阵？？

*****
### E 随机过程
- 主要是数学思路（$26^i$说明考虑的是全部可能情况，在这样的情况下去算）
  ![O4ng7r.png](https://ooo.0x0.ooo/2024/09/17/O4ng7r.png)
  解释：对于于第 i 层某个节点，其不出现的概率，第i层可能的节点数量为$26^i$，所以某一个节点出现的概率为 $(\frac{1}{26})^i$，则在第i层不出现的概率即为$(1 - (\frac{1}{26})^i)^n$，每一个string的插入是一次这样的过程（选择的过程，即在这个string插入的过程中，节点是选择出来的，概率就是上式），共有n个string，所以是n次方，再乘上这一层的节点个数，即为期望个数
*****
### F 包子鸡蛋 III
- 多项式整理 + dp
*****
### G 疯狂星期六
- 最大流
![O4nHZ6.png](https://ooo.0x0.ooo/2024/09/17/O4nHZ6.png)
```cpp
#include <bits/stdc++.h>
using i64 = long long;
#define int i64
#define pb push_back
#define ep emplace
#define eb emplace_back
using std::max, std::min, std::swap;
using std::cin, std::cout, std::cerr, std::string, std::vector;
int read(int x = 0, int f = 0, char ch = getchar()) {
    while (ch < 48 or 57 < ch) f = ch == 45, ch = getchar();
    while(48 <= ch and ch <= 57) x = x * 10 + ch - 48, ch = getchar();
    return f ? -x : x;
}

constexpr int inf = 1E9;
template<class T>
struct MaxFlow {
    struct _Edge {
        int to;
        T cap;
        _Edge(int to, T cap) : to(to), cap(cap) {}
    };
    
    int n;
    std::vector<_Edge> e;
    std::vector<std::vector<int>> g;
    std::vector<int> cur, h;
    
    MaxFlow() {}
    MaxFlow(int n) {
        init(n);
    }
    
    void init(int n) {
        this->n = n;
        e.clear();
        g.assign(n, {});
        cur.resize(n);
        h.resize(n);
    }
    
    bool bfs(int s, int t) {
        h.assign(n, -1);
        std::queue<int> que;
        h[s] = 0;
        que.push(s);
        while (!que.empty()) {
            const int u = que.front();
            que.pop();
            for (int i : g[u]) {
                auto [v, c] = e[i];
                if (c > 0 && h[v] == -1) {
                    h[v] = h[u] + 1;
                    if (v == t) {
                        return true;
                    }
                    que.push(v);
                }
            }
        }
        return false;
    }
    
    T dfs(int u, int t, T f) {
        if (u == t) {
            return f;
        }
        auto r = f;
        for (int &i = cur[u]; i < int(g[u].size()); ++i) {
            const int j = g[u][i];
            auto [v, c] = e[j];
            if (c > 0 && h[v] == h[u] + 1) {
                auto a = dfs(v, t, std::min(r, c));
                e[j].cap -= a;
                e[j ^ 1].cap += a;
                r -= a;
                if (r == 0) {
                    return f;
                }
            }
        }
        return f - r;
    }
    void addEdge(int u, int v, T c) {
        g[u].push_back(e.size());
        e.emplace_back(v, c);
        g[v].push_back(e.size());
        e.emplace_back(u, 0);
    }
    T flow(int s, int t) {
        T ans = 0;
        while (bfs(s, t)) {
            cur.assign(n, 0);
            ans += dfs(s, t, std::numeric_limits<T>::max());
        }
        return ans;
    }
    
    std::vector<bool> minCut() {
        std::vector<bool> c(n);
        for (int i = 0; i < n; i++) {
            c[i] = (h[i] != -1);
        }
        return c;
    }
    
    struct Edge {
        int from;
        int to;
        T cap;
        T flow;
    };
    std::vector<Edge> edges() {
        std::vector<Edge> a;
        for (int i = 0; i < e.size(); i += 2) {
            Edge x;
            x.from = e[i + 1].to;
            x.to = e[i].to;
            x.cap = e[i].cap + e[i + 1].cap;
            x.flow = e[i + 1].cap;
            a.push_back(x);
        }
        return a;
    }
};

void solve() {
    int n = read(), m = read();

    int s = n + m + 1, t = s + 1;
    MaxFlow<int> g(t + 1);

    vector<int> a(n + 1), v(n + 1);
    for (int i = 1; i <= n; i++) {
        a[i] = read();
        v[i] = read();
    }

    int c = 0;
    int tot = 0;
    for (int i = 1; i <= m; i++) {
        int x = read(), y = read(), w = read();

        g.addEdge(s, n + i, w);
        g.addEdge(n + i, x, w);
        g.addEdge(n + i, y, w);

        tot += w;
        if (x == 1 or y == 1) c += w;
    }

    c = min(c + v[1], a[1]);
    bool flag = 1;
    g.addEdge(1, t, c - v[1]);
    for (int i = 2; i <= n; i++) {
        a[i] = min(a[i], c - 1);
        if (a[i] < v[i]) {
            flag = 0;
            break;
        }

        g.addEdge(i, t, a[i] - v[i]);
    }

    if (!flag or g.flow(s, t) != tot) {
        cout << "NO\n";
    } else {
        cout << "YES\n";
    }
}

signed main() {
    // for (int T = read(); T--; solve());
    solve();
    return 0;
}
```
*****
### H 另一个游戏
- 数学问题转化成凸包计算，甚至是使用平衡树 + 二分 维护......
*****
### I 找行李
![O4nLcP.png](https://ooo.0x0.ooo/2024/09/17/O4nLcP.png)
??? 什么是不同情况？？
*****
### J 找最小
- 线性基

![O4nGOb.png](https://ooo.0x0.ooo/2024/09/17/O4nGOb.png)
```cpp
#include <bits/stdc++.h>
using i64 = long long;
#define int i64
#define pb push_back
#define ep emplace
#define eb emplace_back
using std::max, std::min, std::swap;
using std::cin, std::cout, std::string, std::vector;
int read(int x = 0, int f = 0, char ch = getchar()) {
    while (ch < 48 or 57 < ch) f = ch == 45, ch = getchar();
    while(48 <= ch and ch <= 57) x = x * 10 + ch - 48, ch = getchar();
    return f ? -x : x;
}

struct Basis {
    int num_zero;
    std::vector<int> b;
    Basis() {}
    Basis(int n) { b.resize(n); std::fill(b.begin(), b.end(), 0); }
    bool add(int x) {
        for (int i = 62; i >= 0; i--) {
            if (x >> i & 1) {
                if (b[i]) {
                    x ^= b[i];
                } else {
                    b[i] = x;
                    return true;
                }
            }
        }
        num_zero++;
        return false;
    }
};

void solve() {
    int n = read();
    Basis bas(63);

    int fa = 0, fb = 0;
    vector<int> a(n), b(n);
    for (auto &x : a) x = read(), fa ^= x;
    for (auto &x : b) x = read(), fb ^= x;

    for (int i = 0; i < n; i++) bas.add(a[i] ^ b[i]);

    // cout << fa << " " << fb << '\n';

    for (int i = 62; i >= 0; i--) {
        if (max(fa ^ bas.b[i], fb ^ bas.b[i]) < max(fa, fb)) {
            fa ^= bas.b[i];
            fb ^= bas.b[i];
        }
    }

    cout << max(fa, fb) << "\n";
}

signed main() {
    for (int T = read(); T--; solve());
    // solve();
    return 0;
}
```
*****
### K 取沙子游戏
- 博弈论
![O4nQ7l.png](https://ooo.0x0.ooo/2024/09/17/O4nQ7l.png)
*****
### L 网络预选赛
- 签到 暴力