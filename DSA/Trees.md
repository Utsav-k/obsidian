# Trees

## Traversals

### Inorder Traversal

```cpp

// recursive implementation
void dfs(TreeNode* root, vector<int> &ans) {
    if(!root) return;
    dfs(root->left, ans);
    ans.push_back(root->val);
    dfs(root->right, ans);
}
vector<int> inorderTraversal(TreeNode* root) {
    vector<int> ans;
    dfs(root, ans);
    return ans;
}

// iterative, push the left tree of the root, then process right;

void push_all(TreeNode* cur, stack<TreeNode*> &st) {
    while(cur) {
        st.push(cur);
        cur = cur->left;
    }
}
vector<int> inorderTraversal(TreeNode* root) {
    vector<int> ans;
    stack<TreeNode*> st;
    push_all(root, st);
    while(!st.empty()) {
        TreeNode* cur = st.top();
        st.pop();
        ans.push_back(cur->val);
        if(cur -> right) {
            push_all(cur->right, st);
        }
    }
    return ans;
}

```

### Preorder traversal

```cpp
// This style generally works for all 3 traversals.
vector<int> preorderTraversal(TreeNode* root) {
    stack<TreeNode*> st;
    vector<int> ans;
    TreeNode* cur = root;
    while(cur || !st.empty()) {
        // this if before is important, otherwise you repeat push into the stack.
        if(cur) {
            while(cur) {
                ans.push_back(cur->val);
                st.push(cur);
                cur = cur->left;
            }
        } else {
            TreeNode* t = st.top();
            st.pop();
            if(t->right) cur = t->right;
        }
    }
    return ans;
}
```

### PostOrder Traversal


```cpp
// iterative is hard - TODO

```

### Top/Bottom View of BT

http://geeksforgeeks.org/problems/bottom-view-of-binary-tree/1 , https://www.geeksforgeeks.org/problems/top-view-of-binary-tree/1

```cpp
map<int,int> mp, mp2;
vector <int> bottomView(Node *root) {
    dfs(root);
    vector<int> ans;
    for(auto it:mp) {
        ans.push_back(it.second);
    }
    return ans;
}
void dfs(Node* root, int x = 0, int d = 0) {
    if(!root) return;
    if(mp.find(x) == mp.end() || mp2[x] <= d) {
        mp[x] = root->data;
        mp2[x] = d;
    }
    dfs(root->left, x-1, d+1);
    dfs(root->right, x+1, d+1);
}
```

## Lowest Common Ancestor

https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/description/

```cpp
/* 
This can be a bit tricky, Need to return the LCA so that it reaches the root of the tree at 
the end of traversal.
*/
TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    if(!root) return NULL;

    // If root is one of the nodes, return the root itself.
    if(root == p || root == q) return root;

    TreeNode* l = lowestCommonAncestor(root->left, p, q);
    TreeNode* r = lowestCommonAncestor(root->right, p, q);
    
    // If both are found as l and r, this is the LCA, return it
    if(l && r) return root;

    // Otherwise return l or r
    if(l) return l;
    if(r) return r;
    return NULL;
}


/*

if both p and q exist in Tree rooted at root, then return their LCA
if neither p and q exist in Tree rooted at root, then return null
if only one of p or q (NOT both of them), exists in Tree rooted at root, return it

*/
```



## Problems

1. Left/right view of binary tree - https://www.geeksforgeeks.org/problems/left-view-of-binary-tree/1

2. http://geeksforgeeks.org/problems/bottom-view-of-binary-tree/1 , https://www.geeksforgeeks.org/problems/top-view-of-binary-tree/1

3. Max Width of BT - https://leetcode.com/problems/maximum-width-of-binary-tree/solutions/

4. Decent Question on Finding path - https://takeuforward.org/data-structure/print-root-to-node-path-in-a-binary-tree/

5. Diameter of BT - https://leetcode.com/problems/diameter-of-binary-tree/description/

6. If BT is height balanced - https://leetcode.com/problems/balanced-binary-tree/description/

7. LCA https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/description/

8. Don't know why the fuck this exists https://takeuforward.org/plus/dsa/problems/boundary-traversal


# Binary Search Trees

## Searching a node in the BST
https://leetcode.com/problems/search-in-a-binary-search-tree/

```cpp
class Solution {
public:
    TreeNode* searchBST(TreeNode* root, int val) {
        if(!root) return NULL;
        if(root->val == val) return root;
        if(root->val > val) return searchBST(root->left, val);
        return searchBST(root->right, val);
    }
};
```

## Validate Binary Search Tree (Can be tricky)

https://leetcode.com/problems/validate-binary-search-tree/description/


```cpp
bool ans = true;
void dfs(TreeNode* root, TreeNode* &prev) {
    if(!root) return;
    dfs(root->left, prev);
    if(prev != NULL && prev->val >= root->val) ans = false;
    prev = root;
    dfs(root->right, prev);
}
bool isValidBST(TreeNode* root) {
    if(!root) return true;
    TreeNode* prev = NULL;
    dfs(root, prev);
    return ans;
}
```

Little Better Solution Without the global variable

```cpp
bool dfs(TreeNode* root, TreeNode* &prev) {
    if(!root) return true;
    if(!dfs(root->left, prev)) return false;
    if(prev != NULL && prev->val >= root->val) return false;
    prev = root;
    if(!dfs(root->right, prev)) return false;
    return true;
}
bool isValidBST(TreeNode* root) {
    if(!root) return true;
    TreeNode* prev = NULL;
    return dfs(root, prev);
}
```


## LCA in BST - Optimized

https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/

```cpp
TreeNode* lca(TreeNode* root, TreeNode* p, TreeNode* q) {
    if(!root) return NULL;
    if(root == p || root == q) return root;

    if(p->val > q->val) swap(p, q);

    // This means the LCA is on the left;
    if(root->val > q->val) {
        return lca(root->left, p, q);
    }
    // This means the lca is on right;
    if(root->val < p->val) {
        return lca(root->right, p, q);
    }
    // p <= root <= q => this is the splitting point between the nodes
    return root;
}
```

## BST Iterator 
https://leetcode.com/problems/binary-search-tree-iterator/solutions/

Another problem that can be optimized using BST iterator
https://leetcode.com/problems/two-sum-iv-input-is-a-bst/description/

## Flatten Binary Tree to Linked List

https://leetcode.com/problems/flatten-binary-tree-to-linked-list/description/



## Problem List From ChatGPT


1. Same Tree (Basic, Easy)
     https://leetcode.com/problems/same-tree

2. Symmetric Tree (Basic, Easy)
     https://leetcode.com/problems/symmetric-tree

3. Maximum Depth of Binary Tree (Basic, Easy)
     https://leetcode.com/problems/maximum-depth-of-binary-tree

4. Balanced Binary Tree (Basic, Easy)
     https://leetcode.com/problems/balanced-binary-tree

5. Minimum Depth of Binary Tree (Basic, Easy)
     https://leetcode.com/problems/minimum-depth-of-binary-tree

6. Binary Tree Inorder Traversal (Traversal, Easy)
     https://leetcode.com/problems/binary-tree-inorder-traversal

7. Level Order Traversal (Traversal, Medium)
     https://leetcode.com/problems/binary-tree-level-order-traversal

8. Zigzag Level Order Traversal (Traversal, Medium)
     https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal

9. Right Side View (Traversal, Medium)
     https://leetcode.com/problems/binary-tree-right-side-view

10. Invert Binary Tree (DFS, Easy)
     https://leetcode.com/problems/invert-binary-tree

11. Path Sum (DFS / Backtracking, Easy)
     https://leetcode.com/problems/path-sum

12. Path Sum II (DFS / Backtracking, Medium)
     https://leetcode.com/problems/path-sum-ii

13. Path Sum III (DFS / Prefix Sum, Medium)
     https://leetcode.com/problems/path-sum-iii

14. Diameter of Binary Tree (DFS, Easy)
     https://leetcode.com/problems/diameter-of-binary-tree

15. Binary Tree Maximum Path Sum (DFS, Hard)
     https://leetcode.com/problems/binary-tree-maximum-path-sum

16. Lowest Common Ancestor of Binary Tree (LCA, Medium)
     https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree

17. Lowest Common Ancestor of a BST (LCA / BST, Easy)
     https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree

18. Validate BST (BST, Medium)
     https://leetcode.com/problems/validate-binary-search-tree

19. Search in a BST (BST, Easy)
     https://leetcode.com/problems/search-in-a-binary-search-tree

20. Insert into BST (BST, Medium)
     https://leetcode.com/problems/insert-into-a-binary-search-tree

21. Delete Node in BST (BST, Medium)
     https://leetcode.com/problems/delete-node-in-a-bst/description/

22. Kth Smallest in BST (BST / Inorder, Medium)
     https://leetcode.com/problems/kth-smallest-element-in-a-bst


_____________


23. Construct Tree from Preorder + Inorder (Tree Construction, Medium)
     https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal

24. Construct Tree from Inorder + Postorder (Tree Construction, Medium)
     https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal

25. Serialize and Deserialize Binary Tree (Serialization, Hard)
     https://leetcode.com/problems/serialize-and-deserialize-binary-tree

26. Vertical Order Traversal (View Based, Medium)
     https://leetcode.com/problems/binary-tree-vertical-order-traversal

27. Vertical Order with Sorting (View Based, Hard)
     https://leetcode.com/problems/vertical-order-traversal-of-a-binary-tree

28. All Nodes Distance K in Binary Tree (DFS + Graph, Medium)
     https://leetcode.com/problems/all-nodes-distance-k-in-a-binary-tree

29. Subtree of Another Tree (Pattern Matching, Easy)
     https://leetcode.com/problems/subtree-of-another-tree

30. Binary Tree Cameras (Greedy + DFS, Hard)
     https://leetcode.com/problems/binary-tree-cameras

31. Longest Path with Different Adjacent Characters (DFS / Tree DP, Hard)
     https://leetcode.com/problems/longest-path-with-different-adjacent-characters
