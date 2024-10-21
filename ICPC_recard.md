### A
![O4nSLg.png](https://ooo.0x0.ooo/2024/09/17/O4nSLg.png)
```cpp
#include <bits/stdc++.h>
#define int long long
#define pb push_back
#define pii pair<int, int>
using namespace std;
bool debug = 1;
#define dbg(x)                                                   \
    if (debug)                                                   \
        cerr << BRIGHT_CYAN << #x << COLOR_RESET << " = " << (x) \
             << NORMAL_FAINT << COLOR_RESET << endl;
const string COLOR_RESET = "\033[0m", BRIGHT_CYAN = "\033[1;36m",
             NORMAL_FAINT = "\033[0;2m";

void solve() {
    int a[32];
    int de = 0;
    for (int i = 0; i < 32; i++) {
        cin >> a[i];
        if (a[i] <= a[0])
            de++;
    }
    if (de == 32) {
        cout << 1;
    } else if (de >= 28) {
        cout << 2;
    } else if (de >= 14) {
        cout << 4;
    } else if (de >= 7) {
        cout << 8;
    } else if (de >= 3) {
        cout << 16;
    } else
        cout << 32;

    cout << endl;
}

signed main() {
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    int t = 1;
    cin >> t;
    while (t--)
        solve();
    return 0;
}

//__builtin_popcountll()
// cout<<fixed<<setprecision(2);

```

### C
![O4n0uB.png](https://ooo.0x0.ooo/2024/09/17/O4n0uB.png)
```cpp
#include<bits/stdc++.h>
#define int long long 
#define pb push_back
#define pii pair<int,int>
using namespace std;
bool debug = 1;
#define dbg(x) if(debug) cerr<<BRIGHT_CYAN<<#x<<COLOR_RESET<<" = "<<(x)<<NORMAL_FAINT<<COLOR_RESET<<endl;
const string COLOR_RESET = "\033[0m", BRIGHT_CYAN = "\033[1;36m", NORMAL_FAINT = "\033[0;2m";


void solve(){
    int n;
    cin >> n;
    int sum = 0;
    vector<int> a[n + 1]{};
    vector<int> vis(n+1);
    for (int i = 1; i <= n;i++)
    {
        int u, v;
        cin >> u >> v;
        u--;
        a[u].pb(v);
        a[v].pb(u);
    }
    function<void(int)> dfs = [&](int u) {
        if(vis[u]==0)
            sum++, vis[u] = 1;
        for(auto p:a[u]){
            if(vis[p]==0)
                dfs(p);
        }
    };
    dfs(0);
    if(sum==n+1)
        cout << 1;
    else
        cout << 0;
    cout << '\n';
}


signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    int t = 1;
    cin>>t;
    while(t--)
        solve();
    return 0;
}

//__builtin_popcountll()
// cout<<fixed<<setprecision(2);

```

### F
![O4ndzs.png](https://ooo.0x0.ooo/2024/09/17/O4ndzs.png)
```cpp
#include<bits/stdc++.h>
#define int long long 
#define pb push_back
#define pii pair<int,int>
using namespace std;
bool debug = 1;
#define dbg(x) if(debug) cerr<<BRIGHT_CYAN<<#x<<COLOR_RESET<<" = "<<(x)<<NORMAL_FAINT<<COLOR_RESET<<endl;
const string COLOR_RESET = "\033[0m", BRIGHT_CYAN = "\033[1;36m", NORMAL_FAINT = "\033[0;2m";

const int N = 2e5 + 999;
int n, a[N], stk[N], top, l[N], r[N];
void solve(){
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    stk[top = 0] = 0;
    for (int i = 1; i <= n; i++){
        while(top && a[i] > a[stk[top]])
            top--;
        l[i] = stk[top] + 1;
        stk[++top] = i;
    }
    stk[top = 0] = n + 1;
    a[n + 1] = 0;
    for (int i = n; i >= 1; i--){
        while(top && a[i] > a[stk[top]])
            top--;
        r[i] = stk[top] - 1;
        if(a[r[i] + 1] == a[i])
            r[i] = i;
        stk[++top] = i;
    }
    int ans = 0;
    for (int i = 1; i <= n; i++)
        ans += r[i] - l[i];
    cout << ans << '\n';
}
signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    int t = 1;
    cin>>t;
    while(t--)
        solve();
    return 0;
}

//__builtin_popcountll()
// cout<<fixed<<setprecision(2);
```
### G
![O4nsPK.png](https://ooo.0x0.ooo/2024/09/17/O4nsPK.png)
```cpp
#include<bits/stdc++.h>
#define int long long 
#define pb push_back
#define pii pair<int,int>
using namespace std;
bool debug = 1;
#define dbg(x) if(debug) cerr<<BRIGHT_CYAN<<#x<<COLOR_RESET<<" = "<<(x)<<NORMAL_FAINT<<COLOR_RESET<<endl;
const string COLOR_RESET = "\033[0m", BRIGHT_CYAN = "\033[1;36m", NORMAL_FAINT = "\033[0;2m";

const int N = 2033;

int n, a[N], rk[N], s[N], b[N][N], c[N][N], sb[N][N];

bool check(int x){
    for (int i = 1; i <= n; i++){
        s[i] = s[i - 1] + (a[i] > x);
    }
    int cnt = 0;
    for (int i = 1; i <= n; i++)
        for (int j = i; j <= n; j++)
            b[i][j] = !(j - i + 1 - s[j] + s[i - 1] >= (j - i + 2) / 2);
    /*for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++)
            cout << b[i][j] << " \n"[j == n];*/
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++)
            sb[i][j] = sb[i - 1][j] + sb[i][j - 1] + b[i][j] - sb[i - 1][j - 1];
    for (int i = 1; i <= n; i++)
        for (int j = i; j <= n; j++){
            c[i][j] = (sb[j][j] - sb[i - 1][j] - sb[j][i - 1] + sb[i - 1][i - 1]);
            int sz = (j - i + 2) * (j - i + 1) / 2;
            if(sz - c[i][j] >= (sz + 1) / 2)
                cnt++;
        }
    return cnt >= (n * (n + 1) / 2 + 1) / 2;
}
void solve(){
    cin >> n;
    for (int i = 1; i <= n; i++)
        cin >> a[i], rk[i] = a[i];
    sort(rk + 1, rk + n + 1);
    int l = 1, r = n, mid;
    while(l < r){
        mid = (l + r) >> 1;
        if(check(rk[mid]))
            r = mid;
        else
            l = mid + 1;
    }
    cout << rk[l];
}


signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    int t = 1;
    while(t--)
        solve();
    return 0;
}

//__builtin_popcountll()
// cout<<fixed<<setprecision(2);
```

### H
![O4nV9a.png](https://ooo.0x0.ooo/2024/09/17/O4nV9a.png)
```cpp
#include<bits/stdc++.h>
#define int long long 
#define pb push_back
#define pii pair<int,int>
using namespace std;
bool debug = 1;
#define dbg(x) if(debug) cerr<<BRIGHT_CYAN<<#x<<COLOR_RESET<<" = "<<(x)<<NORMAL_FAINT<<COLOR_RESET<<endl;
const string COLOR_RESET = "\033[0m", BRIGHT_CYAN = "\033[1;36m", NORMAL_FAINT = "\033[0;2m";
const int N = 1009;
int n, m, dist[N], head[N], flow[N], vis[N], S, T, tot, pre[N];
struct Edge{
    int v, rest, p, nxt;
} edge[N * 40];
typedef long long ll;
int col[N], cnt[N], val[N];
bool SPFA() {
    for (int i = 1; i <= n + m + 10; i++)
        dist[i] = flow[i] = 1e16, vis[i] = 0;
    queue<int> q;
    q.push(S), dist[S] = 0;
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        vis[u] = 0;
        for (int i = head[u]; i; i = edge[i].nxt) {
            if (edge[i].rest && dist[edge[i].v] > dist[u] + edge[i].p) {
                dist[edge[i].v] = dist[u] + edge[i].p;
                pre[edge[i].v] = i;
                flow[edge[i].v] = min(flow[u], edge[i].rest);
                if (!vis[edge[i].v])
                    q.push(edge[i].v), vis[edge[i].v] = 1;
            }
        }
    }
    return dist[T] < 1e15;
}
void maxflow() {
    ll ans = 0, f = 0;
    //cout << "maxflow" << endl;
    while (SPFA()) {
        //cout << "SPFA" << endl;
        //cout << flow[T] << " " << dist[T] << endl;
        f += flow[T];
        ans += flow[T] * dist[T];
        int x = T;
        while (x != S) {
            int y = pre[x];
            edge[y].rest -= flow[T];
            edge[y ^ 1].rest += flow[T];
            x = edge[y ^ 1].v;
        }
    }
    if(f < n / 2)
        cout << "-1\n";
    else {
        ans = -ans;
        for (int i = 1; i <= n; i++)
            ans += val[i];
        cout << ans << '\n';
    }    
}
void add(int u, int v, int c, int w, int t = 1){
    edge[++tot] = (Edge){v, c, w, head[u]};
    head[u] = tot;
    if(t)
        add(v, u, 0, -w, 0);
}
void solve(){
    cin >> n >> m;
    for(int i = 1; i <= m; i++)
        cin >> cnt[i], cnt[i] = -cnt[i];
    n *= 2;
    for (int i = 1; i <= n; i++){
        cin >> col[i];
        cnt[col[i]]++;
    }
    for (int i = 1; i <= n; i++)
        cin >> val[i];
    //cout << "finish" << endl;
    memset(head, 0, sizeof head);
    S = 1, T = 2;
    tot = 1;
    add(S, n + 2, n / 2, 0);
    for (int i = n; i > 1; i--)
        add(i + 2, i + 1, (i - 1) / 2, 0), add(i + 2, n + 2 + col[i], 1, val[i]);
    add(1 + 2, n + 2 + col[1], 1, val[1]);
    bool flag = 1;
    for (int i = 1; i <= m; i++){
        add(n + 2 + i, T, cnt[i], 0);
        if(cnt[i] < 0)
            flag = 0;
    }
    if(flag) maxflow();
    else
        cout << "-1\n";
}


signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    int t = 1;
    cin>>t;
    while(t--)
        solve();
    return 0;
}

//__builtin_popcountll()
// cout<<fixed<<setprecision(2);
```
### M
![O4nfTS.png](https://ooo.0x0.ooo/2024/09/17/O4nfTS.png)
```cpp
#include<bits/stdc++.h>
#define int long long 
#define pb push_back
#define pii pair<int,int>
using namespace std;
bool debug = 1;
#define dbg(x) if(debug) cerr<<BRIGHT_CYAN<<#x<<COLOR_RESET<<" = "<<(x)<<NORMAL_FAINT<<COLOR_RESET<<endl;
const string COLOR_RESET = "\033[0m", BRIGHT_CYAN = "\033[1;36m", NORMAL_FAINT = "\033[0;2m";


void solve(){
    int n; cin >> n;
    //teamA A accepted
    map<string, set<string>> mp;
    for (int i = 1; i <= n; i++) {
        string a, b, c;
        cin >> a >> b >> c;
        if (c[0] == 'a') {
            mp[b].insert(a);
        }
    }
    int mx = 0;
    string ans;
    for (auto it : mp) {
        if (it.second.size() > mx) {
            mx = it.second.size();
            ans = it.first;
        }
    }
    cout << ans << "\n";
}


signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    int t = 1;
    cin>>t;
    while(t--)
        solve();
    return 0;
}

//__builtin_popcountll()
// cout<<fixed<<setprecision(2);
```
### L
![O4nlvN.png](https://ooo.0x0.ooo/2024/09/17/O4nlvN.png)
