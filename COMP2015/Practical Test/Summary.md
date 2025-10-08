
|          Data Structure           |       Method        | Time Complexity |              备注               |
| :-------------------------------: | :-----------------: | :-------------: | :---------------------------: |
|       Array<br>固定内存, 适合随机访问       |        sort         |     $O(N)$      |              双指针              |
|                                   |         add         |     $O(N)$      |            添加时维持顺序            |
|                                   |      recursion      |        -        |  Fibonacci, BST, Quick Sort   |
| Singly LinkedList<br>动态内存，适合非随机访问 |    append（按插入顺序）    |     $O(1)$      |      插在 head.next / tail      |
|                                   |     add（按升序/降序）     |     $O(N)$      |             遍历插入              |
|                                   |    get（contains）    |     $O(N)$      |             遍历查找              |
|                                   |       remove        |     $O(N)$      |            遍历查找并删除            |
|                                   |        print        |     $O(N)$      |              遍历               |
| Doubly LinkedList<br>非随机访问，实现栈和队列 |       append        |     $O(1)$      |      插在 head.next / tail      |
|                                   |     add（按升序/降序）     |     $O(N)$      |             遍历插入              |
|                                   |   get (Contains)    |     $O(N)$      |             遍历查找              |
|                                   |  removeFirst/Last   |     $O(1)$      |            直接删除头/尾            |
|   Binary Search Tree<br>适合有序存儲    |         add         |    $O(logN)$    |        从上往下比大小，注意更新数值         |
|                                   |         get         |    $O(logN)$    |             二分查找              |
|                                   |       remove        |    $O(logN)$    |             二分查找              |
|                                   |     getMin/Max      |    $O(logN)$    |           左侧到底/右侧到底           |
|                                   |        topK         |     $O(K)$      |         反向中序遍历（右，中，左）         |
|                                   |        print        |     $O(N)$      | 前序(中，左，右）；中序（左，中，右）；后序（左，右，中） |
|      Hash Map<br>频繁查找<br>无排序      |         add         |     $O(1)$      |           getIndex            |
|                                   | search / getIndexOf |     $O(1)$      |           getIndex            |
|                                   |       remove        |     $O(1)$      |           getIndex            |
|                                   |        print        |     $O(N)$      |              遍历               |
|        Heap<br>访问最值<br>无查找        |         add         |    $O(logN)$    |         percolatingUp         |
|                                   |    delMax/delMin    |    $O(logN)$    |        percolatingDown        |
|                                   |        sort         |   $O(NlogN)$    |          原地实现和非原地实现           |
|         Internal Sorting          |      heapsort       |   $O(NlogN)$    |                               |
|                                   |   insertion sort    |    $O(N^2)$     |                               |
|                                   |      quicksort      |   $O(NlogN)$    |                               |
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
