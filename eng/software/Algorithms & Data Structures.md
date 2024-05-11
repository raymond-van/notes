# Algorithms
## KMP
- used for finding patterns in an array/string in O(n + m)
1. Generate array that contains the length of the longest proper prefix equal to the suffix at each index
2.  Use LPS array to find patterns in array/string
- https://leetcode.com/problems/number-of-subarrays-that-match-a-pattern-ii/description/
## Range Queries

# Data Structures
## Trees
- Degree of a node = # of descendants of a node
## Priority Queues
- each element in the queue has a priority associated with it
- an abstract data type similar to stacks/queues 
- often implemented as a heap
## Heap
- Heap property: 
	- for a max heap, a node p must be greater than the values of its children
- can be implemented as an array or in tree form (binary heap)
- can find the largest/smallest element in O(1)
- adding elements / removing the largest/smallest element in O(log n)
	- must maintain heap property while adding/removing
- array implementation
	- the root will be at index 0
	- children of a node i can be found at index 2i + 1 and 2i + 2
	- parent of node i can be found at floor((i-1)/2)
## Balanced Trees
## Segment Trees


# Bit Manipulation
- Bit shifting:
	- multiple/divide n by powers of 2
		- `n <<= x` or `n >>= x`
			- `n = n * (2^x)`
	- multiple/divide n by 2
		- `n <<= 1` or `n >>= 1`
			- `n = n * (2^1)`
- Hamming weight (aka pop count / number of 1 bits)
	- For any number n, doing a logical AND of n and n-1 flips the least significant 1-bit in n to 0
	- https://leetcode.com/problems/number-of-1-bits/solution/
		- O(log n) runtime since the number of bits in n is log n + 1 and all bits in n can be 1
- https://leetcode.com/problems/counting
	- DP + Least Sig. Bit:
		- P(x) = P(x/2) + (x mod 2)
	- DP + Last Set Bit:
		- P(x) = P(x & (x - 1)) + 1
		