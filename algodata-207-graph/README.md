# 1 Introduction

In this chapter, we will take our collections to the next dimension. Literally. We'll look at how to store collections of information that is more complex than a simple ordered List of objects.

Think for example of a digital representation of Stockholm's Metro Network:

<img width="711" alt="image" src="https://user-images.githubusercontent.com/7360266/152983698-22fb9246-311b-4470-bef2-49442e330057.png">

You could store the information in multiple Arrays, maybe:

```cs
Station[] T13 = new Station[] {
   Norsborg, Hallunda, Allby, ...
};
Station[] T14 = new Station[] {
   Fruängen, Västertorp, ...
};
```

But then, if you wanted to see, where you can go from T-Centralen, you'd have to run over all lists to check, whether your station is part of that line:

<details>
  <summary>Pseudo-Code</summary>

```
Procedure find_connected_stations
   lines ← array of all lines
   startStation ← station to search connections for
   result ← empty array of stations

   for each line in lines
      for each station in line
         if station = startStation
            add previousStation to result
            add nextStation to result
         end if
      end for
   end for

   return result
end procedure
```

</details>

That would be the equivalent of looking at multiple separate maps for each line and requiring to find your station in each of those:

<img src ="https://stockholmmetro.com/assets/images/green-line.jpg">

Pretty annoying, or? The original map is much more useful. As soon as you found your station, you can see all connections at one glance:

```cs

public class Connection {
   public Station target;
   public Line line;
   public Station source;
}

public class Station {
   public string name;
   public Connection[] connections;
}
```

We'll look at how we can define a structure like this in code and use it to structure a very useful type of collection for sorted data.

# 2 Graphs

<img src="https://www.tutorialspoint.com/data_structures_algorithms/images/graph_basics.jpg">

A graph is a set of two collections:
- A collection of Vertices
- A collection of Edges

In above graph, you can see:
- Vertices: a, b, c, d, e
- Edges: ab, ac, bd, cd, de

## 2.1 Use-Cases
Used almost everywhere in Computing, Science, Games, AI, ...
- Street Data
- Public Transport Data
- Connections on Linkedin
- Game States
- Menu States
- Dependency Graphs
- Pathfinding
- ...

## 2.2 Terms

- **Vertex** (also: Node) - Each point or state of a graph
- **Edge** (also: Connection) - A path between two vertices
- **Adjacency** (also: Neighbor) - Two vertices are adjacent if they are connected through an edge
- **Path** - A sequence of edges between two vertices.

## 2.3 Directed and Undirected

Graphs can be either directed or undirected.
In an undirected Graph, all Edges can be passed in both direction.
- e.g. you can go from a to b and also from b to a.

<img src="https://www.tutorialspoint.com/graph_theory/images/directed_graph.jpg">

In a directed graph, each connection has a clear from and to Edge. You can only navigate in one direction.
- e.g. a one-way street, which you can pass from North to South, but not from South to North.


## 2.4 Implementation:

Let's look at ways of implementing Graphs.

### 2.4.1 Adjacency Matrix

You could use a two-dimensional Array to store an Adjacency Matrix:

<img src="https://cdncontribute.geeksforgeeks.org/wp-content/uploads/undirectedgraph.png">

<img src="https://cdncontribute.geeksforgeeks.org/wp-content/uploads/adjacencymatrix.png">

```
class Graph<T>
   Node<T>[] nodes;
   bool[,] adjacency;

class Node<T>
   T value;
```

Instead of storing each connection as a class, you could look at the adjacency-matrix. e.g. to find out, whether node 2 and 3 are connected, you check whether `adjacency[2,3] == true`

- Pro: Very high Performance
- Con: Scales badly, especially for many Nodes and few Connections; Hard-To-Read Code; Bad performance when adding or removing Nodes

### 2.4.2 Object-Oriented, Two Piles

Graphs can be implemented in an object-oriented manner, separating Nodes and Connections:

```
class Graph<T>
   Node<T>[] nodes;
   Connection[] connections;

class Node<T>
   T value;

class Connection
   Node from;
   Node to;
```

- Pro: Very intuitive
- Con: A lot of heap allocations, hard to check for neighbors of node; Also need an efficient way of looking up connections, else you'll need LinearSearch when finding Connections from a Node

### 2.4.2 Object-Oriented, One Pile

Graphs can be implemented in an object-oriented manner, webbing it all together:

```
class Graph<T>
   Node<T>[] nodes;

class Node<T>
   T value;
   Node[] connections;
```

- Pro: Very intuitive, easy to use
- Con: You can't have a look at Connections without traversing the Nodes. Makes removing Nodes harder.

## 2.5 Add, Remove, Find

These are all very simple operations, because the Graph only consists of a simple collection of Nodes. Of course, when removing Nodes, you need to make sure that you don't have Nodes still having connections to this node (if using the Approach presented in 2.4.2)

## 2.6 Get Neighbors

This is the most common operation: We are at one Node and want to find out, what nodes are adjacent so we can evaluate, which one we want to evaluate next.

## 2.7 Traversal

Traversal in a Graph describes the process of traversing through all nodes beginning at a certain Node.

Since there is no Root-Node like in a Tree, the Node where we start traversing needs to picked.
- Usually, it's some node of interest, e.g. the Node that the User is located at
- But you may also pick the first node in the Node-List
- Or pick a Random Node

Traversal is used for several reasons:
- Either, you just want to traverse all nodes
- Or you want to find a certain Node from where you started

There are two major ways of traversing a Graph. But before we look at them, I'd like to mention, what they have in common and make an interesting observation.

## 2.7.1 Don't Go There Twice

Graph Traversal Algorithms visit every Node only once. To make sure that they don't get trapped in endless cycles. Also, they don't want to waste resources or return Nodes twice.

## 2.7.2 It's a Tree!

Look at the following two Traversal Algorithms that we will look at next:

<img src="https://www.tutorialspoint.com/data_structures_algorithms/images/breadth_first_traversal.jpg"><img src="https://www.tutorialspoint.com/data_structures_algorithms/images/depth_first_traversal.jpg">

- The continuous lines are connections between the Graph Nodes.
- The dotted lines are the lines showing, how the Graph was traversed.

Can you see, how the dotted lines shape a tree beginning at the Start-Node?

In other words: If you Traverse a Graph, you receive a Tree (Mind Blown!)

## 2.7.3 Depth-First-Traversal

<img src="https://www.tutorialspoint.com/data_structures_algorithms/images/depth_first_traversal.jpg">

With this method of Traversal, we always pick the first adjacent neighbor that's available and follow that strategy deeper and deeper until we hit a Node that we have already visited. (That's why it's called Depth-First: we just keep on walking until we hit a dead-end)

If we do, then we don't visit that node, but instead visit the next adjacent neighbor.

If we have no more neighbors to visit, we go back one step and continue with that node's neighbors again.

```
visited_nodes = [start_node]
path = [start_node]

while path not empty
   found_next_node = false
   for each neighbor in path.peek().getNeighbors()
      if(neighbor in visited_nodes)
         continue
      else
         visited_nodes.add(neighbor)
         path.push(neighbor)
         found_next_node = true
         break
      end if
   end for
   if(not found_next_node)
      path.pop()
   end if
end while
```

- This is basically the same as Preorder-Traversal in a Binary Tree

Applications: Detecting Cycles in a Graph (Circular Dependencies), Some Search Algorithms, Topological Ordering, Solving Puzzles with only one Solution (With a variation, it can also find all Solutions)

## 2.7.4 Breadth-First-Traversal

<img src="https://www.tutorialspoint.com/data_structures_algorithms/images/breadth_first_traversal.jpg">


With this method, we first look at all neighbors of the current node and then all (unvisited) neighbours of the previously visited neighbors and so on. We basically go Layer by Layer from inside to outside farther and farther away. First, we visit all Nodes with a Distance of 1. Then, all Nodes with a Distance of 2, etc.

```
visited_nodes = [start_node]
path = [start_node]

while path not empty
   for each neighbor in path.dequeue().getNeighbors()
      if(neighbor in visited_nodes)
         continue
      else
         visited_nodes.add(neighbor)
         path.enqueue(neighbor)
      end if
   end for
end while
```

If you think of the Graph as a Tree, we're going Level by Level. Which  is also, why:
- This is basically the same as Levelorder-Traversal in a Binary Tree

Applications: Shortest Path and Minimum Spanning Tree (Pathfinding), Torrent (P2P Networks), Google Crawler, Garbage Collection, GPS, Closest Connection on Linkedin, and many more

So, quite obviously, Breadth-First-Traversal seems to be the more important one.
