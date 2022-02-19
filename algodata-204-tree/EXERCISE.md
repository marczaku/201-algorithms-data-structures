# Mandatory
Implement a simple Binary Search Tree `TurboBinarySearchTree` or `TurboBinarySearchTree<T> where T:IComparable<T>` without Balancing Algorithms. It should have these methods:

- `Insert`, `Search`, `Delete`, 
- `GetEnumerator`: returns all items in order, from min to max
- `GetInOrder`: same as GetEnumerator 
- `GetInReverseOrder`: returns all items in reverse order, from max to min

# Bonus (Intermediate)

- `Clone`: creates a clone of the tree
- `Delete`: deletes the tree, but node by node (set the value to 0, then set left to `null` and right to `null`)

# Bonus (Hard)
- Implement the Tree using an `Array` to store all values instead of `Node`-classes.

# Bonus (Insane)

Try yourself at a Balancing Binary Search Tree implementation
