### BFS
```cpp
#include<iostream>
#include<cstdio>
#include<queue>

using namespace std;

const int MAXn = 1e5 + 10;
int n, d;

struct edge{
    int next;
    int v;
}e[MAXn << 1];

struct node{
    int de;
    int v;
};

int head[MAXn]; 
bool vised[MAXn];

void bfs(){
    int cnt = -1;
    queue<node> q;
    q.push({0, 1});
    
    
    while(!q.empty()){
        node cur = q.front();
        q.pop();
        vised[cur.v] = true;
        
        if(cur.de > d) break;
        cnt ++;
        
        for(int i = head[cur.v]; i != 0; i = e[i].next){
            if(!vised[e[i].v] ){
                q.push({cur.de + 1, e[i].v});
            }
        
        }
        
    }
    cout << cnt << endl;

}

int main(){
    cin >> n >> d;
    int k = 1;
    for(int i = 1; i <= n - 1; i ++){
        int u, v;
        scanf("%d%d", &u, &v);
        
        e[k].v = v;
        e[k].next = head[u];
        head[u] = k++;
        
        e[k].v = u;
        e[k].next = head[v];
        head[v] = k++;
        
    }
    
    bfs();
    
    return 0;
    
    
    
}
```


### DFS两次找直径
```cpp
#include<iostream>
#include<cstdio>

using namespace std;

const int N = 1e5 + 10;

struct edge{
    int v;
    int next;
}e[N << 1];

int id = 1;
int h[N];
bool ed[N];

int _max = -N;
int det = 0;

void dfs(int v, int dis){
    if(dis > _max){
        _max = dis;
        det = v;
    }
    ed[v] = true;
    
    for(int i = h[v]; i; i = e[i].next ){ /////////////
        if(!ed[e[i].v]){
            dfs(e[i].v, dis + 1);
        }
    }

    ed[v] = 0;// 还原ed
}

void add_e(int u, int v){
    e[id].next = h[u];
    e[id].v = v;
    h[u] = id ++;
};




int main(){
    
    int n;
    cin >> n;
    int t1, t2;
    
    for(int i = 1; i < n; i ++){
        scanf("%d%d",&t1, &t2);
        add_e(t1, t2);
        add_e(t2, t1);
    }
    dfs(1, 0);
    dfs(det, 0);
     
    cout << _max << endl;
    
    return 0;
}
```

### 单调队列
```cpp
#include <iostream>
#include <deque>

using namespace std;

deque<int> dq;
int a[10];

int n, k;
void monotonic_que(){
    for(int i = 1; i < k; i ++){
        while(!dq.empty() && a[i] <= a[dq.back()]) dq.pop_back();
        dq.push_back(i);
    }

    for(int i = k; i <= n; i ++){
        while(!dq.empty() && a[i] <= a[dq.back()]) dq.pop_back();
        dq.push_back(i);

        while(dq.front() <= i - k) dq.pop_front();

        cout << a[dq.front()] << endl;

    }

}
int main(){

    cin >> n >> k;
    for(int i = 1; i <= n; i ++) cin >> a[i];
//---------------------------------------
    {
    int head = 1, tail = 0;
    int q[10];
    for(int i = 1; i < k; i ++) {
        while(head <= tail && a[i] <= a[q[tail]]) tail --;
        q[++tail] = i;
    }
    for(int i = k; i <= n; i ++) {
        while(head <= tail && a[i] <= a[q[tail]]) tail --;
        q[++tail] = i;

        while(q[head] <= i - k) head ++;
        
        cout << a[q[head]] << endl;
    }
    }


    return 0;


}

```

### 树网的核
```cpp
#include<iostream>
#include<cstdio>
#include <vector>
#include <iostream>
#include <deque>
using namespace std;

const int N = 400;

int n, s;

struct edge{
    int v;
    int w;
    int next;
}e[N << 1];

int h[N];
bool ed[N];
int q[N];

vector<int> tt;//前缀和数组 用于快速计算直径上两点之间的路径长度

int ec = 1;
void add_e(int u, int v, int w){
    e[ec].next = h[u];
    e[ec].v = v;
    e[ec].w = w;
    h[u] = ec ++;
}

int max_dis = -N;
int dest = 0;
void dfs(int v, int dis){

    if(dis > max_dis){
        max_dis = dis;
        dest = v;
    }

    for(int i = h[v]; i; i = e[i].next){
        if(!ed[e[i].v]){
            ed[e[i].v] = 1;
            dfs(e[i].v, dis + e[i].w);
            ed[e[i].v] = 0;
        }
    }
}


int R[N];
int len;
int r;


void path(int u, int v, vector<int> tmp, int l, vector<int> tr){//需要距离
    if(u == v){
        for(int i = 0; i < tmp.size(); i ++)
            R[i] = tmp[i];
        for(int i = 0; i < tr.size(); i++) // 这里tt 改成 tr     
            tt.push_back(tr[i]); //这里的tt[i]报错了 段错误  改成push_back()
        len = tmp.size();
        r = l;
        return ;
    }

    for(int i = h[u]; i; i = e[i].next){
        if(!ed[e[i].v]){
            ed[e[i].v] = 1;
            tmp.push_back(e[i].v);
            tr.push_back(e[i].w);

            path(e[i].v, v, tmp, l + e[i].w, tr);
            
            tmp.pop_back();
            tr.pop_back();
            ed[e[i].v] = 0;
        }
    }
}
int main(){
    cin >> n >> s;
    int t1, t2, t3;

    for(int i = 1; i < n; i ++){
        scanf("%d%d%d",&t1, &t2, &t3);
        add_e(t1, t2, t3);
        add_e(t2, t1, t3);
    }
//////
    // cout << " ------------- " << endl;
    // for(int i = 1; i <= n; i ++ ){
    //     cout << i << " " << ed[i] << endl;
    // }

    ed[1] = 1;
    dfs(1, 0);/// ed[1] = 1??
    ed[1] = 0;
    int det1 = dest;

    max_dis = - N;

    ed[det1] = 1;
    dfs(det1, 0);
    ed[det1] = 0;

    int det2 = dest;

    vector<int> tmp;
    tmp.push_back(det1);

    vector<int> tm;

    // cout << det1 << "  " << det2 << " " << endl << "----------" << endl;

    ed[det1] = 1;
    path(det1, det2, tmp, 0, tm);
    ed[det1] = 0;

    // for(int i = 0; i < len; i ++){
    //     cout << R[i] << " " << endl;
    // }
    // cout << "---------------------" << endl;

    for(int i = 0; i < len; i ++) ed[R[i]] = 1;

    int contr1[len], contr2[len];


    int ldis = 0, rdis = r;


    for(int i = 0; i < len; i ++){
        max_dis = 0;
        dfs(R[i], 0);
        contr1[i] = max_dis;

        if(i == 0 || i == len - 1)
            contr2[i] = 0;             //贡献2不能这么算
        else
            contr2[i] = max(ldis, rdis);///////////// max/min
    
        // ldis += e[R[i]].w;//这里有问题
        // rdis -= e[R[i]].w;
        if(i != len - 1)  {    
            ldis += tt[i];
            rdis -= tt[i];
        }
    }

////
    // for(int i = 0; i < tt.size(); i ++) cout << tt[i] << " " ;
    // cout << endl;


    int pre[N];
    pre[0] = 0;
    for(int i = 1; i <= tt.size(); i++){
        pre[i] = pre[i - 1] + tt[i - 1];
        //cout << pre[i] << endl;
    }

    // for(int i = 0; i < len; i ++){
    //     cout << R[i] << "  con1: " << contr1[i] << "  con2: " << contr2[i] << endl;
    // }
    // cout << " ---------------  " << endl;

    //计算完贡献  用单调队列

    int head = 0, tail = -1;
    int id = 0, cur = 0;
    //int q[N];                               //这里在主函数内初始化  是随机数！！！

    int rh = 0, rt =-1;

    int tot = 0;
    int ans = 99999999;

    // deque<int> q;

    int dist1 = 0, dist2 = r;

    // for(int i = 0, j = 0; i < len; dist2 -= tt[i], ++i){
    //     while(!q.empty() && pre[i] - pre[q.front()] > s)
    //         q.pop_front();
        
    //     while(j < i && r - dist1 - dist2 > s)
    //         dist1 += tt[j ++];//这个就是去算另一个端点的贡献、、、、
        
    //     while(!q.empty() && contr1[q.back()] < contr1[i])
    //         q.pop_back();
        
    //     q.push_back(i);

    //     int temp = max(dist1, dist2);
    //     temp = max(temp, contr1[q.front()]);
    //     ans = min(ans, temp);   

    // }//好像就是 算清楚对应的dist1， dist2 就好  不用管队列中的东西

    for(int i = 0, j = 0; i < len; dist2 -= tt[i], ++i){
        while(head <= tail && pre[i] - pre[q[head]] > s) head ++;//这里注意前缀和，因为求的是之间的数，所以这里对了

        while(j < i && r - dist1 - dist2 > s) dist1 += tt[j ++];
        
        while(head <= tail && contr1[q[tail]] < contr1[i]) tail --;
        q[++ tail] = i;

        int temp = max(dist1, dist2);
        temp = max(temp, contr1[q[head]]);
        ans = min(ans, temp); 


    }

    cout << ans << endl;

    return 0;
}
```

### 会议
```cpp
#include <iostream>
#include <cstdio>

using namespace std;

const int N = 5e4 + 10;

int n, a, b;

struct edge{
    int v;
    int next;
}e[N << 1];
int ec = 1;
int h[N];

void add_e(int u, int v){
    e[ec].v = v;
    e[ec].next = h[u];
    h[u] = ec ++;
}

bool ed[N];
int ans = 0;

void dfs(int u, int dis){
    ans += dis;
    for(int i = h[u]; i; i = e[i].next){
        if(!ed[e[i].v]){
            ed[e[i].v] = 1;
            dfs(e[i].v, dis + 1);
            ed[e[i].v] = 0;
        }
    }
}

int main(){
    cin >> n;
    for(int i = 1; i < n; i ++){
        scanf("%d%d", &a, &b);
        add_e(a, b);
        add_e(b, a);
    }

    int ta = 0x7fffffff;
    int tv;
    for(int i = 1; i <= n; i++){
        ans = 0;
        ed[i] = 1;  // 这里!!!!!
        dfs(i, 0);
        ed[i] = 0;
        cout << ans << endl;

        if(ans < ta){
            ta = ans;
            tv = i;
        }
    }

    cout << tv << " " << ta << endl;

}
//////////////////////////////////////////////////
O(N)
#include <iostream>
#include <cstdio>
#include <vector>

using namespace std;

const int N = 5e4 + 10;

struct edge{
    int v;
    int next;
}e[N << 1];
int ec = 1;

int hd[N];
bool ed[N];
int fa[N];
int sz[N];
vector<int> son[N];
int cost[N];

void add_e(int u, int v){
    e[ec].next = hd[u];
    e[ec].v = v;
    hd[u] = ec ++;
}

int n, a, b;

int dfs(int v)/*设定返回值为以 v 为根的树的节点个数*/{
    int tot = 1;
    for(int i = hd[v]; i; i = e[i].next){
        if(!ed[e[i].v]){
            ed[e[i].v] = 1;

            fa[e[i].v] = v;
            son[v].push_back(e[i].v);
            
            tot += dfs(e[i].v);
            ed[e[i].v] = 0;
        }
    }
    sz[v] = tot;
    return tot;

}
int ans = 0;
void cal(int v, int dis){
    ans += dis;
    for(int i = hd[v]; i; i = e[i].next){
        if(!ed[e[i].v]){
            ed[e[i].v] = 1;
            cal(e[i].v, dis + 1);
            ed[e[i].v] = 0;
        }
    }

}

void calco(int v){
    if(v == 1){
        for(int i = hd[v]; i; i = e[i].next)
        if(!ed[e[i].v]){
            ed[e[i].v] = 1;
            calco(e[i].v);
            ed[e[i].v] = 0;
        }
    }

    else {
    int f = fa[v];
    cost[v] = cost[f] + n - 2 * sz[v]; // //这里是n 不是 cost[f] + sz[f] - 2 * sz[v]
    for(int i = hd[v]; i; i = e[i].next)
        if(!ed[e[i].v]){
            ed[e[i].v] = 1;
            calco(e[i].v);
            ed[e[i].v] = 0;
        }
    }   
}


int main(){
    scanf("%d", &n);

    for(int i = 1; i < n; i ++){
        scanf("%d%d", &a, &b);
        add_e(a, b);
        add_e(b, a);
    }

    ed[1] = 1;
    dfs(1);
    
    cal(1, 0);
    
    cost[1] = ans;

    calco(1);

    int cen = 0;

    for(int i = 1; i <= n; i ++){
        if(cost[i] < ans){
            ans = cost[i];
            cen = i;
        }
    }

    cout << cen << " " << ans << endl;

    return 0;
}



```


### LCA
```cpp
/*
Ma junfei
240902  1:05

ERROR
1. int 范围 2^31 - 1
2. LCA 到最后一步的判定，应为看其父亲是否一致

 */
#include <iostream>
#include <cstdio>
#include <vector>
//#include <algorithm>

using namespace std;

const int N = 5e6 + 10;

struct edge{
    int v;
    int next;
}e[N << 1];

int hd[N];
int ec = 1;
bool ed[N];
int fa[N];
int dep[N];
int go[N][32];
vector<int> dfsord;


void get_dep(int v, int h){
    dep[v] = h;

    for(int i = hd[v]; i; i = e[i].next){
        if(!ed[e[i].v]){
            ed[e[i].v] = 1;
            get_dep(e[i].v, h + 1);
            ed[e[i].v] = 0;
        }
    }
}


void dfs(int v){
    for(int i = hd[v]; i; i = e[i].next){
        if(!ed[e[i].v]){
            dfsord.push_back(e[i].v);
            ed[e[i].v] = 1;
            fa[e[i].v] = v;
            dfs(e[i].v);
            ed[e[i].v] = 0;
        }
    }
}

void add_e(int u, int v){
    e[ec].v = v;
    e[ec].next = hd[u];
    hd[u] = ec ++;
}

int n, m, s;
int x, y, a, b;

int LCA(int u, int v){
    if(u == v) return v; // 这一行对于倍增法LCA有必要吗

    if(dep[v] > dep[u]) swap(u, v);
    int gap = dep[u] - dep[v];

    while(gap){
        //int step = 1;
        for(int i = 30; i >= 0 && gap; i--) // 2^31 - 1
        {
            if(1 << i > gap) continue;
            else{
                u = go[u][i];
                gap -= 1 << i;
            }
        }

    }
    if(u == v) return v;

    while(u != v){
        for(int i = 30; i >= 0; i --){
            if(!go[u][i]) continue;
            else{
                if(fa[u] == fa[v]) return fa[u]; //!!!!!!!!!!!!!!
                if(go[u][i] == go[v][i]) continue;
                else{
                    u = go[u][i];
                    v = go[v][i];
                    //break;
                }
            }
        }
    }

    return u;
}

int main(){
    cin >> n >> m >> s;

    for(int i = 1; i < n; i ++){
        scanf("%d%d", &x, &y);
        add_e(x, y);
        add_e(y, x);
    }  

    dfsord.push_back(s);
    ed[s] = 1;

    dfs(s);
    get_dep(s, 0);

    ed[s] = 0;


    for(int i = 1; i <= n; i ++)
        go[i][0] = fa[i];

    for(int i = 0; i < dfsord.size(); i ++){
        int cnt = 1;
        while(1){
            int x = dfsord[i];
            if((1 << cnt) <= dep[x]){
                go[x][cnt] = go[go[x][cnt - 1]][cnt - 1];
                cnt ++;
            }
            else break;
        }
    }
    // cout << "=======================" << endl;
    // for(int i = 1; i <= n; i ++){
    //     cout << "----- " << i;
    //     for(int j = 0; j <= 31; j ++) //!!! j = 31 2 ^ 32 < 0 && 数组越界
    //         if(go[i][j]) cout << "i = " << i << ", j = " << j << "  "<<go[i][j] << " ";
    //     cout << endl;
    // }
    // cout << "=======================" << endl;
    for(int i = 1; i <= m; i ++)
        scanf("%d%d", &a, &b), cout << LCA(a, b) << endl;

    return 0;
}

```

### 差分
```cpp
/*
Ma junfei
240902 20:40

ERROR
1.MLE  可能是因为路径过长
2.正解 差分
*/
#include <iostream>
#include <cstdio>
#include <vector>

using namespace std;

const int N = 5e4 + 10;

struct edge{
    int v;
    int next;
}e[N << 1];
int ec = 1;
int hd[N];
bool ed[N];
void add_e(int u, int v){
    e[ec].v = v;
    e[ec].next = hd[u];
    hd[u] = ec ++;
}

int n, k;
int x, y, s, t;

int ans[N];

void dfs(int u, int v, vector<int> tmp){
    if(u == v){
        for(int i = 0; i < tmp.size(); i ++) ans[tmp[i]]++;
        //ans[v] ++;
        return;
    }

    for(int i = hd[u]; i; i = e[i].next){
        if(!ed[e[i].v]){
            ed[e[i].v] = 1;
            tmp.push_back(e[i].v);
            dfs(e[i].v, v, tmp);
            tmp.pop_back();
            ed[e[i].v] = 0; 
        }
    }
}
int main(){
    cin >> n >> k;

    for(int i = 1; i < n; i ++)
        scanf("%d%d", &x, &y), add_e(x, y), add_e(y, x);

    
    for(int i = 1; i <= k; i ++){
        scanf("%d%d", &s, &t);
        vector<int> tmp; // 这里int 改为short 有结果不一样 short到3万多
        ed[s] = 1, ans[s] ++;
        dfs(s, t, tmp);
        ed[s] = 0;
    }

    int answer = - N;
    for(int i = 1; i <= n; i ++) if(ans[i] > answer) answer = ans[i];

    cout << answer << endl;


}


//-------------------------------------------------
/*
Ma junfei
240902 20:40

ERROR
1.MLE  可能是因为路径过长
2.正解 差分
3.输入的数据还是另一道题的数据
*/
#include <iostream>
#include <cstdio>
#include <vector>

using namespace std;

const int N = 5e4 + 10;

struct edge{
    int v;
    int next;
}e[N << 1];
int ec = 1;
int hd[N];
bool ed[N];
void add_e(int u, int v){
    e[ec].v = v;
    e[ec].next = hd[u];
    hd[u] = ec ++;
}

int n, k;
int x, y, s, t;
int fa[N];
int go[N][31];
int ans[N];

int de[N];
vector<int> dfn;

void dfs(int v, int dep){
    dfn.push_back(v);
    de[v] = dep;

    for(int i = hd[v]; i; i = e[i].next){
        if(!ed[e[i].v]){
            ed[e[i].v] = 1;
            fa[e[i].v] = v;
            dfs(e[i].v, dep + 1);
            ed[e[i].v] = 0;
        }
    }
}

int LCA(int u, int v){
    if(de[v] < de[u]) swap(u, v);
    int gap = de[v] - de[u];

    int st = 30;
    while(gap){
        if(1 << st <= gap){
            v = go[v][st];
            gap -= 1 << st;
            //st--;
        }
        st--;////////////////  ?????????????? nt if里的和外面的 多减了
    }
    if(u == v) return u;

    for(int i = 30; i >= 0; i --){
        if(go[u][i]){
            if(go[u][i] != go[v][i]) u = go[u][i], v = go[v][i];
            //else if(fa[u] == fa[v]) return fa[u];
        }
    }
    return fa[u];
}

int get(int v){
    int sum = ans[v];

    for(int i = hd[v]; i ; i = e[i].next){
        if(!ed[e[i].v]){
            ed[e[i].v] = 1; sum += get(e[i].v); ed[e[i].v] = 0;
        }
    }

    ans[v] = sum; //......wo zhen de wu yu l  //其他的没更新 相当于就算了ans[0];
    return sum;
}


int main(){
    cin >> n >> k;

    for(int i = 1; i < n; i ++)
        scanf("%d%d", &x, &y), add_e(x, y), add_e(y, x);

    dfn.push_back(0);

    ed[1] = 1;
    dfs(1, 0);
    ed[1] = 0;

    for(int i = 1; i <= n; i ++) go[i][0] = fa[i];

    for(int i = 1; i <= n; i ++){
        int cur = dfn[i]; //输入错了 sb
        int st = 1;
        while(1 << st <= de[cur]){
            go[cur][st] = go[go[cur][st - 1]][st - 1];// st  bushi i 
            st ++;
        }
    }

    for(int i = 1; i <= k; i ++){
        scanf("%d%d", &s, &t);
        int lca = LCA(s, t);
        ans[s] ++;
        ans[t] ++;
        ans[lca] --, ans[fa[lca]] --;
    }


    ed[1] = 1, ans[1] = get(1), ed[1] = 0;

    int mx = - N;

    for(int i = 1; i <= n; i ++)
        mx = max(mx, ans[i]);

    cout << mx << endl;


    return 0;

}
```