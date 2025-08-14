# DP on trees

## Tutorial

### When to Use Tree DP
Use this approach when:

You are given a tree, and

You're trying to compute something using its structure, typically by combining results from subtrees.

```cpp
ReturnType dfs(TreeNode* node) {
    if (!node) return base_case;

    ReturnType left = dfs(node->left);
    ReturnType right = dfs(node->right);

    // Compute current result based on left and right
    ResultType current = combine(node, left, right);

    // Update global answer if needed
    updateGlobal(current);

    // Return part of the result relevant to the parent
    return returnToParent(current);
}
```

### Key Concepts to Keep in Mind
Return Value:

Decide what value your recursive function should return to its parent (e.g., the best single-path sum up to that point).

Global State:

Maintain a global variable if you're aggregating something across the tree (e.g., max path, longest diameter).

Post-order DFS:

Recurse left and right, then process the current node after.

Prune or Zero-Out Negative Paths:

For problems like max path sum, consider max(0, left) to ignore paths that reduce the total.


## CSES Subordinates

https://cses.fi/problemset/task/1674


### Solution
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> ans;
int solve(vector<vector<int>> &g, int s) {
    int tmp = 0;
    for(int v:g[s]) {
        tmp = tmp + 1 + solve(g, v);
    }
    ans[s] = tmp;
    return tmp;
}

int main() {
    int n;
    cin >> n;
    vector<vector<int>> g(n+1);
    ans.resize(n+1);
    for(int i=2; i<=n; i++) {
        int x;
        cin >> x;
        g[x].push_back(i);
    }
    solve(g, 1);
    for(int i=1; i<=n; i++) cout << ans[i] << ' ';
    return 0;
}
```
## Binary Tree Diameter

https://leetcode.com/problems/diameter-of-binary-tree/

```cpp
class Solution {
public:
    int ans = 0;
    int diameterOfBinaryTree(TreeNode* root) {
        dfs(root);
        return ans;
    }

    int dfs(TreeNode* root) {
        if(!root) return 0;
        int left = dfs(root->left);
        int right = dfs(root->right);
        ans = max(ans, left + right);
        return 1 + max(left, right);
    }
};
```

## Binary Tree Maximum Path Sum

https://leetcode.com/problems/binary-tree-maximum-path-sum/description/

```cpp
int ans = INT_MIN;
int solve(TreeNode* root) {
    if(!root) return 0;
    int l = max(0, solve(root->left));
    int r = max(0, solve(root->right));
    // Possible Max Solution if we consider the path starting from current node as global answer
    int tmp = root->val + l + r;
    // update global answer
    ans = max(ans, tmp);
    // This has to be either left or right path from any node, this returns to the parent.
    return max(l, r) + root->val;
}
int maxPathSum(TreeNode* root) {
    solve(root);
    return ans;
}
```