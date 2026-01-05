What is a Graph?
This lesson is a brief introduction to the graph data structure, its types, and the standard terminologies used to describe it.

Introduction
When we talk about graphs, what comes to mind are the conventional graphs used to visualize data. In computer science, the term “graph” has a completely different meaning. It is a data structure used to store and manipulate data.

The graph data structure plays a fundamental role in several applications such as GPS, neural networks, peer-to-peer networks, search engine crawlers, garbage collection (Python), and even social networking websites.

This section will explore its functionality and power. We will also look at how they are used to solve a diverse range of problems.

Now, let’s see what a graph really is.

Graph Structure
A graph is a set of nodes that are connected to each other in the form of a network. First of all, we’ll define the two basic components of a graph.

Vertex
A vertex is the most essential part of a graph. A collection of vertices forms a graph. In that sense, vertices are similar to linked list nodes.

Edge
An edge is the link between two vertices. It can be uni-directional or bi-directional depending on your graph. An edge can also have a cost associated with it (will be discussed in detail later).

Here is a visual representation of a graph:

Vertex
Edge
a
b
c
Did you find this helpful?
Graph, vertices and edges
There are several terms used to describe the properties of graphs.

Graph Terminologies
Degree of a Vertex: The total number of edges incident on a vertex. There are two types of degrees:

In-Degree: The total number of incoming edges of a vertex.

Out-Degree: The total number of outgoing edges of a vertex.

Parallel Edges: Two undirected edges are parallel if they have the same end vertices. Two directed edges are parallel if they have the same starting and ending vertices.

Self Loop: This occurs when an edge starts and ends on the same vertex.

Adjacency: Two vertices are said to be adjacent if there is an edge connecting them directly.

In the illustration above, the in-degree of both a and b is 1. The same goes for the out-degree of the vertices. The in-degree and out-degree for c are 2 as it contains a self-loop

Types of Graphs
This lesson showcases the two main categories of graphs.

Types of Graphs
There are two common types of graphs:

Undirected
Directed
Undirected Graph
In an undirected graph, the edges are bi-directional. For e.g., an ordered pair (2, 3) shows that there exists an edge between vertex 2 and 3 without any specific direction. You can go from vertex 2 to 3 or from 3 to 2.

Let’s calculate the maximum number of edges for an undirected graph. We are denoting an edge between vertex a and b as (a, b). So, the maximum possible edges of a graph with n vertices will be all possible pairs of vertices of that graph, assuming that there are no self-loops.

If a graph has n vertices, then there are C (n,2) possible pairs of vertices according to Combinatorics. Solving C (n,2) by binomial coefficients gives us 
n
(
n
−
1
)
2
2
n(n−1)
​
 
. Hence, there are 
n
(
n
−
1
)
2
2
n(n−1)
​
 
 maximum possible edges in an undirected graph.

You can see an example of an undirected graph below:

svg viewer

Directed Graph
In a directed graph, the edges are unidirectional. For a pair (2, 3), there exists an edge from vertex 2 towards vertex 3 and the only way to traverse is to go from 2 to 3, not the other way around.

This changes the maximum number of edges that can exist in the graph. For a directed graph with n vertices, the minimum number of edges that can connect a vertex with every other vertex is n-1. This excludes self-loops.

If you have n vertices, then all the possible edges become n*(n-1).

Here’s an example of a directed graph:

svg viewer

The choice between the two types depends on the nature of your program.




Representation of Graphs
Two approaches to represent a graph will be covered in this lesson.

Ways to Represent a Graph
The two most common ways to represent a graph are:

Adjacency Matrix
Adjacency List

Adjacency Matrix
The adjacency matrix is a two-dimensional matrix where each cell can contain a 0 or 1. If a cell contains 1, there exists an edge between the corresponding vertices e.g., 
M
a
t
r
i
x
[
0
]
[
1
]
=
1
Matrix[0][1]=1
 shows that an edge exists between vertex 0 and 1. The row and column headings represent the vertices.

svg viewer

In the image above, there is a directed graph that has an edge going from vertex 0 to vertex 1, so there is a 1 at 
M
a
t
r
i
x
[
0
]
[
1
]
Matrix[0][1]
 in the adjacency matrix. In the case of the undirected graph, we would have 
M
a
t
r
i
x
[
1
]
[
0
]
=
1
Matrix[1][0]=1
 as well since the edge is bidirectional.

For a directed graph, the usual convention is to think of the rows as sources and the columns as destinations.


Adjacency List
An array of linked lists is used to store all the edges in the graph. The size of the array is equal to the number of vertices in the graph. Each index of the array contains a vertex. This vertex points to a linked list that contains all the vertices connected to this one.

0
1
2
3
Linked Lists
3
None
2
3
None
1
3
None
0
1
2
None
List
0
1
2
3
3
1
2
None
2
0
3
None
1
0
3
None
0
1
2
None
Linked Lists
List
Directed Graph
Undirected Graph
Did you find this helpful?
As you can see in the diagram above, all the vertices that connect directly to a vertex are appended in the corresponding linked list.

If a new vertex is added to the graph, it is simply added to the array as well.

Note: In an undirected graph an edge is represented with both adjacent nodes having their linked list populated with the corresponding nodes.



implementation of graph