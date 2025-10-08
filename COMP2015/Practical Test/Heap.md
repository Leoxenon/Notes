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