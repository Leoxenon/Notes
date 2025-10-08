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
	
	// O(N)
	public void toArray(Object[] output) {  
    int idx = 0;  
    for (int i=0; i<capacity() && idx < output.length; i++) {  
        if (keys[i] != null && keys[i] != deleted)  
            output[idx++] = values[i];  
    }  
      
}
```
