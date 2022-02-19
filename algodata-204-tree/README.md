# Tree

A tree is a hierarchical Data Structure with one Root Node. It is used whenever you want to structure Data in a way that there is a clear Ownership or Association of the children nodes by a parent node. Trees are also used to structure objects hierarchically.

<img src="https://www.tutorialspoint.com/data_structures_algorithms/images/binary_tree.jpg">

## Use-Cases
- Hierarchies of GameObjects
- Class-Inheritance Graphs
- State-Hierarchies
- Game-Trees (Game-State Trees following Decisions)
- Basically anything with a hierarchical 1-to-n order
- More use cases are covered by more Specialized Trees

## Terms
- **Root** the node at the top of the tree, the only node without a parent
- **Level** the generation of a node, as in the distance of a node to the root
- **Parent** the node on the higher level that is connected with the node
- **Children** the nodes on the lower level that are connected with the node
- **Leaf** a node without children
- **Subtree** a node and all of its descendants form a sub-tree
- **Visiting** refers to checking the value of a node
- **Traversing** passing through nodes in a specific order
- **Path** sequence of Nodes along the edges of a Tree

## Implementation

Implementing a tree is quite simple. The fact that connections only go in one direction makes the implementation much easier:

### Minimalistic

```ts
define Tree<T>
   children : Tree<T>[]
   value : T
end define
```

That's it. Wait, why did I not create a class named Node? Well, because technically, every Node which has children is a sub-tree. That's why I chose to not really make a difference here between Trees, Nodes and Leaves.

<details>
  <summary>What Unity Class is implemented like this?</summary>

Then it would only be one Node and not really a Data Structure.

</details>
=> Unity's `Transform`-Classs is implemented like this.

### More Detailed

If it's important to you to have "empty" Trees, then you could differentiate the two classes:

```ts
define Tree<T>
   root : Node<T>
end define

define Node<T>
   Node<T>[] children
   value : T
end define
```

=> You could argue that `Tree<T>` is like Unity's `Scene` and `Node<T>` is Unity's `Transform`.

### Further information
Very often, you still want to have some more comfort functions to make working with Nodes a bit easier. For example, it's quite useful, if the Node also knows it sparent. This is of course redundant information (because the parent knows its child, if the child also knows its parent, then the "same" information is stored in two places. This is usually considered dangerous, as you might forget to update the information in one place and then you have out-of-sync information), but it makes working with Trees much easier.

Without this information, if you happen to have a reference to a child and want to know its parent, you'd have to search the whole Tree until you find a Node that has this node as a child.

=> Again, Unity's `Transform`-Class also has this feature.

# Binary Tree

Binary Trees are an extremely important specialization of the Tree Data Structure used to bring order to things. There is different variations of the Binary Tree used for different Use-Cases: Getting the Min-Value, Getting the Max-Value or efficiently Sorting and Finding all Items.

## Overview

A binary tree is a tree where each Node has a maximum of two Child Nodes (0, 1 or 2). The tree that we saw in the previous chapter was a Binary Tree:

<img src="https://www.tutorialspoint.com/data_structures_algorithms/images/binary_tree.jpg">

## Use Cases
- Serching and Sorting (more on that later)
- Prioritizing (more on that later)
- Binary Space Partitioning: Used in 3D Games and Apps to determine what needs to be rendered
- Binary Trie: Used in Routers for routing tables
- Huffmann Coding Tree: Used in Compression like jpeg and mp3
- Syntax Tree: Used by compilers and calculators
- Treap: Used in wireless networking and memory allocation
- Hash Trees: Used in torrents and blockchains
- GGM Trees: used in cryptograhy and pseudo RNG
- Yes-No Decision Trees
- Divide-and-Conquer Algorithms

[Source](https://stackoverflow.com/questions/2130416/what-are-the-applications-of-binary-trees/2200588)

## Why Binary?

For the same reason that computers use the Binary System and why Binary Search is so relevant: it's the most easy way of splitting things up.

What if every Node had max 0 Child Nodes?
<details>
  <summary>Answer</summary>

Then it would only be one Node and not really a Data Structure.

</details>

What if every Node had max 1 Child Nodes?
<details>
  <summary>Answer</summary>

Then it would be a LinkedList.

</details>

If each Node has two Child Nodes, then you can start creating very simple rules. If-Then-Left-Else-Right and thereby bring order into the Nodes.

It is also very clearly a good Data Structure to follow the Divide-and-conquer Algorithms.

## Implementation

### Simple

```ts
define Tree<T>
   left : Tree<T>
   right : Tree<T>
   value : T
end define
```

Very obvious implementation. Works well.

### Array

```
       A(0)    
     /   \
    B(1)  C(2)  
  /   \      \
 D(3)  E(4)   F(6) 
 ```

As you can see from above graphic, you can index all elements in a Binary Tree.\
And if you can index them, you can put them into an Array, right?

For a Node with index `n`
- the left child has index `n*2+1`
- the right child has index `n*2+2`

This allows to represent a Tree like this:

```ts
define Tree<T>
   nodes : T[]
   procedure GetLeftChild(n)
      return n*2+1
   end procedure
   procedure GetRightChild(n)
      return n*2+2
   end procedure
   procedure GetValue(n)
      return nodes at index n
   end procedure
end define
```

Pro: Great performance, less cache misses
Con: Harder to read and use

The con can be mitigated by using a struct (Value-Type) like this:

```ts
define Node<T>
   index: number
   tree : Tree<T>
   procedure GetLeftChild()
      return new Node<T>
         with index: tree.GetLeftChild(index)
         and tree: tree
   end procedure
   procedure GetValue()
      return tree.GetValue(index)
   end procedure
end define
```

# Binary Search Tree

<img src = "https://www.tutorialspoint.com/data_structures_algorithms/images/binary_search_tree.jpg">

A Binary Search Tree is a special kind of Binary Tree in which for each node, the following condition is true:
- all nodes on the left side of the node are smaller
- all nodes on the right side of the node ar larger

## Use Cases
- Sorted Lists
- Binary Search

## Basic Operations
- Insert
- Search
- Delete

### Search

Searching works very similar to Binary Search in a sorted collection. But instead of calculatung the next indes as the average between `min` and `max`, we just need to choose the `left` or `right` child on each node:

```
If root.data is equal to search.data
   return root
else
   while data not found

      If data is greater than node.data
         goto right subtree
      else
         goto left subtree
         
      If data found
         return node
   endwhile 
   
   return data not found
   
end if
```

<details>
  <summary>What's the Complexity?</summary>

O(log n) - for the same reasons as we learned in Binary Search.*

*under certain conditions

</details>

### Insert

Basically just a Search where the new Node is put where the searched node would have been expected:

```
If root is NULL 
   then create root node
return

If root exists then
   compare the data with node.data
   
   while until insertion position is located

      If data is greater than node.data
         goto right subtree
      else
         goto left subtree

   endwhile 
   
   insert data
	
end If  
```

<details>
  <summary>What's the Complexity?</summary>

O(log n) - again, we perform a binary search..*

*under certain conditions

</details>

### Delete
- Disconnect the Node
- Traverse all children of the Node (which is a Sub-Tree)
- Insert all Nodes back into the Tree

<details>
  <summary>What's the Complexity?</summary>

O(n) - In worst case, we delete the root node and have to construct a completely new tree.

</details>

### Delete Better

There is better methods for deleting a Node. They cover a few cases:

- If the Node has no children: Just remove it.
- If the Node has one child: Replace the node with the child node.
- Else: Search either for the Maximum of the left sub-tree (go left and then always right until you find a leaf) or the Minimum of the right sub-tree (go right and then always left) and replace the node with the leaf you just found.

## Traversal

Traversal describes the sequence in which you can iterate over all of nodes of a Tree (or a Sub-Tree). There is four common ones:

### Preorder Traversal
Also: Depth-First-Traversal

```
Traverse(node n)
   Visit(n)
   Traverse(n.left)
   Traverse(n.right)
```

Output: `7, 2, 0, 3, 4, 11, 8, 15`

Used to Clone a Tree, Prefix-Expression

### Inorder Traversal
Also: Symmetric Traversal

```
Traverse(node n)
   Traverse(n.left)
   Visit(n)
   Traverse(n.right)
```

Output: `0, 2, 3, 4, 7, 8, 11, 15`

Used to Run through Nodes in Correct Order

### Postorder Traversal

```
Traverse(node n)
   Traverse(n.left)
   Traverse(n.right)
   Visit(n)
```

Output: `0, 4, 3, 2, 8, 15, 11, 7`

Used to Run through Leaves First, Delete a Tree, Postfix-Expressions

### Levelorder Traversal

```
Traverse(node n)
   queue.push(n)
   while(queue is not empty)
      m = queue.pop()
      Visit(m)
      queue.push(m.left)
      queue.push(m.right)
   end while
```

Output: `7, 2, 11, 0, 3, 8, 15, 4`

Use Cases: Find shortest path between two nodes, Serialization of a Tree

### Reverse Inorder Traversal
Also: Symmetric Traversal

```
Traverse(node n)
   Traverse(n.right)
   Visit(n)
   Traverse(n.left)
```

Output: `15, 11, 8, 7, 4, 3, 2, 0`

Used to Run through Nodes in Reverse Order

### Other Reverses

- Reverse Preorder Traversal
- Reverse Postorder Traversal

# AVL Tree / Red-Black Tree

## Degenerate Tree

Problem: If the nodes come in in a sorted (or almost sorted) order:

<img src="https://2.bp.blogspot.com/-eLQTSxHwvLw/Vb2fI3P9hZI/AAAAAAAAHp0/VzpPjaENcGk/s320/ZnJvbT1jbmJsb2dzJnVybD1nWnBkbUw0RVdPNVVqTjNnak54SVROd0lUWWlCRE80UUROMlVtWnhBVE95Z3pZMUVXTHhRRE96VVRNMkl6TDVBek14QWpNdlF6TndnRE0xOHladnhtWXYwMmJqNXladnhtWTBsbWJqNXljbGRXWXRsMkx2b0RjMFJIYQ.jpg">

That does not look like much Divide and Conquer going on here, eh?

This kind of Tree is called a degenerate Tree and as mentioned, it can happen, if the data comes in a certain ordered fashion.

In above case, the difference between a Binary Search Tree and a Linked list is actually only that the BST has some overhead. Meh!

## Balanced Tree

What we want:

<img src="https://1.bp.blogspot.com/-4V266slC8f0/Vb2fiBQVXMI/AAAAAAAAHqo/04CsmIUVky0/s1600/images%2B%25281%2529.png">

<img width="400" src="https://i.kym-cdn.com/entries/icons/original/000/027/257/perfectly-balanced-as-all-things-should-be.jpg">


## Solution:

Balanced Tree Building Approach.

This is, what Red-Black Trees and AVL Trees do.

What they do is: They use Tree Rotations to rebalance a tree whenever it appears too imbalanced.

## Tree Rotation

<img src="https://www.tutorialspoint.com/data_structures_algorithms/images/avl_left_rotation.jpg">


## Balance

### AVL Tree

<img src="https://media.geeksforgeeks.org/wp-content/uploads/AVL-Insertion1-1.jpg">

Every node has the height of the left sub-tree and the height of the right sub-tree stored. The Balance Factor is the difference of both.\
If the absolute of the difference is greater than 1, it is unbalanced and Tree Rotations need to happen in the right places to bring it back to balance.

### Red Black Tree

<img src="https://algorithmtutor.com/images/RBTreeExample.png">

Red-Black Trees give the colors Red and Black (or simply 0/1 bit) to Nodes to specify, depending on:
- the root: black
- `null` child: black
- node with two black children: red

A red tree is balanced, if each path to the root has the same amount of black Nodes.

This can lead to a tree that's twice as long on the longest than on the shortest path (shortes: all black, longest: alternating red and black)

Then, these colors are used to determine strategies for Rebalancing on Insertion and Deletion. These are just algorithms that observe the state around the new node and then start rotating around.

## Further Reading

There is an endless amount of resources on this topic. Red-Black-Trees and AVL Trees are the state of the arts if it comes to Binary Search Trees, as an unbalanced Tree has catastrophic performance impact.

Of course, the balancing comes with an overhead, but that cost is willingly paid considering how a degenerate tree could mean a lookup of 1 million ms instead of 20ms (Remember?)

# Min-Heap

https://www.tutorialspoint.com/data_structures_algorithms/images/min_heap_example.jpg![image](https://user-images.githubusercontent.com/7360266/153140583-c63e48fb-34a8-41e4-9887-e714046accd8.png)


A Min-Heap is another kind of special Binary Tree. Here, each parent's Node should be larger than each Child's Node.

## Insert

When Inserting a New Node, put it at the next available Leaf Node and then continue comparing and, if necessary, swapping it with its current Parent until its in the right place:

```
Step 1 − Create a new node at the end of heap.
Step 2 − Assign new value to the node.
Step 3 − Compare the value of this child node with its parent.
Step 4 − If value of parent is less than child, then swap them.
Step 5 − Repeat step 3 & 4 until Heap property holds.
```

This automatically balances the tree.

## Remove

Remove should always happen at the Root of the Tree, to remove the Smallest Value. You will move the lowest node up and then start swapping its way down the tree again. Again, this keeps the Tree Balanced:

```
Step 1 − Remove root node.
Step 2 − Move the last element of last level to root.
Step 3 − Compare the value of this child node with its parent.
Step 4 − If value of parent is less than child, then swap them.
Step 5 − Repeat step 3 & 4 until Heap property holds.
```