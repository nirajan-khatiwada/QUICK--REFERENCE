Trees consist of vertices (nodes) and edges that connect them. Unlike the linear data structures that we have studied so far, trees are hierarchical. They are similar to Graphs, except that a cycle cannot exist in a Tree - they are acyclic. In other words, there is always exactly one path between any two nodes.
- Root Node: The topmost node in a tree.
- Child Node: A node that has a parent node.
- Parent Node: A node that has one or more child nodes.
- Sibling Nodes: Nodes that share the same parent node.
- Leaf Node: A node that does not have any child nodes.
- Ancestor Node: A node that is a predecessor of a given node.

Example:
```
        A
      /   \
     B     C
    / \   / \
   D   E F   G
```
- A is the root node.
- B and C are child nodes of A.
- D and E are sibling nodes (both children of B).
- B is the parent node of D and E.
- D, E, F, and G are leaf nodes.


***Other Terms:***
- Subtree: A subtree is a portion of a tree that includes a node and all its descendants.
- Degree of a Node: The number of children a node has.
- Length of a Path: The number of edges in the path from one node to another.
- Depth of a Node: The number of edges from the root node to the given node.
- Level of a Node : (Depth of Node)+1
- Height of a node : The number of edges on the longest path from the node to a leaf node.
- Height of Tree : Height of root node
Example:
``` 
       A 
     /   \
    B     C
  /  \   /  \
 D    E F    G
      /
      H 
```
 Here 
Degree of B = 2 (B has two children D and E)
Depth of H = 3 (A->B->E->H)
Level of H = 4 (Depth + 1)
Height of B = 2 (B->E->H)


Types of Trees:
- Binary Tree: Each node has at most two children.
- Binary Search Tree (BST): A binary tree where the left child contains values less than the parent node, and the right child contains values greater than the parent node.
- AVL Tree: A self-balancing binary search tree where the difference in heights between the left and right subtrees cannot be more than one for all nodes.
- Red-Black Tree: A self-balancing binary search tree where each node has an extra bit for denoting the color of the node, either red or black.
- 2-3 Tree: A balanced search tree where every node can have either two or three children and one or two data elements.
- N-ary Tree: A tree where each node can have at most N children.



### Binary Tree:
A binary tree is a tree in which each node has between 0-2 children. They’re called the left and right children of the node. The figure below shows what a Binary Tree looks like.
Example:
```
        1
      /   \
     2     3
    / \   / \
   4   5 6   7
```

Types of Binary Trees:
- A complete binary tree is a binary tree in which all the levels of the tree are fully filled, except for perhaps the last level which can be filled from left to right.
![Complete Binary Tree](/images/COMPLETE.png)
- A full binary tree is a binary tree in which every node has either 0 or 2 children. In other words, no node in a full binary tree has only one child.
Example:
```    
        1
      /   \
     2     3
    / \   / \
   4   5 6   7
   Full Binary Tree

        0
        |
        1
       /  \
      2    3
    Not a Full Binary Tree
```


- Perfect binary tree is a binary tree in which all the internal nodes have exactly two children and all leaf nodes are at the same level.
Example:
```
        1
      /   \
     2     3
    / \   / \
   4   5 6   
   Not a Perfect Binary Tree
    
        1
      /   \
     2     3
    / \   / \
   4   5 6   7
    Perfect Binary Tree
```



### Binary Search Tree (BST):
Binary Search Tree is a special type of binary tree in which each node has atmost two children and the left child contains values less than the parent node, and the right child contains values greater than the parent node.
Example:
```
        8
      /   \
     3     10
    / \      \
   1   6      14
      / \     /
     4   7   13
``` 

***Implementation of BST in Python:***

Node class:
To implement a BST, the first thing you’d need is a node. A node should have a value, a left child, a right child, and a parent. This node can be implemented as a Python class and here is the code.

```python
class Node:
    def __init__(self, key):
        self.left = None
        self.right = None
        self.val = key
        self.parent = None
```


Binary Search Tree class:
ou can then choose to create a wrapper class for the tree itself; this can sometimes make your code cleaner and easier to read, but not always. However, this is a programming convention so let’s create a tree class:
```python
class BST:
    def __init__(self,val):
        self.root = Node(val) 

```

Using it:
```
bst = BST(8)
print(bst.root.val)  # Output: 8
```


### Insertion in BST:
To insert a new value into a BST, you start at the root and compare the value to be inserted with the current node's value. If the value is less than the current node's value, you move to the left child; if it's greater, you move to the right child. You continue this process until you find an appropriate null position where the new node can be inserted.

This can be implement using two approached:
1. Iterative Approach:
In the iterative approach, you use a loop to traverse the tree until you find the correct position for the new node. Here’s how you can implement it in Python:
```python
 def insert_x(self, x):
        node = Node(x)
        current = self.root
        parent = None
        
        while current is not None:
            parent = current
            if x < current.val:
                current = current.left
            elif x > current.val:
                current = current.right
            else:
                return  # duplicate value
        
        if x < parent.val:
            parent.left = node
        else:
            parent.right = node
        node.parent = parent
  ```

### Recursive Approach:
```
def _insert(self,curr,x):
        if curr.val < x:
            if curr.right is None:
                node = Node(x)
                curr.right = node
                node.parent = curr
                return 
            else:
                _insert(self,curr.right,x)
        if curr.val > c:
            if curr.left id None:
                node = Node(x)
                curr.left = node
                node.parent = curr 
                return
            else:
                _insert(self,curr.left,x)
                
```

### Deletion in BST:
To delete a node from a BST, you need to consider three cases:
1. The node to be deleted is a leaf node (no children).
In this case, you can simply remove the node from the tree by setting the corresponding child pointer of its parent to null.
2. The node to be deleted has one child.
In this case, you can replace the node with its child. You need to update the parent pointer of the child to point to the parent of the node being deleted, and then update the corresponding child pointer of the parent to point to the child.
3. The node to be deleted has two children.
In this case, you need to find the in-order successor (the smallest node in the right subtree) or the in-order predecessor (the largest node in the left subtree) of the node to be deleted. You can then replace the value of the node to be deleted with the value of the in-order successor or predecessor, and then delete the in-order successor or predecessor node using one of the first two cases.