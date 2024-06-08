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

### 2.2.1 Depth-first Search 深度优先搜索
This searching method is searching along with paths one by one, from the parent node digging to one particular children node and then children's children. Hence could write a recursion by repeating the same procession for every children digging out the node matches the target.
```python
# DFS - Depth First Search
# Principle: Visited from a starting vertex, and then visit all adj verteices connected to it.
# Time Complexity: O(#Edges), Space Complexity: O(#vertex)
# -1 represented none for prev_v_lst, and for each has a specific # labelled represented.
class Graph:
    # Function to reset the found, visited list and prev vertex list for processing DFS and BFS.    
    def reset_search(self):
        self.found = False
        self.visited_lst = [(False) for p in range(self.vertex_num)]
        self.prev_v_lst = [(-1) for p in range(self.vertex_num)]

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

### 2.2.2 Breadth-first Search 广度优先搜索
This method search goes along with every node in the adjacency set but not in the visited set, defined as the children set, then go along with all children sets of these children nodes. A simple approach is to build a queue to store and establish the order of nodes:

Queue is a data structure where connects all nodes by a next relationship, for every new node added, would add such node at the tail of the queue, while remove nodes from the head. Queue is a specific case of linkedlist, you could definitely design other linkedlisk like stack (storing from head and removing from head), but in this case by using queue we could go through nodes until approaches target. 
```python
class vQueue:
    def __init__(self):
        self.head = None
        self.tail = None
    
    def add_vertex(self, new_v: Vertex):
        if (self.head == None): self.head = new_v
        try: self.tail.next = new_v
        except: pass
        self.tail = new_v

    def remove_head(self):
        if (self.head != None):
            if (self.head.next != None): 
                try: self.head = self.head.next
                except: pass
            else: 
                self.head = None
        else: return
```

Then by using queue, we add all nodes and then add their children into queue, and process (or says check) one by one then remove from the head. 
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

### 2.2.3 Dijkstra Method
The time complexity is $n^{2}$, is to find the shortest path from the starting node to every other nodes in a weighted undirected graph. To approach this we set up a set as *unvisited_lst*, include the whole vertices initially and set up a value names the *curr_smallNode* for the current node with the smallest distance to. For nodes not connected by any weighted edges, we say the distance to be infinity and for those connected we say distance to be the weights sum, stored in the distance_map.

For initial setting, we set the *curr_smallNode* to be s for the distance from the start node s to s is naturally 0, while all other vertices are infinite in distance, then repeat the following steps:
 
1. Finding the next curr_smallNode by $c_{k+1} = argmin_{x \in S_{k}} Dist_{k}(x)$, for intial step $c_{0} = s$. 
2. Remove the curr_smallNode from the set $S_{k+1} = S_{k} / c_{k+1}$.
3. Caculate the distance of all other nodes $i \in S_{k}$ to the curr_smallNode $c_{k}$, $Dist_{k+1}[i] = w_{c_{k}i}$.

Notes that Dijkstra could be mathmatically shown that is an approach finding the minumum path towards each other nodes in an undirected weighted graph.
```python
    def dijkstra_pathfind(self, s, display):    
        self.reset_search()
        unvisited_lst = list(range(1, self.vertex_num + 1))
        distance_dict = dict(zip(
            unvisited_lst, 
            [np.inf for p in list(range(len(unvisited_lst)))] 
        ))
        
        # Looping for nodes.
        curr_smallNode = s
        distance_dict[s] = 0
        while (len(unvisited_lst) > 0):
            # Step - 1
            if (s not in unvisited_lst):
                valueLst = list(distance_dict[x] for x in unvisited_lst)
                for currNode_1 in unvisited_lst: 
                    if (distance_dict[currNode_1] == min(valueLst)): 
                        curr_smallNode = currNode_1
                        break
            else: curr_smallNode = s
            
            # Step - 2, 3
            unvisited_lst.remove(curr_smallNode)
            for currNode_2 in unvisited_lst:
                curr_dist = np.inf
                if (self.adjs_mat[curr_smallNode - 1][currNode_2 - 1] > 0):
                    curr_dist = self.adjs_mat[curr_smallNode - 1][currNode_2 - 1] + distance_dict[curr_smallNode]
                if (distance_dict[currNode_2] > curr_dist): distance_dict[currNode_2] = curr_dist
            if (display): print(f"Distance Dict: {distance_dict}, Unvisited Set: {unvisited_lst}")
        return distance_dict
```
