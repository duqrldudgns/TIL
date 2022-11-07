# Problem Solving

### 

### 시간복잡도

![Untitled](/C++/img/ProblemSolving/Untitled.png)

### Template

```cpp
#include <bits/stdc++.h>

#define endl "\n"
#define all(v) v.begin(), v.end()

using namespace std;

typedef long long ll;
typedef unsigned long long ull;
typedef tuple<int,int,int> tp;
typedef pair<int, int> pii;
typedef pair<ll, ll> pll;
typedef vector<int> vi;
typedef vector <ll> vl;
typedef vector<pii> vpii;
typedef vector<pll> vpll;
typedef unordered_map<int, int> mpii;
```

### BFS

> 하나의 정점에서 다른 정점의 최단거리, 넓게
> 
- 큐

```cpp
void BFS(int x, int y){
    queue<pii> Q;
    Q.push({x,y});
    vis[x][y] = true;

    while(!Q.empty()){
        auto cur = Q.front(); Q.pop();
        for(int i=0; i<4; i++){
            int nx = cur.X + dx[i];
            int ny = cur.Y + dy[i];
            if(nx < 0 || nx >= n || ny < 0 || ny >= n) continue;
            if(board[x][y] != board[nx][ny] || vis[nx][ny]) continue;
            vis[nx][ny] = true;
            Q.push({nx,ny});
        }
    }
}
```

### DFS

> 깊게
> 
- 스택
- 재귀로 구현하는게 편함

```cpp
void DFS(int n){
    visited[n]=true;

    for(int i=0; i<v[n].size(); i++){
        if( visited[v[n][i]] == true ) continue;
        arr[v[n][i]] = n;
        DFS(v[n][i]);
    }
}
```

공통

- 인접행렬 : O(V^2)
- 인접리스트 : O(V+E) 👍
- 모든 곳을 방문

[https://adela.love/posts/dfs-and-bfs](https://adela.love/posts/dfs-and-bfs)

### 백트래킹

> DFS에 가지치기를 통해 안가도 되는 루트는 안가는 방법
> 
- dfs보단 나은 시간복잡도?

```cpp
for(int i=1; i<=n; i++){
    if(vis[i]) continue;
    arr[cnt] = i;
    vis[i] = true;
    DFS(cnt+1);
    vis[i] = false;
}
```

---

- next_permutation 사용하기 👍❗❗
- N이 작으면 백트래킹 해보기

### 이분탐색

> 정답을 구할 수 없지만, X가 가능한지 아닌지 알아내는 것
> 
- 최적의 해를 구하는 문제
    - 가능한 정답의 최솟값, 최댓값
- 무한 루프 주의
    - st = 2, en = 3, mid=2

---

```cpp
int st = 0;
int en = max;
int mid;

while(st < en){
    mid = (st + en + 1)/2;    //st=mid일 때, +1 안해주면 무한루프 위험
    
    ll cnt=0;
    for(int i=0; i<n; i++)
        cnt += arr[i] / mid;
    
    if(cnt >= m){
        st = mid;             // st=2, en=3, mid=2 , st=mid=2 반복
				//st = mid+1;         
    }
    else{
        en = mid-1;
				//en = mid;
    }
}
cout << st; //최종 답 도출되기 전, mid 값이 입력되는 점 주의
//cout << en; //위 주석으로 바뀌는 경우엔 최종 답 도출이 en이 답

```

- STL   binary_search, lower_bound, upper_bound

### 투포인터

> 리스트에 순차적으로 접근해야 할 때 두 개의 점 위치를 기록하며 처리
> 
- O(N)
    
    

### 누적합

![Untitled](/C++/img/ProblemSolving/Untitled1.png)

### 다익스트라

> 하나의 정점에서 모든 정점의 최단 경로, 가중치가 양수 일 때 사용
> 
- 우선순위큐
- O(E logV)
- 행렬은 O(V^2) - 간선이 많을 경우 사용(V^2=E)

```cpp
vpii vtp[200002];
int d[20002];

int main(){
vtp[a].push_back({c,b}); // a->b로가는 가중치 c

fill(d,d+v+1, INT_MAX);  // 최댓값으로 채우기
d[k]=0;                  // 본인은 0

solve();
}

void solve(){
    priority_queue<pii, vpii, greater<pii>> pq;
    pq.push({0,k});

    while(!pq.empty()){
        auto cur = pq.top(); pq.pop();
        int cw = cur.first;
        int cv = cur.second;

        //먼저들어가서 뒤로 밀린 애들도 많으니 제거해주기, 안해도 되지만 시간 단축
        if(d[cv] != cw) continue;

        for(int i=0; i<vtp[cv].size(); i++){
            int nw = vtp[cv][i].first;
            int nv = vtp[cv][i].second;
            if(d[nv] <= cw + nw) continue;   //기존에 계산된 것이 내가 거쳐온 가중치 + 가르키는 가중치보다 작으면 패스
            d[nv] = cw + nw;
            pq.push({d[nv],nv});
        }
    }
}
```

### A*(A star) 알고리즘  -  다익스트라(빈도 낮음)

> 출발점과 도착점이 주어졌을 때 최단경로 구하는 최적의 알고리즘
게임에서 캐릭터 이동 시 벽너머여도 알아서 찾아가게 만드는 알고리즘
> 
- 휴리스틱(h) 값 사용
    - 현재 노드에서 목표 노드까지 가는 최단 경로의 비용을 추정하는 것
- 평가함수를 통해 최적의 경로 탐색
    - f(N) : g(N)과 h(N)을 합한 최단거리
    - g(N) : 초기 노드에서 현재 노드까지의 실제 최단거리
    - h(N) : 현재 노드에서 목표노드로 가는 최단거리 추정치(현재에 있기 때문에 아직 탐색하지 못했지만 추측)
- 여러 f(N) 값을 구해 가장 값이 작은 곳을 따라 탐색하는 작업 반복

![Untitled](/C++/img/ProblemSolving/Untitled2.png)

- [https://ddae9.tistory.com/8](https://ddae9.tistory.com/8)
- [https://hyeooona825.tistory.com/45](https://hyeooona825.tistory.com/45)
- boj 11112, 1525, 1584

```cpp
g//boj 11112
const string TARGET = "12345678#";

string s;
int dx[4] = {0, 0, 1, -1};
int dy[4] = {1, -1, 0, 0};
int ans = -1;

//f = g + h
//f 가능성, 가장 낮은것을 따라감 /  g 현재까지의 값, 움직인 횟수 /  h 앞으로 예상되는 값, 제자리에 있지 않은 퍼즐의 수
struct node{
    string board;// 노드의 모양
    int g;       // g(n)
    int f;       // f(n)
    node(string board, int f, int g){
        this->board = board;
        this->f = f;
        this->g = g;
    }
};

struct cmp{
    bool operator()(node &a, node &b){
        if(a.f==b.f) return a.g > b.g;  // f가 같다면 g가 작은 순
        return a.f > b.f;               // f가 작은 순
    }
};

priority_queue<node, vector<node> ,cmp> pq; // open, 가능성이 가장 높아보이는(f값이 작은) 순으로 계산 진행
unordered_map<string, int> visitMap;        // close, 이미 봤던 모양을 또 가는 중복 방지, value는 이동횟수

int main(){
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr); cout.tie(nullptr);

    int t; cin >> t;
    while(t--){

        //초기화
        visitMap.clear();
        while(!pq.empty()) pq.pop();
        s.clear();

        for(int i=0; i<3; i++){
            string temp;
            cin >> temp;
            s += temp;
        }

        pq.push(node(s,0,0));
        visitMap[s]=0;

        while(!pq.empty()){
            auto cnode = pq.top(); pq.pop();

            // 모두 맞춘 경우 끝냄
            if(visitMap.count(TARGET)) break;

            int zero = cnode.board.find('#');
            int x = zero/3, y = zero%3;         // 0의 위치를 2차원 배열로 표시

            for(int i=0; i<4; i++){
                int nx = x + dx[i];
                int ny = y + dy[i];
                if(nx < 0 || nx >= 3 || ny < 0 || ny >= 3) continue;
                
                string ns = cnode.board;
                swap(ns[x*3+y], ns[nx*3+ny]);   // 0과 상하좌우 위치 변경
                if(visitMap.count(ns)) continue;// 이미 지나온 케이스면 패스

                int f=0,g=0,h=0;
                for(int j=0; j<ns.size(); j++)  // 휴리스틱 값(목표값) 구하기
                    if(ns[j] != TARGET[j]) h++; // 목표 노드가 되기 위해 다른 위치에 있는 숫자 체크
                g = cnode.g;
                f = g + h;

                pq.push(node(ns, f, g+1));      
                visitMap[ns]=g+1;               // 이동횟수 체크
            }
        }
        
        if(visitMap.count(TARGET)){
            cout << visitMap[TARGET] << "\n";
        }
        else{
            cout << "impossible" << "\n";
        }
    }
}
```

### 벨만포드(빈도 낮음)

> 하나의 정점에서 모든 정점의 최단 경로, 가중치가 음수 일 때 사용
> 
- O(VE) ~= O(V^3)   다익보다 느림
- N-1번 반복 함으로써 음의 사이클인지 알 수 있음
    - N번째 반복 했을 때에도 변화가 생긴다면 음의 사이클 확정

```cpp
ll d[501]; //음의사이클 발생 경우, 그 수치가 int형의 범위를 넘어서는 경우도 있기 때문
vector<pair<pii, int>> v;

v.push_back({{A,B},C});

fill(d, d+N+1, INF);
d[1] = 0;

for(int i=1; i<=N; i++){    //벨만포드의 조건 중 N-1번까지 반복을 해봐야 음의 사이클인지 구분 할 수 있음
    for(int j=0; j<v.size(); j++){
        int From = v[j].first.first;
        int To = v[j].first.second;
        int Cost = v[j].second;

        if (d[From] == INF) continue; // INF라면 한 그래프에 같이 존재하지 않음

        if (d[To] > d[From] + Cost){
            d[To] = d[From] + Cost;

            if(i==N){           // N번째 반복 했을때에도 변화가 생긴다면 음의 사이클이 생긴 것
                cout << -1 << endl;
                return;
            }
        }
    }
}
```

### 플로이드와샬(빈도 낮음)

> 모든 정점에서 모든 정점의 최단경로, 거쳐가는 정점을 기준으로 알고리즘 수행 , 가중치 음수도 가능
> 
- 다익스트라로 충분히 모든 정점 구할 수 있어서 음수쓸때아니면 몰라도 될 듯
- O(V^3)
- 사용 전략
    - 간선이 매우 많다(V^2=E): 플로이드 알고리즘이 우수
    - 간선이 적다 : 다익 사용 , 다익스트라 알고리즘은 VElogV
- 2차원 배열 사용

```cpp
for (int i = 1; i <= n; i++)
    for (int j = 1; j <= n; j++)
        arr[i][j] = INF;

for (int i = 1; i <= n; i++)       //제자리는 0
		arr[i][i] = 0;

while(m--){
    cin >> from >> to >> weight;
    if (arr[from][to] > weight) 
        arr[from][to] = weight;
}

//경유지를 기준으로, 해당 경유지를 거쳐가는게 빠르다면 갱신
for (int i = 1; i <= n; i++)             // i경유지를 거치는 경우
    for (int j = 1; j <= n; j++)        
        for (int k = 1; k <= n; k++)    
            if (arr[j][i] != INF && arr[i][k] != INF)  //j->i i->k 로 가는 길이 있다면       
                arr[j][k] = min(arr[j][k], arr[j][i] + arr[i][k]);  // j->k로 가는길을 최소값으로 갱신
```

### 유니온파인드

> 노드를 두개씩 이어나가면서 두 노드가 같은 그래프에 있는지 판단
> 
- 상수시간

```cpp
for(int i=0; i<=n; i++) p[i] = i;
//or
iota(p, p+n+1, 0);                //위와 같이 동작

int find(int x){
    if(parent[x] == x) return x;//루트노드 반환

    int p = find(parent[x]); 
    parent[x] = p;      //부모노드로 갱신
    return p;
}

void merge(int x, int y){
    x = find(x);
    y = find(y);
                           // x == y 라면 이미 한 그래프로 이루어져있다는 것
													 // 이기 때문에 사이클이 형성된다는 뜻
    if(x != y) {
				if(x < y) swap(x,y);
		    parent[y] = x;
        friend_num[x] += friend_num[y];  // 그래프 내의 노드 수 체크 
    }
}
```

### 크루스칼 & 프림(빈도 낮음)

> 가중치가 가장 작은 간선부터 시작해 사이클이 생기지 않은 가중치가 그 다음으로 작은 간선을 추가시키며 MST를 완성하는 알고리즘
> 
- O(ElogE)
- MST 조건 3가지
    - 간선의 수 = 정점의 수 - 1   (E = V-1)
    - 사이클 x
    - 간선의 가중치 합이 최소

![Untitled](/C++/img/ProblemSolving/Untitled3.png)

- 유니온파인드로 구현

```cpp

v.push_back({C,{A,B}});  // c 비용 , a 정점1, b 정점2

sort(all(v));

for(int i=0; i<E; i++){
    int weight = v[i].first;
    int s = v[i].second.first;
    int e = v[i].second.second;

    s = find(s);
    e = find(e);

    if(s == e) continue;   // 이미 같은 그래프라면 패스

    p[s] = e;

    sum += weight;
    num++;

    if(num == V-1){        // MST의 특징 : 간선은 정점의 -1개 
        cout << sum;
        break;
    }
}
```

![Untitled](/C++/img/ProblemSolving/Untitled4.png)

- 우선순위큐로 구현
- 구현이 어려워 대체로 크루스칼 사용

### 위상정렬

> 방향 그래프에 존재하는 각 정점들의 선행순서를 위배하지 않으면서 모든 정점을 나열하는 것
사이클이 존재하는지 확인할 수도 있음
> 
- 사이클 x
- O(V+E)
- 정점을 지워 나갈 때마다 항상 indegree(들어오는 간선)이 0인 정점이 존재하기 때문에 그것에 해당하는 정점을 지워나가면서 알고리즘 짤 수 있음

```cpp
int indegree[32001];    //들어오는 간선 체크
vi v[32001];
queue<int> q;

while(m--){
    int a,b; cin >> a >> b;    // 간선의 방향 및 들어오는 간선 체크
    v[a].push_back(b);
    indegree[b]++;
}

for(int i=1; i<=n; i++)        // 들어오는 간선이 없는 노드 큐에 삽입
    if(indegree[i] == 0)
        q.push(i);

while(!q.empty()){
    int node = q.front(); q.pop();
    cout << node << " ";

    for(int i=0; i<v[node].size(); i++){    
        int x = v[node][i];
        indegree[x]--;            // 연결된 정점의 간선을 끊음
        if(indegree[x] == 0) q.push(x);  // 연결된 정점의 들어오는 간선이 0이면 큐에 삽입
    }
}   
```

![Untitled](/C++/img/ProblemSolving/Untitled5.png)

![Untitled](/C++/img/ProblemSolving/Untitled6.png)

### DP

1. 테이블 정의
2. 점화식 찾기
3. 초기값 설정

- 냅색 문제
    - **i번째 물건을 넣었을 때와 넣지 않았을 때, 둘 중 더 가치가 큰 것을 가져오면 되는 것**

```cpp
for(int i=1; i<=n; i++){
    for(int j=1; j<=k; j++){
        if(w[i] > j) d[i][j] = d[i-1][j];   // i번째 무게보다 가방이 버틸수있는 무게가 더 작다면 넣을 수 없음, 이 전의 값 유지
        else d[i][j] = max(d[i-1][j], d[i-1][j-w[i]] + v[i]);       //버틸수있는무게가 같거나 크면 넣을 수 있음
                                            // 넣지 않았을때와 넣었을 때, 둘 중 더 큰 것으로 초기화
    }
}

cout << d[n][k];
//d[A][B] = C : A번째 물건까지 왔고, 가방의 무게가 B일 때 가방에 담긴 물건들의 가치는 C
/*
4 7
6 13
4 8
3 6
5 12

0 0 0 0 0 13 13 
0 0 0 8 8 13 13
0 0 6 8 8 13 14
0 0 6 8 12 13 14

12865*/
```

- ***LCS*** (Longest Common Subsequence, 최장 공통 부분 수열)
    - 두 수열 중, 모두의 부분 수열이 되는 수열 중 가장 긴 것을 찾는 ***문제***

```cpp
string s1,s2; cin >> s1 >> s2;
int dp[1005][1005]={};

for(int i=1; i<=s1.size(); i++){        // 한개씩 뽑아서 비교
    for(int j=1; j<=s2.size(); j++){    // 비교당할 문자열
        if(s1[i-1] == s2[j-1]) dp[i][j] = dp[i-1][j-1] + 1;
        else dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
    }
}

cout << dp[s1.size()][s2.size()];
/*
ACAYKP   CAPCAK

\ 0 C A P C A K
0 0 0 0 0 0 0 0 
A 0 0 1 1 1 1 1
C 0 1 1 1 2 2 2
A 0 1 2 2 2 3 3
Y 0 1 2 2 2 3 3
K 0 1 2 2 2 3 4
P 0 1 2 3 3 3 4

*/
```

## 꿀팁 & 주의사항

---

### ❗Endl 절대 사용 금지

### 전역변수에 변수 선언 시 자동 초기화

### getline(cin, s)

> 띄어쓰기 포함 문자열 입력 받을 때
> 

### fill(arr, arr+N, 0)

> 0으로 초기화
> 

### iota(arr, arr+N, 0)

> 0,1,2, ... ,N 으로 초기화
> 

### reverse(all(T))

> 컨테이너, 문자열 뒤집기
> 

### is_sorted(all(T))

> 정렬되어 있는지 확인
> 
- 정렬 시 true, 아니면 false 반환

## 문자열

### stringstream

> 공백과 ‘\n’ 을 기준으로 문자열을 나눔
> 

```cpp
#include <sstream>

string s = "진영훈 1996";
stringstream stream;  //선언

stream.clear();       //초기화
stream.str(s);        //삽입

int year; string name;
stream >> name;       //입력,  cin >> s와 같은 느낌
stream >> year;
//or
vector<string> v(2);
for(auto &a : v) stream >> a; 
```

### isdigit(char)

> 숫자인지 판단
> 
- 맞으면 true, 틀리면 false

### strchr(”-_.”, char)

> 주어진 문자열에서 char이 있는지
> 
- 맞으면 1, 틀리면 0

### str1.find(str2, idx=0)

> idx부터 문자열 찾기
> 
- 찾는 문자가 있을 경우 해당 문자의 시작 인덱스 반환
- 찾는 문자가 없는 경우 `string::npos` 반환
- O(nm)으로 느린편이니 성능이 중요하다면 남발금지, KMP알고리즘 사용

### str.substr(idx, (len))

> idx부터 len만큼 자름
> 

### str1.insert(idx ,str2)

> idx부터 문자열 삽입
> 

### str1.erase(idx, len)

> idx부터 len만큼 삭제
> 

### str1.replace(idx, len, str2)

> idx부터 len만큼 str2로 치환
> 

### **regex_replace.(str1, regex(정규식), str2)**

> str1에서 정규식에 해당하는 모든 문자열을 str2로 치환 후 반환
> 

### stoi(str)

> string to int
> 

### to_string(int)

> int to string
> 

### Vector<int> v(3, 5)

> 3공간을 5로 초기화
> 

### vector<string> v(set.begin(), set.end()) ,   vpii v(all(map))

> Set, Map으로 중복 제거 후 벡터에 옮길 일 있을 때
> 

### sort(all(vpii), [](pii a, pii b) {
       return a.second < b.second;
});

> map의 value 정렬
> 

```cpp
vpii v(all(map));
sort(all(v), cmp);

bool cmp( const pii &a, const pii &b){
		return a.second < b.second;
}
```

### STL<int , cmp>

> STL의 사용자 정렬
> 
- priority_queue 는 반대로 적용
    - a > b 가 오름차순

```cpp
struct cmp{
    bool operator()(int a, int b){
        return a > b;               // 내림차순 정렬
		}
};
```

### map<int,int, greater<int>>

> map, set역순 정렬
> 

### prev(map.end(), (len)) / next(set.begin(), (len)) / advance(변수iter, 2)

> 배열의 인덱스처럼 접근 가능
prev - 왼쪽으로 보낸 만큼 반환
next - 오른쪽으로 보낸 만큼 반환
adv - iter를 2로 변경?
> 
- begin은 * 붙여서 사용하면 되지만 end는 끝 다음 값을 가르키기 때문에 prev 사용해야 함
- O(N) 걸리니 주의

### for(auto a : v) , for(auto &a : v)

> a는 복사 , &a는 원본이 a에 들어감
&를 사용한 후 값 변경 가능
> 

### STL Deque 중간 삽입,삭제,검색 가능

> for(auto a : Deque)
> 

### swap(T1, T2)

> 두 값 교환
> 

### sort(a, a+n, (cmp))  , Stable_Sort

> Sort는 Unstable
> 
- greater<>()로 역순

### auto [a, b, c] = {A, B, C} or tuple

> 값 한줄로 모두 받기
> 
- struct, tuple, pair도 가능

### Binary_search(a, a+n, target)

> 컨테이너 내의 원소 검색
> 
- 찾으면 1, 못찾으면 0

### lower_bound(a, a+n, target)  ,  upper_bound

> lower  -  target**보다 같거나, 없다면 그 다음 큰 숫자의 Iterator** 
upper -  target이 모두 나오고 난 다음의 Iterator
> 
- **반환형은 Iterator 이므로 배열 첫 번째 주소를 가리키는 배열의 이름을 빼줘야 함**
- map, set 도 가능

```cpp
lower_bound(arr, arr + 6, 6) - arr; // 배열

lower_bound(v.begin(), v.end(), 6) - v.begin();// 벡터

iter = map(or set).lower_bound(target); // map, set
```

- 내부 동작은 이분탐색

### **max_element(a, a+n),  min_element**

> 저장된 컨테이너에서 최대, 최소값 원소
> 
- 반환형 - Iterator이므로 위와 동일하게 사용

```cpp
*max_element(v.begin(), v.end()); //가장 큰 수
    
max_element(v.begin(), v.end()) - v.begin(); // 가장 큰 인덱스
```

### v.erase(unique(v.begin(), v.end()), v.end());

> 한 벡터에서 중복제거하기
> 
- 정렬된 상태여야 함
- unique
    - 중복제거 후 앞으로 정렬하고 쓰레기값이 시작되는 곳의 주소를 리턴
- erase
    - 그 후 쓰레기값을 날려버림

![Untitled](/C++/img/ProblemSolving/Untitled7.png)

### auto func = [&](int a){};

> 람다함수 선언
> 
- 재귀 불가능
- vpii v[n] 같은거 쓸 때
    - call by reference로 넘기기 힘듦

### function<void(int, int)> func = [&](int a, int b){};

> 람다함수 선언
> 
- 재귀 가능

---

## 풀이

### 섬끼리 짧은 다리 놓기

- 그냥 하면 O(N^2)
- 개선방안
    - ❗모든 섬에서 동시에 BFS 돌리기 - O(N^2)
    
	![Untitled](/C++/img/ProblemSolving/Untitled8.png)
    
    - 섬끼리 묶어서 BFS 한꺼번에 돌리기
    
	![Untitled](/C++/img/ProblemSolving/Untitled9.png)
    
- boj - 2146번

### 재귀는 Bottom Up으로 생각하기

> 함수 따라 들어가면서 계산하면 절대 안됨
아래 사진처럼 귀납적으로 잘 되는구나 하고 짜야함
양을 적게 해봐서 되면 그걸 기준으로 재귀 짜보기
> 

![Untitled](/C++/img/ProblemSolving/Untitled10.png)

![Untitled](/C++/img/ProblemSolving/Untitled11.png)

제일 작게 2를 기준으로 하면

1(개)의 원판을 기둥 1에서 2로 옮긴다 ← 여러개일수도있기때문에 출력x, 옮기는 작업임

2(번) 원판을 기둥1에서 3으로 옮긴다 ← 기준, 1개니까 이때 출력

1(개)의 원판을 기둥2에서 3으로 옮긴다

### 백트래킹 next_permutation(순서) , prev_permutation(역순)

```cpp
int a[3] = {1,2,3};
do{
    for(int i=0; i<3; i++)
        cout << a[i];
    cout << "\n";
}while(next_permutation(a,a+3));

/*
123
132
213
231
312
321
*/
```

```cpp
// 1234에서 순서 없이 수 2개를 모두 뽑는 경우의 수
// 12345개에서 3개를 뽑는 문제라면 a[5] = {0,0,0,1,1}
int a[4] = {0,0,1,1};
do{
    for(int i=0; i<4; i++)
        if(a[i] == 0)
            cout << i+1;
    cout << "\n";
}while(next_permutation(a,a+4));
/*
12    0011
13    0101
14    0110
23    1001
24    1010
34    1100
*/
```
