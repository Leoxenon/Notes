
|                 Data Structure                  |       Method        |      Time Complexity      |              备注               |
| :---------------------------------------------: | :-----------------: | :-----------------------: | :---------------------------: |
|               Array（固定内存, 适合随机访问）               |        sort         |          $O(N)$           |              双指针              |
|                                                 |         Add         |          $O(N)$           |            添加时维持顺序            |
|                                                 |      Recursion      |             -             |           常考 BST 使用           |
|         Singly LinkedList（动态内存，适合非随机访问）         |    Append（按插入顺序）    |          $O(1)$           |      插在 head.next / tail      |
|                                                 |     Add（按升序/降序）     |          $O(N)$           |             遍历插入              |
|                                                 |    Get（Contains）    |          $O(N)$           |             遍历查找              |
|                                                 |       Remove        |          $O(N)$           |            遍历查找并删除            |
|                                                 |        Print        |          $O(N)$           |              遍历               |
|          Doubly Linked List（用于实现栈和队列）           |       Append        |          $O(1)$           |      插在 head.next / tail      |
|                                                 |     Add（按升序/降序）     |          $O(N)$           |             遍历插入              |
|                                                 |   Get (Contains)    |          $O(N)$           |             遍历查找              |
|                                                 |  RemoveFirst/Last   |          $O(1)$           |            直接删除头/尾            |
| Binary Search Tree（常见操作平均 O(logN)，适合排序，无法确保平衡树） |         Add         | 平均 $O(logN)$<br>最差 $O(N)$ |        从上往下比大小，注意更新数值         |
|                                                 |         Get         | 平均 $O(logN)$<br>最差 $O(N)$ |             二分查找              |
|                                                 |       Remove        | 平均 $O(logN)$<br>最差 $O(N)$ |             二分查找              |
|                                                 |     GetMin/Max      | 平均 $O(logN)$<br>最差 $O(N)$ |           左侧到底/右侧到底           |
|                                                 |        TopK         |          $O(N)$           |         反向中序遍历（右，中，左）         |
|                                                 |        Print        |          $O(N)$           | 前序(中，左，右）；中序（左，中，右）；后序（左，右，中） |
|             Hash Map（最适合频繁查找，不能排序）              |         Add         |          $O(1)$           |           getIndex            |
|                                                 | Search / getIndexOf |          $O(1)$           |           getIndex            |
|                                                 |       Remove        |          $O(1)$           |           getIndex            |
|                                                 |        Print        |          $O(N)$           |              遍历               |
|              Heap（适合频繁访问最值，不适合查找）               |         Add         |         $O(logN）$         |         percolatingUp         |
|                                                 |    delMax/delMin    |         $O(logN)$         |        percolatingDown        |
|                                                 |        Sort         |        $O(NlogN)$         |             非原地实现             |
|                     Sorting                     |      heapsort       |        $O(NlogN)$         |             原地實現              |
|                                                 |   insertion sort    |         $O(N^2)$          |                               |
|                                                 |     quick sort      |        $O(NlogN)$         |                               |
## **Recursion**
**Fibonacci**
```
// O(2^N)
public static int fibonacci(int n) {
	if (n <= 1) {
		return n;
	}
	return fibonacci(n - 1) + fibonacci(n - 2);
}
```
```
// O(N)
public static int fibonacci(int n) {
	if (n <= 1) {
		return n;
	}
	int a = 0, b = 1;
	for (int i = 2; i <= n; i++) {
		int temp = a + b;
		a = b;
		b = temp;
	}
	return b;
}
```

## **LinkedList**
动态内存、非随机访问
不适合查找、排序
- **Linked List**
	- add()
		- addFirst / addLast
			- O(1)
		- addWithOrdering
			- O(N)
	- get()
		- O(N)
	- remove()
		- O(N)
	- printAll()
		- O(N)
### **Singly LinkedList**
```
public class SinglyLinkedList<V extends Comparable<V>>{  
    class Node {  
        V value;  
        Node next;  
    }  
  
    Node head = new Node();  
    int count;  
  
    // O(N)  
    public void add(V value) {  
        Node newNode = new Node();  
        newNode.value = value;  
        Node cur = head.next;  
        Node pre = head;  
  
        while(cur != null) {  
            if (cur.value.compareTo(value) < 0) {  
                pre = cur;  
                cur = pre.next;  
            } else if (cur.value.compareTo(value) > 0)  
                break;  
            else {  
                // duplicated  
                return;  
            }  
        }  
        pre.next = newNode;  
        newNode.next = cur;  
        count++;  
    }  
  
    // O(1)  
    public void addFirst(V value) {  
        Node newNode = new Node();  
        newNode.value = value;  
        newNode.next = head.next;  
        head.next = newNode;  
        count++;  
    }  
      
  
    // O(N)  
    public V get(int i) {  
        if (i >= count)  
            throw new ArrayIndexOutOfBoundsException();  
        Node tar = head.next;  
        for (int a = 0; a < i; a++)  
            tar = tar.next;  
        return tar.value;  
    }  
  
    // O(N)  
    public int search(V v) {  
        Node cur = head.next;  
        int index = 0;  
        while (cur != null && cur.value.compareTo(v) != 0) {  
            cur = cur.next;  
            index++;  
        }  
        return index;  
    }  
  
    // O(N)  
    public V remove(int i) {  
        if (i >= count)  
            throw new ArrayIndexOutOfBoundsException();  
        Node tar = head.next;  
        Node pre = head;  
  
        for (int k = 0; k < i; k++) {  
            pre = tar;  
            tar = tar.next;  
        }  
        pre.next = tar.next;  
        count--;  
        return tar.value;  
    }  
  
    // O(N)  
    public void remove(V v) {  
        Node cur = head.next;  
        Node pre = head;  
        while (cur != null && cur.value.compareTo(v) != 0) {  
            pre = cur;  
            cur = cur.next;  
        }  
        if (cur == null) return;  
        pre.next = cur.next;  
        count--;  
    }  
  
    // O(N)  
    void printAll() {  
        Node cur = head.next;  
        while(cur != null) {  
            System.out.print(cur + " ");  
            cur = cur.next;  
        }  
    }  
}
```
### **Doubly LinkedList**
```
public class LinkedList<V extends Comparable<V>>{  
    class Node {  
        V value;  
        Node pre;  
        Node next;  
    }  
  
    Node head = new Node();  
    Node tail = head;  
    int count;  
  
    // O(N)  
    public void add(V value) {  
        Node newNode = new Node();  
        newNode.value = value;  
        Node cur = head.next;  
        Node pre = head;  
  
        while(cur != null) {  
            if (cur.value.compareTo(value) < 0) {  
                pre = cur;  
                cur = pre.next;  
            } else if (cur.value.compareTo(value) > 0)  
                break;  
            else {  
                // duplicated  
                return;  
            }  
        }  
        pre.next = newNode;  
        newNode.next = cur;  
        newNode.pre = pre;  
  
        if (cur != null) {  
            cur.pre = newNode;  
        } else {  
            tail = newNode;  
        }  
        count++;  
    }  
  
    // O(1)  
    // push() / enqueue()
    public void addFirst(V value) {  
        Node newNode = new Node();  
        newNode.value = value;  
        
        newNode.next = head.next;  
        head.next = newNode;  
        newNode.pre = head;  
        if (newNode.next != null)
	        newNode.next.pre = newNode;  
  
        if (tail == head) {  
            tail = newNode;  
        }  
        count++;  
    }  
  
    // O(1)  
    // push() / enqueue()
    public void addLast(V value) {  
        Node newNode = new Node();  
        newNode.value = value;  
        tail.next = newNode;  
        newNode.pre = tail;  
        tail = newNode;  
        count++;  
    }  

	// O(1)
	// pop() / peek() / dequeue()
	public V removeFirst() {
		if (tail == head)
			return null;
		V res = head.next.value;
		head.next = head.next.next;
		head.next.pre = head;
		if (head.next == null)
			tail = head;
		count--;
		return res;
	}

	// O(1)
	// pop() / peek() / dequeue()
	public V removeLast() {
		V res = tail.value;
		tail.pre.next = null;
		tail = tail.pre;
		count--;
		return res;
	}
  
    // O(N)  
    public V get(int i) {  
        if (i >= count)  
            throw new ArrayIndexOutOfBoundsException();  
        Node tar = head.next;  
        for (int a = 0; a < i; a++)  
            tar = tar.next;  
        return tar.value;  
    }  
  
    // O(N)  
    public int search(V v) {  
        Node cur = head.next;  
        int index = 0;  
        while (cur != null && cur.value.compareTo(v) != 0) {  
            cur = cur.next;  
            index++;  
        }  
        return index;  
    }  
  
    // O(N)  
    public V remove(int i) {  
        if (i >= count)  
            throw new ArrayIndexOutOfBoundsException();  
        Node tar = head.next;  
        Node pre = head;  
  
        for (int k = 0; k < i; k++) {  
            pre = tar;  
            tar = tar.next;  
        }  
        if (tar.next == null)  
            tail = pre;  
        else  
            tar.next.pre = pre;  
        pre.next = tar.next;  
        count--;  
        return tar.value;  
    }  
  
    // O(N)  
    public void remove(V v) {  
        Node cur = head.next;  
        while (cur != null && cur.value.compareTo(v) != 0) {  
            cur = cur.next;  
        }  
        if (cur == null) return;  
        if (cur.next == null)  
            tail = cur.pre;  
        else  
            cur.next.pre = cur.pre;  
        cur.pre.next = cur.next;  
        count--;  
    }  
  
    // O(N)  
    void printAll() {  
        Node cur = head.next;  
        while(cur != null) {  
            System.out.print(cur.value + " ");  
            cur = cur.next;  
        }  
        System.out.println();
    }  
}
```

### **LinkedList** Implementation of **Stack** and **Queue**

- **Stack**
	- push()
		- O(1)
	- pop()
		- O(1)
	- peek()
		- O(1)
```
public class Stack<T extends Comparable<T>>{  
    private BasicList<T> stack = new BasicList<>();  
  
    public void push(T v) {  
        stack.addLast(v);  
    }  
  
    public T top() {  
        T res = stack.removeLast();  
        stack.addLast(res);  
        return res;  
    }  
  
    public T pop() {  
        return stack.removeLast();  
    }  
}
```

- **Queue**
	- add()
		- O(1)
	- remove()
		- O(1)
```
public class Queue <T extends Comparable<T>>{  
    private BasicList<T> queue = new BasicList<>();  
  
    public void enqueue(T v) {  
        queue.addFirst(v);  
    }  
  
    public T dequeue() {  
        return queue.removeLast();  
    }
}
```

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
		- // Optional
		- O(logN)
	- print
		- historicalHighest()
			- // Optional
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

## **HashTable**
查找
无排序
- **HashTable**
	- set()
		- O(1)
	- get(K k) / getIndexOf(K k)
		- O(1)
	- remove()
		- O(1)
	- print
		- O(N)
```
public class HashMap <K, V>{  
    K[] keys = (K[]) new Object[17];  
    V[] values = (V[]) new Object[17];  
    int prime = 13;  
    int count;  
    K deleted = (K) new Object();  
  
    private int getIndex(K k, int collisions) {  
        int code = Math.abs(k.hashCode());  
        return (code + collisions * hash2(code)) % keys.length;  
    }  
  
    private int hash2(int code) {  
        return prime - code % prime;  
    }  

	// O(1)
    public void set(K k, V v) {  
        int index;  
	      
	/**  for reusing the deleted blocks  
        int index = getIndexOf(k);        
        if (index >= 0) {            
	        values[index] = v;            
	        return;        
		}                                 **/        
		
		int collisions = 0;  
        do {  
            index = getIndex(k, collisions++);  
        } while(keys[index] != null /* && keys != deleted */ && keys[index] != k);  
  
        // Reusing version do not need the condition  
        if (keys[index] == null) {  
            keys[index] = k;  
            count++;  
        }  
        values[index] = v;  
    }  

	// O(1)
    public V search(K k) {  
        int index;  
        int collisions = 0;  
        do {  
            index = getIndex(k, collisions++);  
        } while(keys[index] != null && keys[index] != k);  
  
        if (keys[index] == k) {  
            return values[index];  
        }  
        return null;  
    }  

	// O(1)
    public int getIndexOf(K k) {  
        int index;  
        int collisions = 0;  
        do {  
            index = getIndex(k, collisions++);  
        } while(collisions <= count && keys[index] != null && keys[index] != k);  
  
        if (keys[index] == k) {  
            return index;  
        }  
        return -1;  
    }  

	// O(1)
    public V remove(K k) {  
        int index = getIndexOf(k);  
        if (index >= 0) {  
            V v = values[index];  
            keys[index] = deleted;  
            values[index] = null;  
            count--;  
            return v;  
        }  
        return null;  
    }  

	// O(N)
    public void print() {  
        for (int i = 0; i < keys.length; i++)  
            if (keys[i] != null && keys[i] != deleted)  
                System.out.printf("[%s:%s] ", keys[i], values[i]);  
        System.out.println();  
    }  
      
}
```

## **Heap**
查找最值、排序
不适合查找
- **Heap**
	- add()
		- percolatingUp()
			- swap()
		- O(logN)
	- delMin() / delMax()
		- percolatingDown()
			- swap()
		- O(logN)
	- sort()
		- O(NlogN)

### **MinHeap**
```
class Thing implements Comparable<Thing> {  
    String name;  
    int size;  
  
    @Override  
    public int compareTo(Thing thing) {  
        return name.compareTo(thing.name);  
    }  
}  
  
public class MinHeap<V extends Comparable<V>> {  
    V[] storage;  
    int count;  
  
    public MinHeap(int capacity) {  
        storage = (V[]) new Comparable[capacity + 1];  
    }  
  
    public boolean isFull() {  
        return count == storage.length - 1;  
    }  
  
    public boolean isEmpty() {  
        return count == 0;  
    }  

	// O(logN)
    public void add(V v) {  
        if (isFull()) return;  
		  
        storage[++count] = v;  
        percolatingUp(count);  
    }  

	// O(logN)
    public V delMin() {  
        if (isEmpty()) return null;  
  
        V value = storage[1];  
        storage[1] = storage[count];  
        storage[count] = null;  
        count--;  
        percolatingDown(1);  
        return value;  
    }  

    private void percolatingUp(int c) {  
        int p = c / 2;  
        while (c > 1 && storage[p].compareTo(storage[c]) > 0) {  
            swap(p, c);  
            c = p;  
            p = c / 2;  
        }  
    }  
  
    private void percolatingDown(int c) {  
        int l = c * 2;  
        int r = l + 1;  
        while (l <= count) {  
            if (r > count || storage[l].compareTo(storage[r]) < 0) {  
                // go left  
                if (storage[c].compareTo(storage[l]) > 0) {  
                    swap(c, l);  
                    c = l;   
                } else  
                    break;  
            } else {  
                // go right  
                if (storage[c].compareTo(storage[r]) > 0) {  
                    swap(c, r);  
                    c = r;  
                } else  
                    break;  
            }
            l = c * 2;  
            r = l + 1;   
        }  
    }  
  
    private void swap(int a, int b) {  
        V temp = storage[a];  
        storage[a] = storage[b];  
        storage[b] = temp;  
    }  

	// O(logN)
	// external version
	static public <V extends Comparable<V>> void sort(V[] data) {  
	    MaxHeap<V> heap = new MaxHeap<>(data.length);  
		  
	    for(V d: data)  
	        heap.add(d);  
		  
	    int i=0;  
	    while(!heap.isEmpty())  
	        data[i++] = heap.delMin();  
	}
  
}
```

### **MaxHeap**
```
public class MaxHeap<V extends Comparable<V>> {  
    V[] storage;  
    int count;  
  
    public MaxHeap(int capacity) {  
        storage = (V[]) new Comparable[capacity + 1];  
    }  
  
    public boolean isFull() {  
        return count == storage.length - 1;  
    }  
  
    public boolean isEmpty() {  
        return count == 0;  
    }  

	// O(logN)
    public void add(V v) {  
        if (isFull()) return;  
  
        storage[++count] = v;  
        percolatingUp(count);  
    }  

	// O(logN)
    public V delMax() {  
        if (isEmpty()) return null;  
  
        V value = storage[1];  
        storage[1] = storage[count];  
        storage[count] = null;  
        count--;  
        percolatingDown(1);  
        return value;  
    }  
  
    private void percolatingUp(int c) {  
        int p = c / 2;  
        while (c > 1 && storage[p].compareTo(storage[c]) < 0) {  
            swap(p, c);  
            c = p;  
            p = c / 2;  
        }  
    }  
  
    private void percolatingDown(int c) {  
        int l = c * 2;  
        int r = l + 1;  
        while (l <= count) {  
            if (r > count || storage[l].compareTo(storage[r]) > 0) {  
                // go left  
                if (storage[c].compareTo(storage[l]) < 0) {  
                    swap(c, l);  
                    c = l;  
                } else  
                    break;  
            } else {  
                // go right  
                if (storage[c].compareTo(storage[r]) < 0) {  
                    swap(c, r);  
                    c = r;   
                } else  
                    break;  
            }
            l = c * 2;  
            r = l + 1;   
        }  
    }  
  
    private void swap(int a, int b) {  
        V temp = storage[a];  
        storage[a] = storage[b];  
        storage[b] = temp;  
    }  

	// O(logN)
	// external version
	static public <V extends Comparable<V>> void sort(V[] data) {  
	    MaxHeap<V> heap = new MaxHeap<>(data.length);  
		
	    for(V d: data)  
	        heap.add(d);  
		  
	    int i=0;  
	    while(!heap.isEmpty())  
	        data[i++] = heap.delMax();  
	}
}
```
## **Sorting Algorithms**

### **HeapSort (Asc, Max)**
```
public class HeapSort {  
  
    public static <V extends Comparable<V>> void asc(V[] data) {  
        if (data.length < 2) return;  
  
        V min = data[0];  
        int index = 0;  
        for (int i = 1; i < data.length; i++) {  
            if (data[i].compareTo(min) < 0) {  
                min = data[i];  
                index = i;  
                }  
        }  
        swap(data, index, 0);  
  
        for (int i = data.length / 2; i > 0; i--) {  
            percolatingDown(data, i, data.length - 1);  
        }  
  
        int i = data.length - 1;  
  
        while(i > 1) {  
            swap(data, 1, i);  
            percolatingDown(data, 1, --i);  
        }  
    }  
  
    private static <V extends Comparable<V>> void percolatingDown(V[] data, int c, int lastIdx) {  
        int l = c * 2, r = l + 1;  
        while (l <= lastIdx) {  
            if (r > lastIdx || data[l].compareTo(data[r]) > 0) {  
                if (data[c].compareTo(data[l]) < 0) {  
                    swap(data, l, c);  
                    c = l;  
                } else break;  
            } else {  
                if (data[c].compareTo(data[r]) < 0) {  
                    swap(data, r, c);  
                    c = r;  
                } else break;  
            }  
            l = c * 2;  
            r = l + 1;  
        }  
    }  
  
    private static <V extends Comparable<V>> void swap(V[] data, int a, int b) {  
        V temp = data[a];  
        data[a] = data[b];  
        data[b] = temp;  
    }  
}
```

### **Insertion Sort**
```
public class InsertionSort {  
  
    public static <V extends Comparable<V>> void asc(V[] data) {  
        int i = data.length - 2;  
        while(i >= 0) {  
            for (int j = i; j < data.length - 1; j++)  
                if (data[j].compareTo(data[j+1])>0)  
                    swap(data, j, j+1);  
            i--;  
        }  
    }  
  
    private static <V extends Comparable<V>> void swap(V[] data, int a, int b) {  
        V temp = data[a];  
        data[a] = data[b];  
        data[b] = temp;  
    }  
}
```

### **Quick Sort**
```
public class QuickSort {  
    public static <V extends Comparable<V>> void quicksort(V[]data, int s, int e)  
    {  
        if (s >= e) return;  
        V pivot = data[e];  
        int l = s, r = e - 1;  
        while (l <= r) {  
            while (l <= r && data[l].compareTo(pivot) < 0) l++;  
            while (l <= r && data[r].compareTo(pivot) >= 0) r--;  
            if (l < r) swap(data, l, r);  
        }  
        swap(data, l, e);  
        quicksort(data, s, l - 1);  
        quicksort(data, l + 1, e);  
    }  
  
    private static <V extends Comparable<V>> void swap(V[] data, int a, int b) {  
        V temp = data[a];  
        data[a] = data[b];  
        data[b] = temp;  
    }  
}
```