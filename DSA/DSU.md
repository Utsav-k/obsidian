## Disjoint Set

Source https://cp-algorithms.com/data_structures/disjoint_set_union.html

This data structure provides the following capabilities. We are given several elements, each of which is a separate set. A DSU will have an operation to combine any two sets, and it will be able to tell in which set a specific element is. The classical version also introduces a third operation, it can create a set from a new element.

Thus the basic interface of this data structure consists of only three operations:

1. make_set(v) - creates a new set consisting of the new element v

2. union_sets(a, b) - merges the two specified sets (the set in which the element a is located, and the set in which the element b is located)

3. find_set(v) - returns the representative (also called leader) of the set that contains the element v. This representative is an element of its corresponding set. It is selected in each set by the data structure itself (and can change over time, namely after union_sets calls). This representative can be used to check if two elements are part of the same set or not. a and b are exactly in the same set, if find_set(a) == find_set(b). Otherwise they are in different sets.


### Basic Implementation

```cpp
void makeSet(int v) {
    parent[v] = v;
}

int findSet(int v) {
    if(v == parent[v]) {
        return v;
    }
    return findSet(parent[v]);
}

void union(int a, int b) {
    int u = findSet(a);
    int v = findSet(b);
    if(u != v) {
        parent[v] = u;
    }
}
```

### Path Compression Optimization

```cpp
int findSet(int v) {
    if(v == parent[v]) return v;
    // compressing the path by making the set leader as parent of everything in the path
    return parent[v] = findSet(parent[v]); 
}

```

### Union By Size/Rank

In this optimization we will change the union_set operation. To be precise, we will change which tree gets attached to the other one. In the naive implementation the second tree always got attached to the first one. In practice that can lead to trees containing chains of length O(n). With this optimization we will avoid this by choosing very carefully which tree gets attached.

Most popular are the following two approaches: 
In the first approach we use the size of the trees as rank, and in the second one we use the depth of the tree (more precisely, the upper bound on the tree depth, because the depth will get smaller when applying path compression).


```cpp

// Union by size

void makeSet(int v) {
    parent[v] = v;
    size[v] = 1; // Size of the group of which the leader is v;
}

void unionBySize(int a, int b) {
    a = findSet(a);
    b = findSet(b);

    // If both are from different sets
    if(a != b) {
        // merge smaller set into the larger one.
        if(size[a] < size[b]) {
            swap(a, b);
        }
        parent[b] = a;
        size[a] += size[b];
    }
}
```

```cpp
// Union by Rank
void makeSet(int v) {
    parent[v] = v;
    rank[v] = 0;
}

void unionByRank(int a, int b) {
    a = findSet(a);
    b = findSet(b);

    if(a != b) {
        if(rank[a] < rank[b]) {
            swap(a, b);
        }
        parent[b] = a;
        if(rank[a] == rank[b]) {
            rank[a]++;
        }
    }
}

```

### Time Complexity

Single Operation = O(log n)
Amortized = O(alpha(n))

alpha(n) is Ackerman function, for 10^600 it doesn't exceed 4...

_______________

### Practice Problems

https://leetcode.com/problems/number-of-provinces/description/

https://leetcode.com/problems/redundant-connection/description/

https://leetcode.com/problems/most-stones-removed-with-same-row-or-column/description/ (Decent Problem)

https://leetcode.com/problems/number-of-operations-to-make-network-connected/description/ (Good)

https://leetcode.com/problems/satisfiability-of-equality-equations/description/

https://leetcode.com/problems/accounts-merge/description/

(Nice DSU Problem with Idea from Seive)
https://leetcode.com/problems/graph-connectivity-with-threshold/

