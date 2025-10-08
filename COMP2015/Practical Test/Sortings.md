## **Sorting Algorithms**
### **HeapSort**
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