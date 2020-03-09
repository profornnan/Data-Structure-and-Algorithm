# 최소 스패닝 트리(MST)

### 스패닝 트리

* 무향 그래프의 스패닝 트리는 원래 그래프의 정점 전부와 간선의 부분집합으로 구성된 부분 그래프이다. 이때 스패닝 트리에 포함된 간선들은 정점들을 트리 형태로 전부 연결해야 한다.
* 선택된 간선들이 사이클을 이루지 않으며, 정점들이 꼭 부모-자식 관계로 연결될 필요는 없다.
* 그래프의 스패닝 트리는 유일하지 않다.



### 최소 스패닝 트리(Minimum Spanning Tree)

* 가중치 그래프의 스패닝 트리 중 가중치의 합이 가장 작은 트리를 찾는 문제를 최소 스패닝 트리 문제라고 한다.



---



## 크루스칼의 최소 스패닝 트리 알고리즘

* 크루스칼(Kruskal)의 최소 스패닝 트리 알고리즘은 그래프의 모든 간선을 가중치의 오름차순으로 정렬한 뒤, 스패닝 트리에 하나씩 추가해간다.
* 가중치가 작다고 해서 무조건 간선을 트리에 더하는 것은 아니다.
* 사이클이 생기는 간선은 고려에서 제외해야 한다.
* 크루스칼 알고리즘은 모든 간선을 한 번씩 검사하고 난 뒤 종료한다.



### 자료구조의 선택

* 중요한 것은 간선을 트리에 추가했을 때 이미 추가한 간선들과 합쳐 사이클을 이루는지 여부를 판단하는 부분이다.
* 어떤 간선을 추가해서 그래프에 사이클이 생기려면 간선의 양 끝 점은 같은 컴포넌트에 속해 있어야 한다.
* 상호 배타적 집합 자료구조 사용
* 그래프에 새 간선을 추가해 사이클이 생기는지를 확인하려면 두 정점이 이미 같은 집합에 속해 있는지를 확인하면 되고, 간선을 트리에 추가할 경우에는 이들이 포함된 두 집합을 합치면 된다.



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



## 프림의 최소 스패닝 트리 알고리즘

* 크루스칼 알고리즘과 같은 원리로 동작하는 또 다른 스패닝 트리 알고리즘
* 크루스칼 알고리즘에서는 여기저기서 산발적으로 만들어진 트리의 조각들을 합쳐 스패닝 트리를 만드는 반면, 프림 알고리즘에서는 하나의 시작점으로 구성된 트리에 간선을 하나씩 추가하며 스패닝 트리가 될 때까지 키워 간다.
* 선택된 간선들은 중간 과정에서도 항상 트리를 이루게 된다.
* 이미 만들어진 트리에 인접한 간선만을 고려한다.
* 선택할 수 있는 간선들 중 가중치가 가장 작은 간선을 선택하는 과정을 반복한다.



### 프림 알고리즘의 구현

```c++
// 프림의 최소 스패닝 트리 알고리즘
const int MAX_V = 100;
const int INF = 987654321;
// the number of vertices
int V;
// adjacency list. (id, weight)
vector<pair<int, int> > adj[MAX_V];
// heap nodes
struct MST_node {
	int id;
	int key;
	int parent;
};
// compare heap nodes
class Compare {
public:
	bool operator()(MST_node n1, MST_node n2) {
		return n1.key > n2.key;
	}
};

int prim(vector<pair<int, int> >& selected) {
	int ret = 0;
	selected.clear();
    
	priority_queue<MST_node, vector<MST_node>, Compare> q;
	vector<bool> added(V, false);
	vector<int> key(V, INF);
    
	MST_node src = { 0, 0, -1 };  // source node
	key[0] = 0;
	q.push(src);
    
	while (!q.empty())
	{
		MST_node min = q.top();
		q.pop();
		int u = min.id;
		if (added[u] == true)  // skip if already in MST
			continue;
		added[u] = true;
		ret += min.key;
		if (min.parent >= 0)
			selected.push_back(make_pair(min.parent, min.id));
		for (unsigned i = 0; i < adj[u].size(); i++) {
			int v = adj[u][i].first;
			int w = adj[u][i].second;
			if (!added[v] && key[v] > w) {
				key[v] = w;
				MST_node a = { v, w, u };
				q.push(a);
			}
		}
	}
	return ret;
}
```



