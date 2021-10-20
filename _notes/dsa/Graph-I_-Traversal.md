---
layout: note
name: Graph I_ Traversal
categories: DSA
date: 2021-10-20
---
${toc}

# Graph Traversal Problems O(V+E)

## DFS and BFS

```cpp
//the true entries in visited array denotes a connected component; that can be used for getting connected components
vector<vector<int>> adj_list;
int V;//number of vertices
void dfs(int u){
    static vector<int> visited(V, 0);
    visited[u] = 1;
    cout<<v<<"->";//final op
    for(auto x:adj_list[u])
        if(!visited[x]) dfs(x);
}

void bfs(int v){
    vector<int> visited(V,0);
    queue<int> q;
    q.push(v);
    visited[v] = 1;
    while(!q.empty()){
        int u = q.front(); q.pop();
        cout<<u<<"->";//final op
        for(auto x:adj_list[u])
            if(!visited[x]){
                q.push(x);
                visited[x] = 1;
            }
    }
}
```

## Flood Fill

```cpp
vector<vector<char>> grid = {
    {'#','#','#','#','#','#'},
    {'#','$','$','$','#','#'},
    {'#','#','$','#','#','#'},
    {'#','#','$','#','#','#'},
    {'#','#','$','$','#','#'},
    {'#','#','#','$','#','#'}
};
int C = 6, R = 6;
//also counts number of filled pixels; dfs based
int floodfill(int r, int c, char c1, char c2){
    //directions
    static int drow[] = {0, 0,1,1, 1,-1,-1,-1};
    static int dcol[] = {1,-1,0,1,-1, 0, 1,-1};
    if(r<0 || r>=R || c<0 || c>=C) return 0;
    if(grid[r][c] != c1) return 0;

    int ans = 1;
    grid[r][c] = c2;
    for(int i=0;i<8;i++)
        ans += floodfill(r+drow[i], c+dcol[i], c1, c2);
    return ans;
}
```

## Topological Sort(of DAG- directed acyclic graph)

- DAG: directed graph that will never form loops
- topological sort: vertices ordering such that for every edge u->v u comes before v in the ordering

```cpp
vector<vector<int>> adj_list;
int V;

stack<int> top_sort;
vector<int> visited(V, 0);
void dfs_util(int u){
    visited[u] = 1;
    for(auto x: adj_list[u]){
        if(!visited[x])
            dfs_util(x);
    }
    top_sort.push(u);
}
void topological_sort(){
    for(int i=0; i<V; i++)
        if(!visited[i]) 
            dfs_util(i);
    
        //final op
    while(!top_sort.empty()){
        cout<<top_sort.top()<<",";
        top_sort.pop();
    }
    cout<<'\n';
}
```

### Kahn's Algorithm(topological sort BFS style)

```cpp
vector<vector<int>> adj_list;
int V;
void kahn_topological_sort(){
    vector<int> in_degree(V, 0);
    vector<int> result;
    for(auto x:adj_list)
        for(auto y:x)
            in_degree[y]++;

    queue<int> q;
    for(int i=0; i<V; i++)
        if(in_degree[i] == 0) q.push(i);

    while(!q.empty()){
        int u = q.front();q.pop();
        result.push_back(u);

        for(auto neighbor : adj_list[u]){
            in_degree[neighbor]--;
            if(in_degree[neighbor] == 0) q.push(neighbor);
        }
    }
        //final op
    for(auto x:result) cout<<x<<", ";
    cout<<'\n';
}
```

## Bipartite Graph Check

- bipartite graph: such graph that can be divided into two disjoint sets such that every edge will take us from one of the sets to other

```cpp
vector<vector<int>> adj_list;
int V;
bool bipartite_check(int v){
    vector<int> color(V,-1);
    queue<int> q;q.push(v);
    color[v] = 0;

    while(!q.empty()){
        int u = q.front(); q.pop();
        for(auto x:adj_list[u]){
            if(color[x] == -1){
                q.push(x);
                color[x] = 1 - color[u];
            }else{
                if(color[x] == color[u]) return false;
            }
        }
    }
    return true;
}
```

## Graph Edge Properties Check

<div class="mermaid">%%{init: {'theme': 'base', 'themeVariables': { 'primaryColor': '#ffff33', 'background': '#ff0000','titleColor':'#ffffff','nodeBorder':'#000000'}}}%%
flowchart LR
    1 --- 2
    1 --> 3
    2 --> 4
    4 --> 6
    3 --> 5
    5 --> 7
    5 --> 8
    1 -...-> 8
    6 -..-> 2
    2 -..-> 5

</div>
- in the above diagram
    - solid lines are tree edges
    - 1->8 is forward edge
    - 2->1 is bidirectional edge
    - 2->5 is cross edge
    - 6->2 is back edge

```cpp
vector<vector<int>> adj_list;
int V;
const int UNVISITED = 0, EXPLORED = 1, VISITED = 2;
// for forward or cross edge distinction maybe we can replace these three flags with some bitmask thing, in which 2 bits will be used for these three flags and other will be used for identifying the branch(maybe, not sure)
vector<int> parent(V,0), state(V,UNVISITED);

void dfs_util(int u){
    state[u] = EXPLORED;
    for(auto x: adj_list[u]){
        if(state[x] == UNVISITED){ //EXPLORED -> UNVISITED
            printf("Tree Edge (%d,%d)\n",u,x);
            parent[x] = u;
            dfs_util(x);
        }else if(state[x] == EXPLORED){//EXPLORED -> EXPLORED
            if(x == parent[u]) printf("Two Way Edge (%d,%d)\n",u,x);
            else printf("Back Edge (%d,%d)\n",u,x);
        }else if(state[x] == VISITED){//EXPLORED -> VISITED
            printf("Forward/Cross Edge (%d,%d)\n",u,x);
        }
    }
    state[u] = VISITED;
}

void edge_property_check(){
    int nComponent = 1;
    for(int i=0; i<V; i++)
        if(state[i] == UNVISITED){
            printf("COMPONENT %d:\n",nComponent++);
            dfs_util(i);
        }
}
```

## Articulation vertices and edges

```cpp
vector<int> dfs_num(V,-1), dfs_low(V,0), dfs_parent(V,-1), artVertex(V,-1);
int nCounter = 0, dfs_root, nRootChildren;

void dfs_util(int u){
    dfs_low[u] = dfs_num[u] = nCounter++;
    for(auto x:adj_list[u]){
        if(dfs_num[x] == -1){
            dfs_parent[x] = u;
            if(u == dfs_root) nRootChildren++;
            dfs_util(x);

            if(dfs_low[x] >= dfs_num[u]) artVertex[u] = 1;
            if(dfs_low[x] > dfs_num[u]) 
                printf(" Edge (%d, %d) is a bridge\n", u, x);
            dfs_low[x] = min(dfs_low[x], dfs_low[u]);
        }
        else if(x != parent[u]) dfs_low[u] = min(dfs_low[u], dfs_num[x]);
    }
}
void articulationPointAndBrige(){
    printf("Bridges:\n");
    for(int u = 0; u < V; ++u){
        if(dfs_num[u] == -1){
            dfs_root = u; nRootChildren = 0;
            dfs_util(u);
            artVertex[dfs_root] = (nRootChildren > 1); // special case
        }
    }
    printf("Articulation Points:\n");
    for (int u = 0; u < V; ++u)
        if (artVertex[u])
            printf(" Vertex %d\n", u);
}
```

## Kosaraju's Algorithm for Strongly Connected Components

```cpp
stack<int> stk;
vector<int> visited(V,0);
vector<vector<int>> adj_list_transpose(V);
void dfs_util(int u){
    visited[u] = 1;
    for(auto x:adj_list[u])
        if(!visited[x]) dfs_util(x);
    stk.push(u);
}
void dfs_util2(int u){
    visited[u] = 1;
    cout<<u<<", ";
    for(auto x:adj_list_transpose[u])
        if(!visited[x]) dfs_util2(x);
}
void kosaraju_algorithm(){
    for(int i=0; i<V; i++)
        if(!visited[i]) dfs_util(i);

    visited.assign(V,0);
    for(int i=0; i<V; i++)
        for(auto neighbor:adj_list[i])
            adj_list_transpose[neighbor].push_back(i);
    
    while(!stk.empty()){
        int v = stk.top(); stk.pop();
        if(!visited[v]){
            dfs_util2(v);
            cout<<'\n';
        }
    }
}
```