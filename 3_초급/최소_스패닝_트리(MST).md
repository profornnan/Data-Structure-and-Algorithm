# 최소 스패닝 트리(MST)

### 스패닝 트리

무향 그래프의 스패닝 트리는 원래 그래프의 정점 전부와 간선의 부분집합으로 구성된 부분 그래프이다.

이때 스패닝 트리에 포함된 간선들은 정점들을 트리 형태로 전부 연결해야 한다.

선택된 간선들이 사이클을 이루지 않으며, 정점들이 꼭 부모-자식 관계로 연결될 필요는 없다.

그래프의 스패닝 트리는 유일하지 않다.



### 최소 스패닝 트리(Minimum Spanning Tree)

가중치 그래프의 스패닝 트리 중 가중치의 합이 가장 작은 트리를 찾는 문제를 최소 스패닝 트리 문제라고 한다.



---



## 크루스칼의 최소 스패닝 트리 알고리즘

크루스칼(Kruskal)의 최소 스패닝 트리 알고리즘은 그래프의 모든 간선을 가중치의 오름차순으로 정렬한 뒤, 스패닝 트리에 하나씩 추가해간다.

가중치가 작다고 해서 무조건 간선을 트리에 더하는 것은 아니다.

사이클이 생기는 간선은 고려에서 제외해야 한다.

크루스칼 알고리즘은 모든 간선을 한 번씩 검사하고 난 뒤 종료한다.



### 자료구조의 선택

중요한 것은 간선을 트리에 추가했을 때 이미 추가한 간선들과 합쳐 사이클을 이루는지 여부를 판단하는 부분이다.

어떤 간선을 추가해서 그래프에 사이클이 생기려면 간선의 양 끝 점은 같은 컴포넌트에 속해 있어야 한다.

상호 배타적 집합 자료구조 사용

그래프에 새 간선을 추가해 사이클이 생기는지를 확인하려면 두 정점이 이미 같은 집합에 속해 있는지를 확인하면 되고, 간선을 트리에 추가할 경우에는 이들이 포함된 두 집합을 합치면 된다.



### 크루스칼 알고리즘의 구현

```c++
// 크루스칼의 최소 스패닝 트리 알고리즘
// 트리를 이용해 상호 배제적 집합을 구현한다.
// 유니온 파인드(Union-Find) 참고
struct DisjointSet;
const int MAX_V = 100;
// 정점의 개수
int V;
// 그래프의 인접 리스트. (연결된 정점의 번호, 간선 가중치) 쌍을 담는다.
vector<pair<int, int> > adj[MAX_V];

// 주어진 그래프에 대해 최소 스패닝 트리에 포함된 간선의 목록을 selected에 저장하고, 가중치의 합을 반환한다.
int kruscal(vector<pair<int, int> >& selected) {
    int ret = 0;
    selected.clear();
    // (가중치, (정점1, 정점2))의 목록을 얻는다.
    vector<pair<int, pair<int, int> > > edges;
    for(int u = 0; u < V; ++u)
        for(int i = 0; i < adj[u].size(); ++i) {
            int v = adj[u][i].first, cost = adj[u][i].second;
            edges.push_back(make_pair(cost, make_pair(u, v)));
        }
    // 가중치 순으로 정렬
    sort(edges.begin(), edges.end());
    // 처음엔 모든 정점이 서로 분리되어 있다.
    DisjointSet sets(V);
    for(int i = 0; i < edges.size(); ++i) {
        // 간선 (u, v)를 검사한다.
        int cost = edges[i].first;
        int u = edges[i].second.first, v = edges[i].second.second;
        // 이미 u와 v가 연결되어 있을 경우 무시한다.
        if(sets.find(u) == sets.find(v)) continue;
        // 이 둘을 합친다.
        sets.merge(u, v);
        selected.push_back(make_pair(u, v));
        ret += cost;
    }
    return ret;
}
```



