# Graphs
## Traversals
### BFS

Source - https://cp-algorithms.com/graph/breadth-first-search.html

The algorithm takes as input an unweighted graph and the id of the source vertex  
s. The input graph can be directed or undirected, it does not matter to the algorithm.

Implementation

```cpp

vector<int> p, d;
vector<bool> used;

void run_bfs(int s, vector<vector<int>> &g) {
    deque<int> q;

    q.push_back(s);
    p[s] = -1; // parent of s
    used[s] = true;
    
    while(!q.empty()) {
        int v = q.front();
        q.pop_front();
        for(int u:g[v]) {
            if(!used[u]) {
                used[u] = true;
                q.push_back(u);
                p[u] = v;
                d[u] = d[v] + 1;
            }
        }
    }
}

void trace_path(int s, int e) {
    vector<int> path;
    int v = e;
    if(!used[e]) {
        cout << "No Path!";
    } else {
        while(v != -1) {
            path.push_back(v);
            v = p[v];
        }
    }
    reverse(path.begin(), path.end());
    for(int x:path) {
        cout << x << " ";
    }
}

```

_________________

### DFS
Source - https://cp-algorithms.com/graph/depth-first-search.html
```cpp

vector<int> d, p;
vector<bool> used;

// DFS with finding out the components
void dfs(int v, vector<vector<int>> &g, vector<int> &component) {
    used[v] = true;
    component.push_back(v);
    for(int u:g[v]) {
        if(!used[u]) {
            dfs(u, g, component);
        }
    }
}

// Iterative DFS 
void dfs_iter(int v, vector<vector<int>> &g, vector<int> &component) {
    stack<int> st;
    st.push(v);
    while(!st.empty()) {
        int cur = st.top();
        st.pop();
        if(!used[cur]) {
            used[cur] = true;
            component.push_back(cur);
            for(int &u:g[cur]) {
                st.push(u);
            }
        }
    }
}

// DFS with timein and timeout & 3 states.

vector<int> color;
vector<int> time_in, time_out;
int dfs_timer = 0;

void dfs_colour(int v, vector<vector<int>> &g, vector<int> &component) {
    color[v] = 1;
    time_in[v] = dfs_timer++;
    component.push_back(v);
    for(int &u:g[v]) {
        if(color[u] == 0) {
            dfs_colour(u, g, component);
        }
    }
    color[v] = 2;
    time_out[v] = dfs_timer++;
}

int main() {
    for(int i=0; i<n; ++i) {
        vector<int> component;
        if(color[i] == 0)  {
            cc++;
            dfs_colour(i, g, component);
            components.push_back(component);
        }
    }
}

```
___________

## Bipartite Graph & Color algorithm

```cpp
    vector<int> color;
    bool dfs(vector<vector<int>> &g, int v, int col) {
        color[v] = col;
        bool tmp = true;
        for(int &u:g[v]) {
            /*
            Logic below is incorrect because here I'm not exploring all branches before returning.
            I find the first branch's solution and return that immediately.
            if(color[u] == -1) {
                return dfs(g, u, col^1);
            }
            */
            if(color[u] == -1) tmp = tmp & dfs(g, u, col^1);


            // This is the correct solution, return only if dfs returns false, else just keep exploring.
            // if(color[u] == -1) {
            //     if(!dfs(g, u, col^1)) return false;
            // }
            else if(color[u] == col) {
                return false;
            } 
        }
        return tmp;
    }
```

________________

## Cycle Detection

https://cp-algorithms.com/graph/finding-cycle.html


### Undirected Graph - DFS

https://www.geeksforgeeks.org/problems/detect-cycle-in-an-undirected-graph/0


```cpp
vector<bool> vis;
bool dfs(vector<vector<int>> &g, int v, int p) {
    vis[v] = true;
    for(int &u:g[v]) {
        if(!vis[u]) {
            // If cycle is found anywhere, return true; Note that return(dfs(g,u,v)) is incorrect.
            if(dfs(g, u, v)) return true;
        } else if(u != p) return true;
    }
    return false;
}
bool isCycle(vector<vector<int>>& adj) {
    int n = adj.size();
    vis.resize(n, 0);
    bool ans = false;
    for(int i=0; i<n; i++) {
        if(!vis[i]) ans |= dfs(adj, i, -1);
    }
    return ans;
}
```

### Directed Graph - DFS

https://www.geeksforgeeks.org/problems/detect-cycle-in-a-directed-graph/1


The main difference here is that there can be multiple sources to reach a vertex. 
In undirected, we can explore the completed connected component at once that's not 
necessarily possible in DFS so you can find wrong cycles using the same algorithm.

We can use color algorithm in this case

```cpp
vector<int> color, parent;
int cycle_start, cycle_end;
bool dfs(vector<vector<int>> &g, int v) {
    color[v] = 1;
    for(int &u:g[v]) {
        if(color[u] == 0) {
            parent[u] = v;
            if(dfs(g, u)) return true;
        }
        // Note that if a node is already completed it's dfs, it's not a part of cycle. 
        else if(color[u] == 1) {
            cycle_start = u;
            cycle_end = v;
            return true;
        }
    }
    color[v] = 2;
    return false;
}
```

_____________________

## Topological Sorting

Source - [CPAlgorithms Article](https://cp-algorithms.com/graph/topological-sort.html)

You are given a directed graph with n vertices and m edges. You have to find an order of the vertices, so that every edge leads from the vertex with a smaller index to a vertex with a larger one.

In other words, you want to find a permutation of the vertices (topological order) which corresponds to the order defined by all edges of the graph.


** Topological Order exists only in a Directed Acyclic Graph **

### 1. Kahn's Algorithm (In-degree Based)

Algorithm Steps:
1. Compute In-degrees
2. Enqueue all vertices with in-degree 0
3. Pull from queue and it to topo order, reduce the indegree of adj vertices by 1, If any updated vertex has in-degree 0, enqueue it.
4. Check for cycles - if order doesn't contain all the vertecies, there's a cycle.

```cpp
vector<int> topologicalSortKahn(int N, vector<vector<int>>& adj) {
    vector<int> inDegree(V, 0); // Store in-degrees of all vertices
    vector<int> topoOrder;      // Store the topological order

    for (int i = 0; i < N; i++) {
        for (int v : adj[i]) {
            inDegree[v]++;
        }
    }

    queue<int> q;
    for (int u = 0; u < N; u++) {
        if (inDegree[u] == 0) {
            q.push(u);
        }
    }

    while (!q.empty()) {
        int u = q.front();
        q.pop();
        topoOrder.push_back(u); 
        for (int v : adj[u]) {
            inDegree[v]--;
            if (inDegree[v] == 0) {
                q.push(v);
            }
        }
    }
    if (topoOrder.size() != V) {
        cout << "Graph contains a cycle. No valid topological order exists." << endl;
        return {};
    }
    return topoOrder;
}

```

### 2. DFS

```cpp

vector<int> color;
vector<int> topoOrder;
bool dfs(int v, vector<vector<int>> &g) {
    color[v] = 1;
    for(int &u:g[v]) {
        if(color[u] == 0) {
            if(dfs(u, g)) {
                // cycle detected.
                return true;
            } else if(color[u] == 1) {
                // back edge
                return true;
            }
        }
    }
    color[v] = 2;
    topoOrder.push_back(v);
    return false;
}

// Now just reverse the topoOrder array.

```

Example Problems 

1. https://leetcode.com/problems/course-schedule/
2. https://leetcode.com/problems/course-schedule-ii/description/


## Shortest/Longest Paths

### Shortest path using BFS 

[Only Possible in unweighted graphs]

Example - https://leetcode.com/problems/word-ladder/

```cpp
unordered_map<string, bool> vis;
unordered_map<string, int> d;

bool isReachable(string &a, string &b) {
    int cnt = 0;
    for(int i=0; i<a.length(); i++) {
        cnt += (a[i]!=b[i]);
    }
    return cnt == 1;
}
int bfs(string s, string tar, vector<string> &A) {
    queue<string> q;
    q.push(s);
    d[s] = 0;
    vis[s] = true;
    while(!q.empty()) {
        string v = q.front();
        q.pop();
        if(v == tar) return d[v];
        for(string &u:A) {
            if(isReachable(v, u)) {
                if(vis[u] == true) continue;
                vis[u] = true;
                d[u] = d[v] + 1;
                q.push(u);
            }
        }
    }
    if(vis[tar]) return d[tar];
    return -1;
}
int ladderLength(string s, string t, vector<string>& A) {
    return bfs(s, t, A) + 1;
}
```

Follow up - https://leetcode.com/problems/word-ladder-ii/description/



_____________________

### Dijkstra - Single Source to All

Greedy algorithm that works like BFS, but you use priority queue instead of queue.
Pick the shortest distance yet and relax the adj edges.

Source - https://cp-algorithms.com/graph/dijkstra_sparse.html

Practice Problems 
1. https://leetcode.com/problems/network-delay-time/description/
2. https://leetcode.com/problems/path-with-minimum-effort/description/ (Good problem)
3. https://leetcode.com/problems/cheapest-flights-within-k-stops/description/ (Good Problem)


```cpp
const int INF = 1000000000;
vector<vector<pair<int, int>>> adj;

void dijkstra(int s, vector<int> & d, vector<int> & p) {
    int n = adj.size();
    d.assign(n, INF);
    p.assign(n, -1);

    d[s] = 0;
    using pii = pair<int, int>;
    priority_queue<pii, vector<pii>, greater<pii>> q;
    q.push({0, s});
    while (!q.empty()) {
        int v = q.top().second;
        int d_v = q.top().first;
        q.pop();
        if (d_v != d[v]) continue;
        for (auto edge : adj[v]) {
            int to = edge.first;
            int len = edge.second;
            if (d[v] + len < d[to]) {
                d[to] = d[v] + len;
                p[to] = v;
                q.push({d[to], to});
            }
        }
    }
}
```
______________

### Floyd Warshall Algorithm - All Sources All Destinations

Source - https://cp-algorithms.com/graph/all-pair-shortest-path-floyd-warshall.html

```cpp
void floydWarshall(vector<vector<int>>& graph, int n) {
    vector<vector<int>> d = graph;
    for (int k = 0; k < n; ++k) {
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                if (d[i][k] < INF && d[k][j] < INF)
                    d[i][j] = min(d[i][j], d[i][k] + d[k][j]); 
            }
        }
    }
}
```

### Problems 
1. https://leetcode.com/problems/find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance/description/


______________________________

## Bridges & Articulation Points

Source - https://cp-algorithms.com/graph/bridge-searching.html


```cpp

vector<vector<int>> g(100);
vector<bool> used;
vector<int> tin, low;
int timer;

void dfs(int v, int p) {
    used[v] = true;
    tin[v] = low[v] = timer++;
    for(int &u:g[v]) {
        if(u == p) continue;
        // backward edge case
        if(used[u]) {
            low[v] = min(low[v], tin[u]);
        } else {
            dfs(u, v);
            low[v] = min(low[v], low[u]);
            if(low[u] > tin[v]) {
                cout << "edge " << u << "-->" << v << " is a bridge" << '\n';
            }
        }
    }
}
```

## Problems

Good Basic Problem set Here -

https://leetcode.com/discuss/general-discussion/655708/graph-for-beginners-problems-pattern-sample-solutions/

https://leetcode.com/problems/flood-fill/description/

https://leetcode.com/problems/surrounded-regions/

https://leetcode.com/problems/rotting-oranges/description/

https://leetcode.com/problems/number-of-enclaves/description/

https://leetcode.com/problems/time-needed-to-inform-all-employees/description/

https://leetcode.com/problems/number-of-closed-islands/solutions/

https://leetcode.com/problems/max-area-of-island/description/

https://www.geeksforgeeks.org/problems/detect-cycle-in-a-directed-graph/1

https://www.geeksforgeeks.org/problems/detect-cycle-in-an-undirected-graph/0

https://leetcode.com/problems/find-eventual-safe-states/description/ (Good Question on cycles)

https://leetcode.com/problems/cheapest-flights-within-k-stops/description/ (V good problem on BFS)

https://leetcode.com/problems/path-with-minimum-effort/ (dijkstra style algo)

https://leetcode.com/problems/swim-in-rising-water/description/ (dijkstra style algo, similar to above problem)

