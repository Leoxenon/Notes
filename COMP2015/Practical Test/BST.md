## **Binary Search Tree**
适合所有操作
查找逊于哈希表，查找最值和排序逊于堆
- **Binary Search Tree**
	- add()
		- O(logN)
	- get(int i) / search(K key) / contains(K key, V value)
		- O(logN)
	- getMin / getMax
		- O(logN)
	- remove()  
		- Optional
		- O(logN)
	- print
		- historicalHighest()
			- Optional
			- O(1)
		- topK
			- O(K)
		- traversal
			- O(N)
```
public class BST<K extends Comparable<K>, V> {  
    class Node<K, V> {  
        K key;  
        V value;  
        Node<K, V>[] children = (Node<K, V>[]) new Node[2];  
    }  
  
    private final int LEFT = 0, RIGHT = 1;  
    Node<K, V> root;  
    int count;  

	// Optional
    int index; // finding topK  
    Node<K, V> historicalHighest;  
  
    // iteration  
    // O(logN)    
    public void add(K key, V value) {  
        Node<K, V> newNode = new Node<>();  
        newNode.key = key;  
        newNode.value = value;  
  
        if (root == null) {  
            root = newNode;  
        } else {  
            Node<K, V> p = null;  
            Node<K, V> t = root;  
            int LorR = -1;  
            while (t != null) {  
                p = t;  
                if (t.key.compareTo(key) < 0) {  
                    LorR = LEFT;  
                } else if (t.key.compareTo(key) > 0) {  
                    LorR = RIGHT;  
                } else {  
                    // do something for duplicated value  
                    return;  
                }  
                t = t.children[LorR];  
            }  
            p.children[LorR] = newNode;  
        }  
        count++;  
    }  
  
    //recursion  
    // O(logN)    
    public void insert(K key, V value) {  
        root = insert(root, key, value);  
        count++;  
    }  
    private Node<K, V> insert(Node<K, V> node, K key, V value) {  
        if (node == null) {  
            Node<K, V> newNode = new Node<>();  
            newNode.key = key;  
            newNode.value = value;  
            /**  
             * if (historicalHighest == null || a.compareTo(historicalHighest.a) > 0)                historicalHighest = newNode;  */            
            return newNode;  
        }  
        int comp = node.key.compareTo(key);  
  
        if (comp < 0)  
            node.children[LEFT] = insert(node.children[LEFT], key, value);  
        else if (comp > 0)  
            node.children[RIGHT] = insert(node.children[RIGHT], key, value);  
        else {  
            // do something for duplicated value  
            node.value = value;  
        }  
        return node;  
    }  
  
    // iteration  
    // O(logN)    
    public V search(K key) {  
        Node<K, V> t = root;  
        int LorR = -1;  
        while (t != null) {  
            if (t.key.compareTo(key) < 0) {  
                LorR = LEFT;  
            } else if (t.key.compareTo(key) > 0) {  
                LorR = RIGHT;  
            } else {  
                // do something for duplicated value  
                return t.value;  
            }  
            t = t.children[LorR];  
        }  
        return null;  
    }  
  
    // recursion  
    // O(logN)
    public boolean contains(K key, V value) {  
        return get(root, key, value) != null;  
    }  
    // O(logN)  
    private Node<K,V> get(Node<K,V> node, K key, V value) {  
        if (node == null) return null;  
        int comp = key.compareTo(node.key);  
        Node<K,V> target;  
        if (comp < 0)  
            target = get(node.children[LEFT], key, value);  
        else if (comp > 0)  
            target = get(node.children[RIGHT], key, value);  
        else {  
            if (value == node.value)  
                return node;  
            else  
                target = get(node.children[RIGHT], key, value);  
        }  
        return target;  
    }  
  
  
    // O(N)  
    public void printAll() {  
        inorder(root);  
        System.out.println();  
    }  
    private void inorder(Node<K, V> node) {  
        if (node == null) return;  
        inorder(node.children[RIGHT]);  
        System.out.print(node.key + "(" + node.value + ") ");  
        inorder(node.children[LEFT]);  
    }  
```

```
    /** Optional **/ 
    // O(logN)    
    public void remove(K key, V value) {  
        root = remove(root, key, value);  
    }  
    private Node<K, V> remove(Node<K, V> node, K key, V value) {  
        if (node == null) return null;  
        int comp = key.compareTo(node.key); 
         
         // key < node.key
        if (comp < 0)                  
            node.children[LEFT] = remove(node.children[LEFT], key, value);  
        
        // key > node.key or (key == node.key but value != node.value)  
        else if (comp > 0 || value != node.value)  
            node.children[RIGHT] = remove(node.children[RIGHT], key, value);  
            
        // key == node.key and value == node.value  
        else {   
            if (node.children[RIGHT] == null) {  
                count--;  
                return node.children[LEFT];  
            } else if (node.children[LEFT] == null) {  
                count--;  
                return node.children[RIGHT];  
            } else {  
                Node<K, V> minNode = getMinNode(node.children[RIGHT]);  
                node.key = minNode.key;  
                node.value = minNode.value;  
                node.children[RIGHT] = 
	                remove(node.children[RIGHT], minNode.key, minNode.value);  
            }  
        }  
        return node;  
    }  
    private Node<K, V> getMinNode(Node<K, V> node) {  
        if (node == null) return null;  
        if (node.children[LEFT] == null) return node;  
        return getMinNode(node.children[LEFT]);  
    }  
    
    // O(K)  
    public void top(int k) {  
        index = 0;  
        inorder(root, k);  
        System.out.println();  
    }  
    private void inorder(Node<K, V> node, int k) {  
        if (node == null) return;  
        inorder(node.children[RIGHT], k);  
        if (index < k) {  
            index++;  
            System.out.printf("%s:%s ", node.key, node.value);  
        }else  
            return;  
        inorder(node.children[LEFT], k);  
    }  
    
    public String getHistoricalHighest() {  
        return String.format("%s:%s", historicalHighest.key, historicalHighest.value);  
    }  
}
```

### **Abstract Tree**
```
abstract public class AbsBST <A extends Comparable<A>, B extends Comparable<B>>{  
  
    class Node<A, B>{  
        A a;  
        B b;  
        Node<A, B>[] children = (Node<A, B>[]) new Node[2];  
    }  
  
    private final int LEFT = 0, RIGHT = 1;  
    Node root;  
    int count;  
  
    abstract int compare(Node<A, B> n1, Node<A, B> n2);
```
```
AbsBST<String, Double> nameTree = new AbsBST<String, Double>() {  
    @Override  
    int compare(AbsBST<String, Double>.Node<String, Double> n1, AbsBST<String, Double>.Node<String, Double> n2) {  
        return n1.a.compareTo(n2.b);  
    }  
};
```

```
abstract class Tree {  
    class Node{  
        Student value;  
        Node[] children= new Node[2];  
    }  
  
    Node root;  
    int RIGHT = 1, LEFT = 0;  
  
    abstract int compare(Student a, Student b);
}
```
```
Tree idTree = new Tree() {  
    @Override  
    int compare(Student a, Student b) {  
        return a.getID().compareTo(b.getID());  
    }  
};  
  
Tree nameTree = new Tree() {  
    @Override  
    int compare(Student a, Student b) {  
        return a.getName().compareTo(b.getName());  
    }  
};  
  
Tree ageTree = new Tree() {  
    @Override  
    int compare(Student a, Student b) {  
        return a.getAge() - b.getAge();  
    }  
};
```
