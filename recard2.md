## Trie
### 最长异或路径

#### 题目描述

给定一棵 $n$ 个点的带权树，结点下标从 $1$ 开始到 $n$。寻找树中找两个结点，求最长的异或路径。

异或路径指的是指两个结点之间唯一路径上的所有边权的异或。

#### 输入格式

第一行一个整数 $n$，表示点数。

接下来 $n-1$ 行，给出 $u,v,w$ ，分别表示树上的 $u$ 点和 $v$ 点有连边，边的权值是 $w$。

#### 输出格式

一行，一个整数表示答案。

```cpp
struct trie{

    int nxt[1000][26], cnt = 0;
    bool existed[1000];

    void insert(char* s, int l){
        int p = 0;
        for(int i = 0; i < l; i ++){
            int c = s[i] - 'a';
            if(!nxt[p][c]) nxt[p][c] = ++ cnt;
            p = nxt[p][c];
        }
        existed[p] = 1;
    }       

    bool find(char* s, int l){
        int p = 0;
        for(int i = 0; i < l; i ++){
            int c = s[i] - 'a';
            if(!nxt[p][c]) return 0;
            p = nxt[p][c];
        }
        return existed[p];
    }
};

////   
#include <iostream>
#include <vector>
#include <cstdio>
using namespace std;

const int N = 1e6 + 10;

vector<int> trans(int num){
    vector<int> res;
    while(num > 0){
        res.push_back(num & 1);
        num >>= 1;
    }
    int l = res.size();
    for(int i = l + 1; i <= 31; i ++) res.push_back(0);
    return res;
}
struct trie{
    int nxt[N][2], cnt = 0;
    bool existed[N];
    int pos[N];
    //friend vector<int> trans(int num);
    void insert(int num, int v){
        int p = 0;
        vector<int> s;
        while(num > 0){
            s.push_back(num & 1);
            num >>= 1;
        }
        int l = s.size();
        for(int i = 30; i >= 0; i --){
            int c;
            if(i >= l) c = 0;
            else c = s[i];
            if(!nxt[p][c]) nxt[p][c] = ++ cnt;
            p = nxt[p][c];
        }
        existed[p] = 1;
        pos[p] = v;
    }
    bool find(int num){
        int p = 0;
        vector<int> s;
        while(num > 0){
            s.push_back(num & 1);
            num >>= 1;
        }
        int l = s.size();
        for(int i = l + 1; i <= 31; i ++) s.push_back(0);
        //l = s.size();
        for(int i = 30; i >= 0; i --){
            int c = s[i];
            if(!nxt[p][c]) return 0;
            p = nxt[p][c];
        }
        return existed[p];
    }
    
    int get(vector<int> tmp){
        int res = 0;
        int p = 0;
        for(int i = 30; i >= 0; i --){
            int c1 = tmp[i], c2 = !tmp[i];
            res <<= 1;
            if(nxt[p][c2]) res = res | 1, p = nxt[p][c2];
            else if(nxt[p][c1])res = res | 0, p = nxt[p][c1];
        }
        return res;
    }
};

struct edge{
    int v, next;
    int w;
}e[N << 1];
int hd[N], ec = 1;
int W[N];
bool ed[N];
void add_e(int u, int v, int w){
    e[ec].v = v;
    e[ec].next = hd[u];
    e[ec].w = w;
    hd[u] = ec ++ ; //sb;
}

int n;
int a, b, w;

void dfs(int u, int res){
    W[u] = res;
    for(int i = hd[u]; i; i = e[i].next){
        if(!ed[e[i].v]) ed[e[i].v] = 1, dfs(e[i].v, res ^ e[i].w),
        ed[e[i].v] = 0;
    }
}
int main(){
    scanf("%d", &n);
    for(int i = 1; i < n; i ++)scanf("%d%d%d", &a, &b, &w),
    add_e(a, b, w), add_e(b, a, w);
    
    ed[1] = 1, dfs(1, 0), ed[1] = 0;

    //for(int i = 1; i <= n; i ++) cout << W[i] << endl;
    trie t;
    for(int i = 1; i <= n; i ++) t.insert(W[i], i);
    
    int mx = -0x80000000;
    for(int i = 1; i <= n; i ++) mx = max(mx, t.get(trans(W[i])));
    
    cout << mx << endl;
    return 0;
}
```


```cpp
#include <iostream>
#include <vector>
using namespace std;

const int _ = 526010;
int n;
int V[_];
int debug = 0;


const int MAXH = 21;
int ch[_ * (MAXH + 1)][2], w[_ * (MAXH + 1)], xorv[_ * (MAXH + 1)];
int tot = 0;

int mknode() {//创建新的节点
  ++tot;
  ch[tot][1] = ch[tot][0] = w[tot] = xorv[tot] = 0;
  return tot;
}

void maintain(int o) { // 维护w[o], xorv[o]
  w[o] = xorv[o] = 0;
  if (ch[o][0]) {
    w[o] += w[ch[o][0]];
    xorv[o] ^= xorv[ch[o][0]] << 1; //  左移的原因 : o位为低位  与高位异或 
  }
  if (ch[o][1]) {
    w[o] += w[ch[o][1]];
    xorv[o] ^= (xorv[ch[o][1]] << 1) | (w[ch[o][1]] & 1); // 奇数个1  则加上1
  }
  w[o] = w[o] & 1;  
}

void insert(int &o, int x, int dp) { // 注意 int &o 和 o = mknode()
  if (!o) o = mknode();
  if (dp > MAXH) return (void)(w[o]++);
  insert(ch[o][x & 1], x >> 1, dp + 1);
  maintain(o);
}

int merge(int a, int b) { // 将两个trie 结合在一起， 但仍可能存在一部分还是在原来位置上
  if (!a) return b;       // 是在 a 中一些nxt[][]=0位置上，填上一个数，能够跳转到 b
  if (!b) return a;       // 这样 add1  或者  maintain维护时就可一下子计算出来
  w[a] = w[a] + w[b];
  xorv[a] ^= xorv[b];
  ch[a][0] = merge(ch[a][0], ch[b][0]);
  ch[a][1] = merge(ch[a][1], ch[b][1]);
  return a;
}

void addall(int o) {
  swap(ch[o][0], ch[o][1]);
  if (ch[o][0]) addall(ch[o][0]);
  maintain(o);
}


int rt[_];
long long Ans = 0;
vector<int> E[_];

void dfs0(int o) {
  insert(rt[o], V[o], 0);
  for (int i = 0; i < E[o].size(); i++) {
    int node = E[o][i];
    dfs0(node);
    rt[o] = merge(rt[o], rt[node]);
  }
  addall(rt[o]);
  //insert(rt[o], V[o], 0);
  Ans += xorv[rt[o]];
}
int read(){
	int x=0,f=1; char ch=getchar();
	while(ch<'0' || ch>'9'){if(ch=='-')f=-1;ch=getchar();}
	while(ch>='0' && ch<='9'){x=x*10+ch-'0';ch=getchar();}
	return x*f;
}
int main() {
  n = read();
  for (int i = 1; i <= n; i++) V[i] = read();
  for (int i = 2; i <= n; i++) E[read()].push_back(i);
  dfs0(1);
  printf("%lld", Ans);
  return 0;
}

//////////////
#include <iostream>
#include <cstdio>
using namespace std;

typedef long long ll;
const int N = 1e5; // N要开够
const int MAXH = 21; //最大位数要够
struct edge{int v, next;}e[N << 1];
int hd[N], wei[N], ec = 1, n;
bool ed[N];
int ch[N * MAXH][2], tot = 0, w[N * MAXH], xorv[N * MAXH], rt[N];
int mknode(){
    tot ++;
    ch[tot][1] = ch[tot][0]= w[tot] = xorv[tot] = 0;
    return tot;
}
void maintain(int o){
    w[o] = xorv[o] = 0;
    if(ch[o][0]) w[o] += w[ch[o][0]], xorv[o] ^= xorv[ch[o][0]] << 1; 
    if(ch[o][1]) w[o] += w[ch[o][1]], xorv[o] ^= (xorv[ch[o][1]] << 1) | (w[ch[o][1]] & 1);
    w[o] = w[o] & 1;
}
void insert(int &o, int x, int dep){
    if(!o) o = mknode();
    if(dep > MAXH) return (void)(w[o] ++);
    insert(ch[o][x & 1], x >> 1, dep + 1); // 右移
    maintain(o);
}


int merge(int a, int b){
    if(!a) return b;
    if(!b) return a;
    w[a] = w[a] + w[b], xorv[a] ^= xorv[b];//merge 也有 w, xorv的合并
    ch[a][0] = merge(ch[a][0], ch[b][0]);
    ch[a][1] = merge(ch[a][1], ch[b][1]);
    return a;
}

void add_e(int u, int v){
    e[ec].v = v, e[ec].next = hd[u];
    hd[u] = ec ++;
}

void add1(int o){
    swap(ch[o][1], ch[o][0]);
    if(ch[o][0]) add1(ch[o][0]);
    maintain(o);
}

ll ans = 0;
void dfs(int o){
    for(int i = hd[o]; i; i = e[i].next){
        int v = e[i].v;
        if(!ed[v]){
            ed[v] = 1, dfs(v), ed[v] = 0;
            rt[o] = merge(rt[o], rt[v]);
        }
    }
    add1(rt[o]);
    insert(rt[o], wei[o], 0);
    ans += xorv[rt[o]];
}

int main(){
    scanf("%d", &n);
    for(int i = 1; i <= n; i ++)scanf("%d", &wei[i]);
    for(int i = 2; i <= n; i ++){
        int u;
        scanf("%d", &u), add_e(u, i), add_e(i, u);
    }
    ed[1] = 1, dfs(1), ed[1] = 0; //ed

    cout << ans << endl;
}
```

### Acwing 5001. 异或和之和

```cpp
#include <iostream>
#include <cstdio>
const int N = 1e5 + 10;
typedef long long ll;
using namespace std;

int A[N], n;
ll sum;
int main(){
    cin >> n;
    for(int i = 1; i <= n; i ++) scanf("%d", &A[i]);
    
    for(int i = 0; i <= 20; i ++){
        int ji = 0, cnt = 0, ou = 1;
        for(int j = 1; j <= n; j ++){
            cnt += (A[j] >> i) & 1;
            if(cnt % 2) sum +=(1LL << i) * ou, ji ++;
            else sum += (1LL << i) * ji, ou ++;
        }
    }
    
    printf("%lld", sum);//wcnm sbsbsbsbsbsbsbsb
    return 0;
}
```

## 树状数组与线段树
- 注意常见错误
```cpp
const int N = 1e5 + 10;
int a[N], n;
namespace SegmentTree{
struct Node{
    int l, r;
    int sum;
}tr[N << 2];
void pushup(int u){
    tr[u].sum = tr[u << 1].sum + tr[u << 1 | 1].sum;
}
void build(int u, int l, int r){
    if(l == r) tr[u] = {l, r, a[l]};
    else{
        tr[u] = {l, r};
        int mid = l + r >> 1;
        build(u << 1, l, mid);
        build(u << 1 | 1, mid + 1, r);
        pushup(u);
    }
}

void modify(int u, int p, int v){
    if(tr[u].l == tr[u].r) tr[u].sum += v;
    else{
        int mid = tr[u].l + tr[u].r >> 1;
        if(p <= mid) modify(u << 1, p, v);
        else modify(u << 1 | 1, p, v);
        pushup(u);
    }
}

int query(int u, int l, int r){
    if(tr[u].l >= l && tr[u].r <= r) return tr[u].sum;
    int mid = tr[u].l + tr[u].r >> 1;
    int sum = 0;
    // HERE
    // if(l <= mid) sum = query(u << 1, l, mid);
    // if(r > mid) sum += query(u << 1 | 1, mid + 1, r);
    if(l <= mid) sum = query(u << 1, l, r);
    if(r > mid) sum += query(u << 1 | 1, l, r);
    return sum;
}
}

namespace BinaryIndexedTree{
int c[N];
void add(int p, int x){
    for(int i = x; i <= n; i += -i & i) c[i] += x;
}
int sum(int x){
    int res = 0;
    for(int i = x; i > 0; i -= -i & i) res += c[i];
    return res;
}
}
```

## 区间改线段是 BIT
```cpp
#include <cstdio>
#include <iostream>
typedef long long ll;
using namespace std;
const int N = 1e5 + 10; 
struct node{
    int l, r, lazy;
    ll sum;
}tr[N << 2];
int a[N];
void pushup(int u){
    tr[u].sum = tr[u << 1].sum + tr[u << 1 | 1].sum;
}
void pushdown(int u){
    tr[u << 1].lazy += tr[u].lazy, tr[u << 1 | 1].lazy += tr[u].lazy; // = || +=  是 += 必然是
																	  // 例如 先(4,5)+k1, 然后(1,5)+k2,(4,5)的lazy应为(k1 + k2)，这样才能正确更新
																	  //下面的节点  
    tr[u<<1].sum+=tr[u].lazy*(tr[u<<1].r-tr[u<<1].l+1); // t[u].lazy 不是儿子的  因为lazy的含义是 这个父亲区间欠子区间的值
    tr[u<<1|1].sum+=tr[u].lazy*(tr[u<<1|1].r-tr[u<<1|1].l+1);
    tr[u].lazy = 0;
}
void build(int u, int l, int r){
    if(l == r){tr[u] = {l, r, 0, a[l]}; return;}
    tr[u] = {l, r, 0};
    int mid = l + r >> 1;
    build(u << 1, l, mid), build(u << 1 | 1, mid + 1, r);
    pushup(u);
}
void modify(int u, int l, int r, ll x){
    if(tr[u].l >= l && tr[u].r <= r){
        tr[u].lazy += x;
        tr[u].sum += (tr[u].r - tr[u].l + 1) * x;  // 这里直接用x
        return ;
    }
    pushdown(u);  // 不可省？ 首先结果是 如果省去 结果变少
					// ans : 如果这里不 pushdown ,不更新子节点的
					// val, 最后的pushup又将此节点变回去了。。。  	 
    int mid = tr[u].l + tr[u].r >> 1;
    if(l <= mid) modify(u << 1, l, r, x);
    if(r > mid) modify(u << 1 | 1, l, r, x);
    pushup(u);  // 对 改变的节点的父节点更新维护
}
ll query(int u, int l, int r){
    if(tr[u].l >= l && tr[u].r <= r) return tr[u].sum;
    pushdown(u);    // 这里保证用到的值都是最新的
    int mid = tr[u].l + tr[u].r >> 1;
    ll sum = 0;
    if(l <= mid) sum += query(u << 1, l, r);
    if(r > mid) sum += query(u << 1 | 1, l, r);
	return sum;
}
int n, k;
int main(){
    scanf("%d%d", &n, &k);
    for(int i = 1; i <= n; i ++)scanf("%d", &a[i]);
    build(1, 1, n);
    while(k --){
        int op, a, b, c;
        scanf("%d%d%d", &op, &a, &b);
        if(op == 1){
            scanf("%d", &c);
            modify(1, a, b, c);
        }else{
            cout << query(1, a, b) << endl;
        }
    }
    return 0;
}
```