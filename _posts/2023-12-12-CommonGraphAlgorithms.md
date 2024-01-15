---
layout: post
title:  "Common Graph Algorithms"
categories: LeetCode
tags:  Graph-Algorithm LeetCode
author: Marcus
catalog: yes
---

* content
{:toc}
# How to represent graph

1. **Adjacency List:** An Adjacency list is an array consisting of the address of all the [linked lists](https://www.geeksforgeeks.org/data-structures/linked-list/). The first node of the linked list represents the vertex and the remaining lists connected to this node represents the vertices to which this node is connected. This representation can also be used to represent a weighted graph. The linked list can slightly be changed to even store the weight of the edge.
2. **Adjacency Matrix:** Adjacency Matrix is a [2D array](https://www.geeksforgeeks.org/multidimensional-arrays-in-java/) of size V x V where V is the number of vertices in a graph. Let the 2D array be `adj[][]`, a slot `adj[i][j] = 1` indicates that there is an edge from vertex i to vertex j. Adjacency matrix for undirected graph is always symmetric. Adjacency Matrix is also used to represent weighted graphs. If `adj[i][j] = w`, then there is an edge from vertex i to vertex j with weight w.

Ref: https://www.geeksforgeeks.org/comparison-between-adjacency-list-and-adjacency-matrix-representation-of-graph/

## how to construct Adjacency List

![exmaple](/imgs/inposts/2023-12-12_CommonGraphAlgorithms_0.png)

```python
egdes = [[2,1,1],[2,3,1],[3,4,1]]

graph = collections.defaultdict(list)
for u,v,w in times:
    graph[u].append((v, w))
```

&nbsp;

# Dijkstra

Dijkstra's algorithm to find the shortest path between one vertex(root) and the others. It picks the unvisited vertex with the lowest distance, calculates the distance through it to each unvisited neighbor, and updates the neighbor's distance if smaller. Mark visited when done with neighbors.

Time: `O(E+VlogV)`

Space: `O(V+E)`

Illustration of Dijkstra Algorithm:

![exmaple](/imgs/inposts/2023-12-12_CommonGraphAlgorithms_1.gif)

```python
def dijkstra(edges: List[List[int]], root: int) -> Dict[int, int]:
    # construct adjacency list
    graph = collections.defaultdict(list)
    for u,v,w in edges:
        graph[u].append((v, w))
    n = len(graph)
    # set up "inf" distances
    dist = {i:float('inf') for i in range(1, n+1)}
    # set up distance for root
    dist[root] = 0
    # set up visited node set
    visited = set()
    # set up priority queue
    heap = [(0,root)]
    # if tracking is need add parents dict to store shortest
    parents = {i:-1 for i in range(1, n+1)}
    while heap:
        # get node u from priority queue
        distu, u = heapq.heappop(heap)
        # if the node is visited, skip
        if u in visited:
            continue
        # node u visited
        visited.add(u)
        # relax nodes distance adjacent to u
        for v, w in graph[u]:
            if distu + w < dist[v]:
                dist[v] = distu + w
                parent[v] = u
                heapq.heappush(heap, (dist[v], v))
    return dist
```

&nbsp;

# Bellman-Ford

A **Bellman-Ford** algorithm is also guaranteed to find the shortest path in a graph, similar to Dijkstra’s algorithm. Although Bellman-Ford is slower than Dijkstra’s algorithm, it is capable of handling graphs with negative edge weights, which makes it more versatile. The shortest path cannot be found if there exists a negative cycle in the graph. If we continue to go around the negative cycle an infinite number of times, then the cost of the path will continue to decrease (even though the length of the path is increasing). As a result, Bellman-Ford is also capable of detecting negative cycles, which is an important feature.

**Principle of Relaxation of Edges for Bellman-Ford:**

- It states that for the graph having ***\*N\**** vertices, all the edges should be relaxed ***\*N-1\**** times to compute the single source shortest path.
- In order to detect whether a negative cycle exists or not, relax all the edge one more time and if the shortest distance for any node reduces then we can say that a negative cycle exists. In short if we relax the edges ***\*N\**** times, and there is any change in the shortest distance of any node between the ***\*N-1th\**** and ***\*Nth\**** relaxation than a negative cycle exists, otherwise not exist.

Time: `O(VE)`
Space: `O(N)`

```python
def bellmanFord(edges: List[List[int]], n: int, root: int) -> Dict[int, int]:
    # set up "inf" distances
    dist = {i:float('inf') for i in range(1, n+1)}
    # set up distance for root
    dist[root] = 0
    # relax n-1 times
    for _ in range(n-1):
        for u, v, w in edges:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
    return dist
```

&nbsp;

# SPFA

The **shortest path faster algorithm (SPFA)** is based on [Bellman-Ford](https://www.geeksforgeeks.org/bellman-ford-algorithm-dp-23/) algorithm where every vertex is used to relax its adjacent vertices but in SPF algorithm, a queue of vertices is maintained and a vertex is added to the queue only if that vertex is relaxed. This process repeats until no more vertex can be relaxed. 

The following steps can be performed to compute the result: 

1. Create an array **d[]** to store the shortest distance of all vertex from the source vertex. Initialize this array by infinity except for d[S] = 0 where **S** is the source vertex.
2. Create a [queue](http://www.geeksforgeeks.org/queue-data-structure/) **Q** and push starting source vertex in it.
   - while Queue is not empty, pop leftmost vertex **u**
   - do the following for each edge(u, v) in the graph
     - If d[v] > d[u] + weight of edge(u, v)
       - d[v] = d[u] + weight of edge(u, v)
       - If vertex v is not present in Queue, then push the vertex v into the Queue.

Time: average `O(E)`, worst `O(VE)`
Space: `O(V+E)`

```python
def SPFA(edges: List[List[int]], root: int) -> Dict[int, int]:
    # construct adjacency list
    graph = collections.defaultdict(list)
    for u,v,w in edges:
       graph[u].append((v,w))
   	n = len(graph)
    # set up "inf" distances
    dist = {i: float('inf') for i in range(1,n+1)}
    dist[root] = 0
    # set up queue
    queue = collections.deque([root])
    # to check if vertex is present in queue or not
    isInQ = {i: False for i in range(1, n+1)}
    isInQ[root] = True
    while queue:
        u = queue.popleft()
        isInQ[u] = False
        for v,w in graph[u]:
            if dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
                if not isInQ[v]:
                    queue.append(v)
                    isInQ[v] = True
    return dist
```

# Floyd-Warshall

The **Floyd Warshall Algorithm** is an all pair shortest path algorithm unlike [Dijkstra](https://www.geeksforgeeks.org/dijkstras-shortest-path-algorithm-greedy-algo-7/) *and* [Bellman Ford](https://www.geeksforgeeks.org/bellman-ford-algorithm-dp-23/) *which are single source shortest path algorithms. This algorithm works for both the* **directed** *and* **undirected weighted** *graphs. But, it does not work for the graphs with negative cycles (where the sum of the edges in a cycle is negative). It follows* [Dynamic Programming](https://www.geeksforgeeks.org/introduction-to-dynamic-programming-data-structures-and-algorithm-tutorials/) approach to check every possible path going via every possible node in order to calculate shortest distance between every pair of nodes.

**Floyd Warshall Algorithm Algorithm:**

- Initialize the solution matrix same as the input graph matrix as a first step.
- Then update the solution matrix by considering all vertices as an intermediate vertex. 
- The idea is to pick all vertices one by one and updates all shortest paths which include the picked vertex as an intermediate vertex in the shortest path. 
- When we pick vertex number k as an intermediate vertex, we already have considered vertices `{0, 1, 2, .. k-1}` as intermediate vertices. 
- For every pair `(i, j)` of the source and destination vertices respectively, there are two possible cases. 
  - k is not an intermediate vertex in shortest path from `i` to `j`. We keep the value of `dist[i][j]` as it is. 
  - k is an intermediate vertex in shortest path from `i` to `j`. We update the value of `dist[i][j] `as` dist[i][k] + dist[k][j]`, if `dist[i][j] > dist[i][k] + dist[k][j]`

Time: `O(V^3)`
Space: `O(V^2)`

```python
def networkDelayTime(self, edges: List[List[int]]) -> List[List[int]:
    # construct adjacency list
    graph = collections.defaultdict(list)
    for u,v,w in edges:
       graph[u].append((v,w))
   	n = len(graph)
    # construct distance matrix                                                       
    dist = [[float('inf') for _ in range(n)] for _ in range(n)]
    for i in range(n):
        dist[i][i] = 0
    for u,v,w in edges:
        dist[u-1][v-1] = w
    # DP                                                       
    for k in range(n):
        for i in range(n):
            for j in range(n):
                dist[i][j] = min(dist[i][k]+dist[k][j], dist[i][j])
	return dist
```

