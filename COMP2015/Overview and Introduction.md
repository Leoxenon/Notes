
## Overview

Abstract data structure: List, Stack, Queue, Tree
Agorithms: Sorting, Searching, Graph
![[Course Organization.png]]

## Introduction 

- Make programs run efficiently
	- Data structures: efficient management of data
	- Algorithms: efficient sequence of actions

Data Size: N (can be ->∞)

- Algorithm design
	- Correctness
	- Clarity
	- Elegance
	- Efficiency


Steps:
1. Design a algorithm
	- Use good data structures
2. Proving correctness
3. Formal study of efficiency
	- Running time
		- Time function: T(N) = ... = O(?)
	- Storafe required

Analysis:
- Empirical analysis
	- Actual data
	- Random data (average-case behavior)
	- Perverse data (worst-case behavior)
- Mathematical analysis
	- Characterizes running time as a function of the input size, N
	- Takes into account all possible inputs, often analyzing the worst case
- Main Concerns
	- The algorithm used
	- The input to the algorithm
- Random-AccessMachine (RAM) model assumed
	- Instructions are executed one after another. No concurrent operations (Not parallel computers)
	- Each of instructions takes a constant amount of time

![[T(N) Example.png]]T(N) = 6N + 4 = O(N)


![[Tutorial 1.png]]
(1) 
```
int Sum(int N) {
	double i;
	int sum, j, tmp;
	for (i = 0.0; i <= N; i++) {
		tmp = 1;
		for (j = 0; j < i; j++) {
			tmp *= 4;
		}
		sum += i / tmp;
	}
	return sum;
}
```

   S(n)    =                       1 * (1/4)^1 + 2 * (1/4)^2 + ... + N * (1/4)^N
4 * S(n) =  1 * (1/4)^0 + 2 * (1/4)^1 + ... + N * (1/4)^(N-1)

4 * S(n) - S(n) = 1 + (1/4)^1 + (1/4)^2 + ... + (1/4)^N - N * (1/4)^N

When n -> ∞,
3 * S(n) = 1/(1-1/4) = 4/3
S(n) = 4/9

(2)
```
int Sum(int N) {
	double i;
	int sum, j, tmp;
	for (i = 0.0; i <= N; i++) {
		tmp = 1;
		for (j = 0; j < i; j++) {
			tmp *= 4;
		}
		sum += i*i / tmp;
	}
	return sum;
}
```
 
   S(n)    =           1 * 1 * (1/4)^1 + 2 * 2 * (1/4)^2 + ... + N * N * (1/4)^N
4 * S(n) =  1 * 1 * (1/4)^0 + 2 * 2 * (1/4)^1 + ... + N * N * (1/4)^(N-1)

4 * S(n) - S(n) = 1 + 3 * (1/4)^1 + 5 * (1/4)^2 + ... + (2N-1) * (1/4)^(N - 1) - N * N * (1/4)^N
4 * [4 * S(n) - S(n)] = 4 + 3 * (1/4)^0 + 5 * (1/4)^1 + ... + (2N-1) * (1/4)^(N-2) - N * N * (1/4)^(N-1)

12 * S(n) - 3 * S(n) = 4 + 2 * [1 + (1/4)^1 + (1/4)^2 + ... + (1/4)^(N-2)] - N * N * (1/4)^(N-1) + N * N * (1/4)^N

When N -> ∞,
9 * S(n) = 4 + 2 * (4/3)
S(n) = 20/27