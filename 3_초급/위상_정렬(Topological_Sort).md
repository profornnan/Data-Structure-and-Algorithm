# 위상 정렬(Topological Sort)

## 위상 정렬 정의

* 위상 정렬(Topological Sort)은 순서가 정해져 있는 작업을 차례로 수행해야 할 때 그 순서를 결정해주기 위해 사용하는 알고리즘이다.
* 위상 정렬은 그래프를 정렬하는 것이고, 정렬 기준은 진입 차수의 비 내림차순 순서이다.
* 진입 차수가 0개 => N개 순으로 탐색하고, 정렬 과정을 거치며 진입 차수가 0인 노드들은 제거하고, 진입 차수가 0이 아닌 노드들은 점점 0으로 수정한 뒤 제거해가며 정렬하는 원리이다.
* 위상 정렬은 여러 개의 답이 존재할 수 있다.



## 위상 정렬 조건

* 위상 정렬은 DAG(Directed Acyclic Graph)에만 적용이 가능하다. (사이클이 발생하지 않는 방향 그래프)
* 사이클이 발생하는 경우 위상 정렬을 수행할 수 없다.
* 위상 정렬은 시작점이 존재해야 하는데 사이클 그래프에서는 시작점을 찾을 수 없다.



## 위상 정렬 설명

* 위상 정렬에는 DFS를 이용한 방법과 진입 차수(Indegree)를 이용한 방법이 있다.
* DFS 탐색 결과를 역으로 나타낸 결과가 위상 정렬 결과와 동일하다.



### Indgree를 이용한 위상 정렬

```c++
// Topological Sort(indegree)
const int MAX_V = 100;
// the number of vertices
int V;
// adjacency list
vector<int> adj[MAX_V];

vector<int> topological_sort() {
	vector<int> ret, indegree(V, 0);
	queue<int> q;
	// compute indegree
	for (int i = 0; i < V; i++) {
		for (int j = 0; j < adj[i].size(); j++) {
			indegree[adj[i][j]] += 1;
		}
	}
	// enqueue nodes of indegree 0
	for (int i = 0; i < V; i++) {
		if (indegree[i] == 0)
			q.push(i);
	}
	// process a node
	while (!q.empty()) {
		int u = q.front();
		q.pop();
		ret.push_back(u);
		for (int i = 0; i < adj[u].size(); i++) {
			int v = adj[u][i];
			if (--indegree[v] == 0)
				q.push(v);
		}
	}
	return ret;
}
```



1. 각 노드별로 진입 차수를 계산
2. queue에 진입 차수가 0인 노드들을 push 한다.
3. queue에서 노드를 하나씩 꺼내면서 결과 배열에 저장하고, 연결된 노드들의 진입 차수를 하나씩 감소시킨다.
4. 만약 수정 결과 진입 차수가 0인 노드가 있다면 queue에 넣는다.
5. queue가 빌 때까지 반복한다.



