# Dynamic Programming

## Resources

https://leetcode.com/discuss/general-discussion/458695/Dynamic-Programming-Patterns

https://leetcode.com/discuss/general-discussion/662866/DP-for-Beginners-Problems-or-Patterns-or-Sample-Solutions

https://leetcode.com/discuss/general-discussion/1050391/Must-do-Dynamic-programming-Problems-Category-wise


## Problems (Category Wise)


### Unbounded Knapsack

#### [Coin Change](https://leetcode.com/problems/coin-change/) (Classic Problem)

#### [Coin Change II](https://leetcode.com/problems/coin-change-2/) 
(Read about why the coins should be in outer loop)

```cpp
int change(int amount, vector<int>& coins) {
    vector<int> dp(amount+1, 0);
    dp[0] = 1;
    for(int &x:coins) {
        for(int i=x; i<=amount; ++i) {
            dp[i] += dp[i-x];
        }
    }
    return dp[amount];
}
```

#### [CombinationSum4](https://leetcode.com/problems/combination-sum-iv/description/) 
(This is similar to Coin Change 2 but the duplicates (order change of the array) are allowed, Just reverse the loop)
```cpp
int combinationSum4(vector<int>& A, int target) {
    int n = A.size();
    vector<unsigned int> dp(target+1, 0);
    dp[0] = 1;
    for(int i=1; i<=target; i++) {
        for(int &x:A) {
            if(i >= x) dp[i] += dp[i-x];
        }
    }
    return dp[target];
}
```

#### [Minimum Cost for Tickets](https://leetcode.com/problems/minimum-cost-for-tickets/) 
(It has the added complexity due to days not being continuous)

```cpp
class Solution {
    const int inf = 1e9+7;
public:
    int mincostTickets(vector<int>& days, vector<int>& costs) {
        unordered_set<int> st(days.begin(), days.end());
        vector<int> dp(367, inf);
        dp[0] = 0;
        for(int i=1; i<=365; ++i) {
            if(st.find(i) == st.end()) {
                // This is important, if you don't have to visit a day, the number remains same.
                dp[i] = dp[i-1];
            } else {
                dp[i] = min(dp[i], dp[i-1] + costs[0]);
                dp[i] = min(dp[i], dp[max(i-7, 0)] + costs[1]);
                dp[i] = min(dp[i], dp[max(i-30, 0)] + costs[2]);
            }
        }
        return dp[days.back()];
    }
};
```

#### [Rod Cutting Problem](https://www.geeksforgeeks.org/cutting-a-rod-dp-13/) 
(Classic DP Problem, Similar Solution to Coin Change 2)
```cpp
int main() {
    int n;
    cin >> n;
    vector<int> a(n);
    for(int i=0; i<n; i++) {
        cin >> a[i];
    }

    vector<int> dp(n+1, 0);
    for(int j=0; j<n; j++) {
        for(int i=j+1; i<=n; ++i) {
            dp[i] = max(dp[i], dp[i-j-1] + a[j]);
        }
    }
    cout << dp[n] << " ";
    return 0;
}
```

_____________________

### Subset Sum Problems

#### [416. Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/description/)

Just another variant of Coin Change II

Find if the subsetSum of totalSum/2 is possible.

**Important - can also be used to find the min difference of subsets**

#### [1049.Last Stone Weight II](https://leetcode.com/problems/last-stone-weight-ii/description/) 

Tricky Problem but resolves to Minimum Subset Sum Difference


```cpp
int lastStoneWeightII(vector<int>& A) {
    int n = A.size();
    int tw = accumulate(A.begin(), A.end(), 0);
    
    // dp[i] represents if a subset sum of i is possible 
    vector<int> dp(tw+1, 0);
    dp[0] = 1;
    for(int &x:A) {
        for(int i=tw; i>=x; i--) {
            dp[i] |= dp[i-x];
        }
    }
    int ans = INT_MAX;
    for(int sum=0; sum<=tw; sum++) {
        if(dp[sum]) {
            ans = min(ans, abs(sum - (tw-sum)));
        }
    }
    return ans;
}
```

#### [494. Target Sum](https://leetcode.com/problems/target-sum/description/)

Classical DP - Find the number of subsets to reach a sum of K

```cpp
int findTargetSumWays(vector<int>& A, int T) {
    int ts = accumulate(A.begin(), A.end(), 0);
    // Not possible as all elements are positive
    if(ts < T) return 0;
    // fractional is not possible
    if((ts + T) & 1) return 0;

    int target = (T + ts) >> 1;
    if(target < 0) return 0;

    // Just find the subsets with sum = target.
    vector<int> dp(target+1, 0);
    dp[0] = 1;
    for(int &x:A) {
        for(int i=target; i>=x; --i) {
            dp[i] += dp[i-x];
        }
    }
    return dp[target];
}
```
____________________________


### Min-Max Kind of Problems

LC64(Classic DP) - https://leetcode.com/problems/minimum-path-sum/description

LC746(Classic DP) - https://leetcode.com/problems/min-cost-climbing-stairs/description/

LC931 - https://leetcode.com/problems/minimum-falling-path-sum/description/

LC322(Classic DP) - https://leetcode.com/problems/coin-change/description/


LC650 - https://leetcode.com/problems/2-keys-keyboard/description/  (Top Down is easy, Bottom up can be tricky. Read the LC article for all solutions)


LC120 - https://leetcode.com/problems/triangle/description/

LC279 - https://leetcode.com/problems/perfect-squares/description (Coin Change kind of problem)


LC983 - https://leetcode.com/problems/minimum-cost-for-tickets/description/


___________


## DP On Strings (Substring/Subsequence)

### Longest Common Subsequence

[1143. Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/description)

Classic DP problem

```cpp
int longestCommonSubsequence(string s1, string s2) {
    int m = s1.length();
    int n = s2.length();
    vector<vector<int>> dp(m+1, vector<int> (n+1));
    // state dp[i][j] means longest subsequence before i in s1 and j in s2;

    for(int i=1; i<=m; ++i) {
        for(int j=1; j<=n; j++) {
            if(s1[i-1] == s2[j-1]) {
                dp[i][j] = 1 + dp[i-1][j-1];
            } else {
                // Do not have to consider dp[i-1][j-1] here as it's taken in account.
                dp[i][j] = max(dp[i-1][j-1], max(dp[i-1][j], dp[i][j-1]));
            }
        }
    }
    return dp[m][n];
}
```

[72. Edit Distance](https://leetcode.com/problems/edit-distance/description/)

Another Classic Variant of Longest Common Subsequence

```cpp
int minDistance(string word1, string word2) {
        int m = word1.size();
        int n = word2.size();
        vector<vector<int>> dp(m+1, vector<int>(n+1));
        // prepare base cases
        for(int i=1; i<=m; i++) {
            dp[i][0] = i;
        }
        for(int i=1; i<=n; i++) {
            dp[0][i] = i;
        }
        for(int i=1; i<=m; ++i) {
            for(int j=1; j<=n; ++j) {
                if(word1[i-1] == word2[j-1]) {
                    dp[i][j] = dp[i-1][j-1];
                }
                else {
                    // Here the {i-1, j-1} should be considered because of replace action.
                    dp[i][j] = 1 + min({dp[i-1][j], dp[i][j-1], dp[i-1][j-1]});
                }
            }
        }
        return dp[m][n];
    }
```

[115. Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/)

Hard Problem based on a similar concept
```cpp
/*
Table is as follows

    b a b g b a g
  1 1 1 1 1 1 1 1  This is initialized to account for 0 length and to make life easier.
b 0 1 1 2 2 3 3 3
a 0 0 1 1 1 1 4 4
g 0 0 0 0 1 1 1 5
*/

int numDistinct(string s, string t) {
        int m = t.size();
        int n = s.size();
        
        vector<vector<unsigned int>> dp(m+1, vector<unsigned int>(n+1));

        for(int j=0; j<=n; j++) {
            dp[0][j] = 1;
        }
        for(int i=1; i<=m; ++i) {
            for(int j=1; j<=n; ++j) {
                if(t[i-1] == s[j-1]) {
                    // In this case, the carryover from {i-1, j-1} is considered 
                    // along with the current value.
                    dp[i][j] = dp[i-1][j-1] + dp[i][j-1];
                } else {
                    // If a character does not match then the total is the number for the current char
                    // Because to create a successful match, the current char should match.
                    dp[i][j] = dp[i][j-1];
                }
            }
        }
        return dp[m][n];
    }
```

[1092. Shortest Common Supersequence](https://leetcode.com/problems/shortest-common-supersequence/description/)

Problem based on Longest Common Subsequence
______________

### Longest Palindromic Subsequence

[516. Longest Palindromic Subsequence](https://leetcode.com/problems/longest-palindromic-subsequence/)

Think of the recurrence first and then design the loop. 

```cpp
if(s[i] == s[j]) dp[i][j] = 2 + dp[i+1][j-1]; else dp[i][j] = max(dp[i+1][j], dp[i][j-1]);

Now think of how to set the loops for the correct evaluation order.

Base case is dp[i][i] = 1;

// Or answer is just LCS(s, reverse(s))

```


___________

### Longest Palindromic Substring

[5. Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/description/)

```
Dp[i][j] means if string is palindrome;

dp[i][j] == s[i] == s[j] ? (len < 4 || dp[i+1][j-1]) : 0;

```

### Longest Increasing Subsequence

[300. Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/description/)

```
if(A[i] > A[j]) {
    dp[i] = max(dp[i], dp[j] + 1);
}
```

The Greedy Solution is worth reading.
[Greedy Article on LC](https://leetcode.com/problems/longest-increasing-subsequence/solutions/1326308/c-python-dp-binary-search-bit-segment-tree-solutions-picture-explain-o-nlogn/)

```cpp
// With Greedy and bSearch 

int lengthOfLIS(vector<int>& A) {
    int n = A.size();
    vector<int> ss;
    for(int &x:A) {
        if(ss.empty() || ss.back() < x) {
            ss.push_back(x);
        }
        else {
            // Find the number larger than the current and replace
            // To maintain the stack and possible solution ahead.
            auto it = lower_bound(ss.begin(), ss.end(), x);
            *it = x; // Replacing the number
        }
    }
    return ss.size();
}

```

Follow Up : Retrieve the Sequence itself.


_______________

## Matrix Chain Multiplication

### Discuss MCM

Visualization with Example:
Let’s revisit the example with P = [10, 30, 5, 60] and matrices:

A_1: 10 x 30

A_2: 30 x 5

A_3: 5 x 60

Subproblem (1, 3):
We are computing the minimum cost to multiply A_1 x A_2 x A_3.

Possible splits:

k = 1: Split into (A_1) and (A_2 x A_3).

k = 2: Split into (A_1 x A_2) and (A_3).

Cost Calculation:
For k = 1:

Left part: A_1 (dimensions 10 x 30).

Right part: A_2 x A_3 (dimensions 30 x 60).

Cost = P[0] x P[1] x P[3] = 10 x 30 x 60 = 18000.

For k = 2:

Left part: A_1 x A_2 (dimensions 10 x 5).

Right part: A_3 (dimensions 5 x 60).

Cost = P[0] x P[2] x P[3] = 10 x 5 x 60 = 3000.

The minimum cost is 3000, which corresponds to the split k = 2.

Why P[i-1] x P[k] x P[j] Works:
P[i-1]: The number of rows in the resulting matrix from the left part.

P[k]: The number of columns in the resulting matrix from the left part (and rows in the resulting matrix from the right part).

P[j]: The number of columns in the resulting matrix from the right part.

This formula ensures that we correctly account for the dimensions of the matrices being multiplied at each step.

```cpp
// Function to compute the minimum number of scalar multiplications
int matrixChainOrder(vector<int>& p) {
    int n = p.size() - 1; // Number of matrices
    // Create a DP table of size n x n initialized with 0
    vector<vector<int>> dp(n, vector<int>(n, 0));

    // Fill the DP table diagonally
    for (int length = 2; length <= n; length++) { // length is the chain length
        for (int i = 0; i <= n - length; i++) {  // Starting index of the chain
            int j = i + length - 1;              // Ending index of the chain
            dp[i][j] = INT_MAX; // Initialize to a large value

            // Try all possible splits
            for (int k = i; k < j; k++) {
                // Cost of multiplying (A_i...A_k) and (A_k+1...A_j)
                int cost = dp[i][k] + dp[k + 1][j] + p[i] * p[k + 1] * p[j + 1];
                // Update the minimum cost
                if (cost < dp[i][j]) {
                    dp[i][j] = cost;
                }
            }
        }
    }

    // The result is the minimum cost to multiply matrices from 0 to n-1
    return dp[0][n - 1];
}
```
Explanation of the Code:

Input:
The input is a vector p where p[i-1] and p[i] represent the dimensions of matrix A_i.

For example, if p = [10, 30, 5, 60], then:

A_1 is 10 x 30,

A_2 is 30 x 5,

A_3 is 5 x 60.

DP Table:
dp[i][j] stores the minimum number of scalar multiplications needed to compute the product of matrices from A_i to A_j.

Filling the DP Table:
We iterate over all possible chain lengths (length from 2 to n).

For each chain length, we iterate over all possible starting indices (i).

For each starting index, we compute the ending index (j = i + length - 1).

We then try all possible splits (k) and compute the cost of multiplying the two resulting subsequences.

Cost Calculation:
The cost of multiplying the two subsequences is:
dp[i][k] + dp[k+1][j] + p[i] * p[k+1] * p[j+1]

This represents:

The cost of multiplying A_i to A_k,

The cost of multiplying A_k+1 to A_j,

The cost of multiplying the resulting matrices.

Result:
The final result is stored in dp[0][n-1], which represents the minimum cost to multiply all matrices from A_1 to A_n.



________________

## State Machine DP

### Best Time to Buy and Sell Stocks problems

[Best Time to Buy and Sell Stocks](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)

```cpp
/*
For this problem, you can just check what is the minimum before any price. Keep track of max, 
that is the answer

If multiple transactions are allowed -

s0 = freeState
s1 = boughtState

s0[i] = max(s0[i-1], s1[i-1] + A[i-1])
s1[i] = max(s1[i-1], s0[i-1] - A[i-1])

at the end you want to be in s0 state so s0[n] is the answer
*/
int maxProfit(vector<int>& a) {
    int maxProfit = 0;
    int minSoFar = 1e9+7;
    for(int &x:a) {
        minSoFar = min(x, minSoFar);
        maxProfit = max(maxProfit, x-minSoFar);
    }
    return maxProfit;
}
```

[Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/description/)

```cpp
/*
s0 = freeState
s1 = boughtState

s0[i] = max(s0[i-1], s1[i-1] + A[i-1])
s1[i] = max(s1[i-1], s0[i-1] - A[i-1])

at the end you want to be in s0 state so s0[n] is the answer
*/

int maxProfit(vector<int>& p) {
    int n = p.size();
    int maxProfit = 0;
    // you can not sell on first day so bought is INT_MIN or -1*a[0].
    // Otherwise free state is updated to firstDay price, which is wrong.
    int free = 0, bought = INT_MIN;
    for(int i=0; i<n; i++) {
        int cur = bought;
        bought = max(bought, free - p[i]);
        free = max(free, cur + p[i]);
    }
    return free;
}

```

[Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/description/)

& 

[Best Time to Buy and Sell Stock IV](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/description/)

```cpp
/*
This can be generalized for atmost k transactions. 
Article - 
https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/solutions/54125/very-understandable-solution-by-reusing-problem-iii-idea/

The idea is that, the solution for any 

dp[ithPosition][kTransactions] comes from all i (or previous) so far and the (k-1)th transaction.


for(i=1; i<=n; i++) {
    for(t=1; t<=k; t++) {
        // either keep the state same [same k] or increase the transaction from last sell
        buy[i][k] = max(buy[i-1][k], sell[i-1][k-1] - p[i-1]);

        // sell for kth transaction or carry the answer from previous index 
        sell[i][k] = max(sell[i-1][k], buy[i-1][k] + p[i-1]);
    }
}
*/


// This can further be optimized for space
vector<int> buy(K+1, INT_MIN), sell(K+1, INT_MIN);

for(int price:prices) {
    for(int k=1; k<=K; k++) {
        buy[k] = max(buy[k], sell[k-1] - price);
        sell[k] = max(sell[k], buy[k] + price);
    }
}

return sell[k];
```

[Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/description/)

[Best Time to Buy and Sell Stock with Fee](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/description/)


**Note : Read and understand the [Article on LC](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/solutions/108870/most-consistent-ways-of-dealing-with-the-series-of-stock-problems/)**