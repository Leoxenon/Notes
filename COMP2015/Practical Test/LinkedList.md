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
		if (head.next == null)
			tail = head;
		else 
			head.next.pre = head;
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
