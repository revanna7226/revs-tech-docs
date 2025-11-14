# Binary Tree

## 1) What is a Binary Tree?

A **binary tree** is a hierarchical data structure where each node has at most **two children**: commonly named `left` and `right`.
It’s used in many algorithms: expression trees, heaps, BSTs, segment trees, tries (variant), etc.

Important properties:

- Height (or depth): longest root→leaf path length (number of edges or nodes; be explicit in your code).
- Perfect, Full, Complete — common special shapes:

  - **Full** (every node has 0 or 2 children).
  - **Complete** (all levels full except possibly last, filled left to right).
  - **Perfect** (all internal nodes have two children and all leaves are same depth).

---

## 2) Basic Node & Tree classes

```java
public class BinaryTree {

    Node root;

    static class Node {
        int val;

        Node left, right;

        Node(int v) {
            val = v;
            left = right = null;
        }
    }

    public BinaryTree() { root = null; }
}
```

---

## 3) Traversals

### Depth First Search (DFS) traversals (Preorder, Inorder, Postorder)

```java
// Preorder: root, left, right
void preorder(Node node) {
    if (node == null) return;
    System.out.print(node.val + " ");
    preorder(node.left);
    preorder(node.right);
}

// Inorder: left, root, right
void inorder(Node node) {
    if (node == null) return;
    inorder(node.left);
    System.out.print(node.val + " ");
    inorder(node.right);
}

// Postorder: left, right, root
void postorder(Node node) {
    if (node == null) return;
    postorder(node.left);
    postorder(node.right);
    System.out.print(node.val + " ");
}
```

### Iterative inorder (using stack)

```java
import java.util.Stack;

void inorderIterative(Node root) {
    Stack<Node> st = new Stack<>();
    Node cur = root;
    while (cur != null || !st.isEmpty()) {
        while (cur != null) { st.push(cur); cur = cur.left; }
        cur = st.pop();
        System.out.print(cur.val + " ");
        cur = cur.right;
    }
}
```

### Breadth First Search (BFS) - Level-order

```java
import java.util.Queue;
import java.util.LinkedList;

public List<Integer> BFS() {
    if(root == null) return List.of();
    Node currentNode = root;
    Queue<Node> queue = new LinkedList<>();
    ArrayList<Integer> result = new ArrayList<>();

    queue.add(currentNode);

    while(!queue.isEmpty()) {
        currentNode = queue.remove();
        result.add(currentNode.val);

        if(currentNode.left != null) {
            queue.add(currentNode.left);
        }
        if(currentNode.right != null) {
            queue.add(currentNode.right);
        }
    }

    return result;

}
```

---

## 4) Common operations & utilities

### Height (depth) of tree (recursive)

```java
int height(Node node) {
    if (node == null) return 0; // number of nodes on longest path
    return 1 + Math.max(height(node.left), height(node.right));
}
```

### Count nodes / count leaves (recursive)

```java
int countNodes(Node node) {
    if (node == null) return 0;
    return 1 + countNodes(node.left) + countNodes(node.right);
}

int countLeaves(Node node) {
    if (node == null) return 0;
    if (node.left == null && node.right == null) return 1;
    return countLeaves(node.left) + countLeaves(node.right);
}
```

---

## 5) Binary **Search** Tree (BST)

A BST is a binary tree with ordering property: for any node `n`, all keys in `n.left` < `n.val` and all keys in `n.right` > `n.val` (commonly assuming no duplicates, or you define a tie rule).

### BST node & operations: insert, search, delete

```java
public class BST {
    Node root;

    static class Node {
        int val;
        Node left, right;
        Node(int v) { val = v; }
    }


    // Insert (iterative)
    public void insert(int val) {
        Node newNode = new Node(val);
        if (root == null) {
            root = newNode;
            return;
        }
        Node current = root;
        Node parent = null;
        while (current != null) {
            parent = current;
            current = (val < current.val) ? current.left : current.right;
        }
        if (val < parent.val) {
            parent.left = newNode
        } else {
            parent.right = newNode;
        }
    }

    // Search (iterative)
    public Node search(int val) {
        Node current = root;
        while (current != null) {
            if (val == current.val) return current;
            current = (val < current.val) ? current.left : current.right;
        }
        return null;
    }

    private Node rSearch(Node node, int val) {
        if (node == null || node.val == val) return node;
        return (val < node.val) ? rSearch(node.left, val) : rSearch(node.right, val);
    }

    public Node rSearch(int val) {
        return rSearch(root, val);
    }

    private Node rInsert(Node node, int val) {
        if (node == null) return new Node(val);
        if (value == node.value) return node;
        if (val < node.val) {
            node.left = rInsert(node.left, val);
        } else {
            node.right = rInsert(node.right, val);
        }
        return node;
    }

    public void rInsert(int val) {
        root = rInsert(root, val);
    }

    // Delete (recursive) - handles 3 cases
    public Node delete(Node node, int key) {
        if (node == null) return null;
        if (key < node.val) {
            node.left = delete(node.left, key);
        } else if (key > node.val) {
            node.right = delete(node.right, key);
        } else {
            // found node to delete
            if (node.left == null && node.right == null) {
                return null;
            } else if (node.left == null) {
                return node.right;
            } else if (node.right == null) {
                return node.left;
            } else {
                // two children: replace with inorder successor (smallest in right subtree)
                Node succ = minValue(node.right);
                node.val = succ.val;
                node.right = delete(node.right, succ.val);
            }
        }
        return node;
    }

    private Node minValue(Node node) {
        Node cur = node;
        while (cur.left != null) {
            cur = cur.left;
        }
        return cur;
    }
}
```

**Usage**

```java
BST tree = new BST();
tree.insert(50);
tree.insert(30);
tree.insert(70);
tree.root = tree.delete(tree.root, 50); // delete root
```

---

## 6) Interview Questions/LeetCode Questions

### 1. BST: Convert Sorted Array to Balanced BST

```java
    private Node sortedArrayToBST(int[] nums, int left, int right) {
        if(left > right) return null;

        int mid = left + (right - left)/2;
        Node parentNode = new Node(nums[mid]);

        parentNode.left = sortedArrayToBST(nums, left, mid-1);
        parentNode.right = sortedArrayToBST(nums, mid+1, right);

        return parentNode;
    }

    public Node sortedArrayToBST(int[] nums) {
        return sortedArrayToBST(nums, 0, nums.length-1);
    }

```

### 2. BST: Invert Binary Tree

```java
    private Node invertTree(Node node) {
        if(node == null) {
            return node;
        }

        Node leftTree = node.left;

        node.left = invertTree(node.right);
        node.right = invertTree(leftTree);

        return node;
    }

    public Node invertTree() {
        return invertTree(root);
    }

```

---

## 6) Time & Space Complexity (Big-O)

| Operation             | Binary Tree (general) |    BST (average) | BST (worst — skewed) |
| --------------------- | --------------------: | ---------------: | -------------------: |
| Search                |                  O(n) | O(log n) average |                 O(n) |
| Insert                |                  O(n) |     O(log n) avg |                 O(n) |
| Delete                |                  O(n) |     O(log n) avg |                 O(n) |
| Traversal (all nodes) |                  O(n) |             O(n) |                 O(n) |
| Height                |                  O(n) |     O(log n) avg |                 O(n) |

Space: recursion depth = O(h) where `h` is tree height; auxiliary structures for level-order O(n) worst-case.

---

## 7) Full runnable example (combines many pieces)

```java
import java.util.*;

public class BinaryTreeDemo {
    static class Node { int val; Node left, right; Node(int v){val=v;} }

    Node root;

    // build a sample binary tree (not BST) by level insertion
    void buildSample() {
        // creates:    1
        //           /   \
        //          2     3
        //         / \   /
        //        4  5  6
        root = new Node(1);
        root.left = new Node(2);
        root.right = new Node(3);
        root.left.left = new Node(4);
        root.left.right = new Node(5);
        root.right.left = new Node(6);
    }

    void preorder(Node n){ if(n==null) return; System.out.print(n.val+" "); preorder(n.left); preorder(n.right);}
    void inorder(Node n){ if(n==null) return; inorder(n.left); System.out.print(n.val+" "); inorder(n.right);}
    void postorder(Node n){ if(n==null) return; postorder(n.left); postorder(n.right); System.out.print(n.val+" ");}

    void levelOrder(Node root){
        if(root==null) return;
        Queue<Node> q = new LinkedList<>();
        q.offer(root);
        while(!q.isEmpty()){
            Node cur = q.poll();
            System.out.print(cur.val+" ");
            if(cur.left!=null) q.offer(cur.left);
            if(cur.right!=null) q.offer(cur.right);
        }
    }

    int height(Node n){ if(n==null) return 0; return 1+Math.max(height(n.left), height(n.right)); }

    public static void main(String[] args){
        BinaryTreeDemo t = new BinaryTreeDemo();
        t.buildSample();
        System.out.print("Preorder: "); t.preorder(t.root); System.out.println();
        System.out.print("Inorder: "); t.inorder(t.root); System.out.println();
        System.out.print("Postorder: "); t.postorder(t.root); System.out.println();
        System.out.print("LevelOrder: "); t.levelOrder(t.root); System.out.println();
        System.out.println("Height: " + t.height(t.root));
    }
}
```

Expected output:

```
Preorder: 1 2 4 5 3 6
Inorder: 4 2 5 1 6 3
Postorder: 4 5 2 6 3 1
LevelOrder: 1 2 3 4 5 6
Height: 3
```

---

## 8) Tips & common pitfalls

- Distinguish **binary tree** vs **binary search tree** — operations differ.
- Recursive solutions are concise but watch recursion depth for skewed trees — convert to iterative if stack overflow risk.
- When deleting in BST, remember to handle three cases (leaf, one child, two children). Using **inorder successor** or **predecessor** is common.
- For balanced trees (AVL, Red-Black), operations cost guarantee O(log n) — consider these for production.
- Use `ArrayDeque` or `LinkedList` for queue operations — `LinkedList` implements `Queue`, but `ArrayDeque` is usually faster.

---

## 9) Practice problems

- Serialize / deserialize a binary tree.
- Convert binary tree to its mirror.
- Check if two binary trees are identical.
- Find lowest common ancestor (LCA).
- Validate if a tree is BST.
- Print level order in zig-zag (spiral order).
