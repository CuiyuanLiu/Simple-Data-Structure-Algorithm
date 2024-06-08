# 2 Graph
## 2.1 Graph Structure
We could introduce the adjacency matrix to describe the graph data structure, graph could be considered as a combination of a bunch of vertices and edges connected these vertices, for each pair there exists an unique edge connects so. For instance, in describing i ~ j in graph, says $A_{ij} = w_{ij}$ where $w_{ij}$ represents the weight function of i ~ j normally be 1. With this, we could describe directed graph with 2 separate weights (e.g. 1 and -1), and undirected graph with weight == 1, graph with different weights also expressed so hence could use number representing colour here.

We try write a graph structure in python, java, c++ and rust. First we need to specify some classes needed:
- Class Vertex, a single node stores information includes color, num, adjacency and so forths.
```python
class Vertex:
    def __init__(self):
        self.color = None
        self.number = 0 
        self.adjs  = []
        self.next = None
        
    def setup_vertex(self, ipt_color: str, ipt_num: int):
        self.color = ipt_color
        self.number = ipt_num
        return self
```

- Class graph, need to have an adjacency matrix as attribute stores information about connectedness between vertices, stored as a list of list.
```python
class Graph:
    def __init__(self):
        self.edge_num = 0
        self.vertex_num = 0
        self.vertex_lst = [] 
        self.adjs_mat = []
        
        # DFS search and BFS search
        self.found = False
        self.visited_lst = []
```
We set up a new graph by follows. Notes that in python, 
- 1. Methods of copying information consists of shallow copying and deep copying, shallow copying would do a simple repeation of a kind of similar variables, but deep copying would deeply affects the structure themselves hence vairate the values. Thereby we need to do deep-copying for information in these kinds
`self.adjs_mat = [([0] * self.vertex_num) for p in range(self.vertex_num)]`
instead of `self.adjs_mat = [[0] * self.vertex_num] * self.vertex_num` which did in a shallow copying way.
```python
class Graph:
    def setup_graph(self, vertices_size: int, vertices_seires: list, edges_lst: list):
        c = 0
        for v in vertices_seires:
            c = c + 1
            new_v = Vertex()
            new_v = new_v.setup_vertex(c, v)
            self.add_vertex(new_v)
        self.adjs_mat = [([0] * self.vertex_num) for p in range(self.vertex_num)] 
        for e in edges_lst: self.add_edge(e[0] - 1, e[1] - 1, e[2]) # here e == "3 4 1" Means 3 connects 4 with weight 1.
        return self
```
Next design functions for adding and deleting edges:
```python
class Graph:
    # adjs_mat[i][j] = weight between i, j. If adjs_mat[i][j] = 0, then says i, j is not connected.
    # In specific, we could use weight label color counting.
    def add_edge(self, i, j, weight):
        try:
            if (weight > 0 and (self.adjs_mat[i][j] == 0 and self.adjs_mat[j][i] == 0)):
                self.adjs_mat[i][j] = weight
                self.adjs_mat[j][i] = weight
                self.edge_num = self.edge_num + 1
        except: pass
    
    # Delete an edge of graph.
    def delete_edge(self, i, j):
        try:
            if (self.adjs_mat[i][j] != 0 and self.adjs_mat[j][i] != 0):
                self.adjs_mat[i][j] = 0
                self.adjs_mat[j][i] = 0
                self.edge_num = self.edge_num - 1
        except: pass
    
    # Add a new vertex to the existed graph. And construct a linked_list from first element to last.
    def add_vertex(self, new_vertex: Vertex):
        self.vertex_lst.append(new_vertex)
        self.vertex_num = self.vertex_num + 1
        if (len(self.vertex_lst) > 0):
            try: self.vertex_lst[-1].next = new_vertex
            except: pass
        return self.vertex_lst
```
Hereby we complete all needs for a structure with fixed vertices and edges could be updated.

In java we write in the following ways:
In rust we write in the following ways:
In c++  we write in the following ways:

## 2.2 Searching Method of the Graph
Searching method in graph include depth-first search and breadth-first search, there two ways provides approaches from a given starting vertex to a target destination, here we specified $s$ and $t$ as the label of these starting and target vertices.

- Depth-first Search 深度优先搜索
This searching method is searching along with paths one by one, from the parent node digging to one particular children node and then children's children.
```python
# DFS - Depth First Search
# Principle: Visited from a starting vertex, and then visit all adj verteices connected to it.
# Time Complexity: O(#Edges), Space Complexity: O(#vertex)
# -1 represented none for prev_v_lst, and for each has a specific # labelled represented.
class Graph:
    def dfs_search(self, s, t, display: bool):
        self.reset_search()
        self.dfs_recur(s - 1, t - 1, display)
        return 

    # recursion function of DFS.
    # w: the starting vertex, t: the target vertex, prev_v_lst: previous list of vertex v.
    def dfs_recur(self, w, t, display: bool):
        # If founded then omit the searching.
        if (self.found == True): return
        self.visited_lst[w] = True
        # Display
        if (display):
            t_, w_ = t + 1, w + 1 
            print(f"Target is: {t_} and Current vertex is: {w_}")
        
        # If the vertex w is the target, then omit the searching.
        if (w == t):
            self.found = True
            return
        
        # Do DFS_recursion 
        for index in range(len(self.adjs_mat[w])):
            if (not self.visited_lst[index]) and (self.adjs_mat[w][index] == 1):
                self.prev_v_lst[index] = w
                self.dfs_recur(index, t, display)
```

- Breadth-first Search
This method search goes along with every children node, then along with every children's children nodes.
```python
class Graph:
    def bfs_search(self, s, t, display: bool):    
        # Sub function to create a new vertex.
        def create_vertex(s, adjs_mat, visited_lst):
            new_v = Vertex()
            new_v = new_v.setup_vertex("", s + 1)
            new_v.adjs = list(filter(
                lambda x: (adjs_mat[s][x] != 0 and visited_lst[x] == False), list(range(len(adjs_mat[s])))
            ))
            visited_lst[s] = True
            return new_v
        
        self.reset_search()
        if (s == t): return
        myvQ = vQueue()
        s_v = create_vertex(s - 1, self.adjs_mat, self.visited_lst)
        myvQ.add_vertex(s_v)
        
        while (myvQ.head != None):
            # Criteria, tail_number approaches target.
            if (myvQ.tail.number == t + 1): return
            
            # Adding all vertices in head's adjs into queue.
            for c_v_label in myvQ.head.adjs: 
                c_v = create_vertex(c_v_label, self.adjs_mat, self.visited_lst)
                myvQ.add_vertex(c_v)                 
            
            # Display
            if (display):
                t_, w_ = t + 1, myvQ.head.number 
                print(f"Target is: {t_} and Current vertex is: {w_}, ADJS: {myvQ.head.adjs}")
                myvQ.display_vQueue()
            
            # Remove head for looping.
            myvQ.remove_head()
        return
``` 
