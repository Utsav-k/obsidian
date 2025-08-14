# Heaps and Priority Queue

## Introduction

https://www.hackerearth.com/practice/notes/heaps-and-priority-queues/

A heap is a specific tree based data structure in which all the nodes of tree are in a specific order. Let’s say if X is a parent node of Y, then the value of X follows some specific order with respect to value of Y and the same order will be followed across the tree.

An array can be used to simulate a tree in the following way. If we are storing one element at index i in array Arr, then its parent will be stored at index i/2 (unless its a root, as root has no parent) and can be access by Arr[ i/2 ], and its left child can be accessed by Arr[ 2 * i ] and its right child can be accessed by Arr[ 2 * i +1 ]. Index of root will be 1 in an array.

```cpp
parent[i] = a[i/2];
leftChild[i] = a[2*i];
rightChild[i] = a[2*i+1];
```

### Insertion in Heap

Always inseart at the end of the Heap and then adjust

Example

```cpp
// Insert 60 in the given heap
50 30 20 15 10 8 16 (60)

// Adjust - Compare with parent and swap
swap(60, 15) // i/2th index
swap(60, 30) // i/2th index
swap(60, 50) // i/2th index

// Final Heap
60 50 20 30 8 10 16 15
```

### Deletiom From Heap

To maintain a complete binary tree, remove the Root of the heap and replace it with the last element of the tree. 

Then compare and adjust so it’s a min/max heap.

```cpp
/*
Adjusting is done by comparing the new root with it's children and swap with the 
bigger children, As the root needs to be the biggest.
Example
*/
Arr = 50 30 20 15 10 8 16
remove 50

Arr = _ 30 20 15 10 8 16
Arr = 16 30 20 15 10 8 _
// Now start swapping with either 2*i or 2*i + 1 depending on which one is larger

Arr = 30 16 20 15 10 8
```

### Implementations - Heapify / BuildHeap / HeapSort

```cpp
void heapify(int i, int N) {
    while(i < N) {
        int lc = 2*i+1;
        int rc = 2*i+2;
        if(lc >= N) break;
        int mx = lc;
        if(rc < N && A[lc] < A[rc]) {
            mx = rc;
        }
        if(A[mx] > A[i]) {
            swap(A[mx], A[i]);
        }
        i = mx;
    }
}
void buildMaxHeap() {
    int n = A.size();
    for (int i=n/2; i>=0; --i) {
        heapify(i, n);
    }
}
int main() {
    ios_base::sync_with_stdio(0);
    cin.tie(0);

    A.clear();
    A = {1,2,3,4,5,6,7};
    buildMaxHeap();
    print();
}
```

## Priority Queue Implementations

```cpp
// pq with custom comparator using lambda.

auto comapre = [](vector<int> &a, vector<int> &b) -> bool {
    return a[0] * a[0] + a[1] * a[1] < b[0] * b[0] + b[1] * b[1];
};

priority_queue<vector<int>, vector<vector<int>>, decltype(comapre)> pq(compare);

// or

priority_queue<vector<int>, vector<vector<int>>, compare> pq;

// Method Overriding
struct compare {
    bool operator()(vector<int>& p, vector<int>& q) {
        return p[0] * p[0] + p[1] * p[1] < q[0] * q[0] + q[1] * q[1];
    }
};

// Max Heap
priority_queue<int> pq;

// Min Heap
priority_queue<int, vector<int>, greater<int>> pq;

```

____________

## Practice Problems

### LC378 - Find the Kth Smallest Element in a Sorted Matrix 

https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/ 
Given an n x n matrix where each of the rows and columns is sorted in ascending order, return the kth smallest element in the matrix.

#### Similar Problems
https://leetcode.com/problems/find-k-pairs-with-smallest-sums/description/

https://leetcode.com/problems/kth-smallest-number-in-multiplication-table/description/

https://leetcode.com/problems/find-k-th-smallest-pair-distance/description/

https://leetcode.com/problems/k-th-smallest-prime-fraction/description/



