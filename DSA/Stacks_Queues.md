## Stacks

Stack is a Last In First Out (LIFO) data structure that supports three operations:

push, which adds an element to the top of the stack, pop, which removes an element from

the top of the stack, and top, which retrieves the element at the top without removing it, all

in O(1) time.
___________
```cpp
stack<int> s;
s.push(1); // [1]
s.push(13); // [1, 13]
cout << s.size() << endl; // 2
s.pop(); // [1]
cout << s.top() << endl; // 1
s.pop(); // []
cout << s.size() << endl; // 0
```


_________


### LC 232. Implement Queue using Stacks
Implement a first in first out (FIFO) queue using only two stacks. The implemented queue should support all the functions of a normal queue (push, peek, pop, and empty).

Implement the MyQueue class:

void push(int x) Pushes element x to the back of the queue.
int pop() Removes the element from the front of the queue and returns it.
int peek() Returns the element at the front of the queue.
boolean empty() Returns true if the queue is empty, false otherwise.

```cpp
class MyQueue {
private:
    vector<int> a, b;
public:

    MyQueue() {
        a.clear();
        b.clear();
    }
    
    void push(int x) {
        a.push_back(x);
    }
    
    int pop() {
        move();
        int x = b.back();
        b.pop_back();
        return x;
    }
    
    int peek() {
        move();
        return b.back();
    }
    
    bool empty() {
        return a.empty() && b.empty();
    }

    void move() {
        if(b.empty()) {
            while(!a.empty()) {
                b.push_back(a.back());
                a.pop_back();
            }
        }
    }
};
```
_______________________

### LC 150 Evaluate Reverse Polish Notation - Neet Use of C++ Lambdas
https://leetcode.com/problems/evaluate-reverse-polish-notation/description/

```cpp
class Solution {
public:
    int evalRPN(vector<string>& tokens) {

        // Initialize key->function pairs
        unordered_map<string, function<int (int, int) >> mp = {
            {"+", [&](int a, int b) { return a + b; }},
            {"-", [&](int a, int b) { return a - b; }},
            {"*", [&](int a, int b) { return a * b; }},
            {"/", [&](int a, int b) { return a / b; }}
        };
        vector<int> a;
        for(string &s:tokens) {
            if(mp.find(s) == mp.end()) {
                a.push_back(stoi(s));
            } else {
                int x = a.back();
                a.pop_back();
                int y = a.back();
                a.pop_back();
                // calling the function
                a.push_back(mp[s](y, x));
            }
        }
        return a.back();
    }
};
```
____________
### LC 456. 132 Pattern
https://leetcode.com/problems/132-pattern/
```cpp
class Solution {

public:

    bool find132pattern(vector<int>& a) {
        int n = a.size();
        vector<int> minBefore(n, INT_MAX);
        for(int i=1; i<n; ++i) {
            minBefore[i] = min(minBefore[i-1], a[i-1]);
        }

        stack<int> st;
        /* This is tricky to use the reverse loop.
        need to have a decreasing stack. if a[i] > st.top(), it is
        a candidate for 3 */
        for(int i=n-1; i>=0; --i) {
            if(st.empty()) st.push(i);
            while(!st.empty() && a[i] > a[st.top()]) {
                if(a[st.top()] > minBefore[i]) return true;
                st.pop();
            }
            st.push(i);
        }
        return false;
    }
};
```
_____________
### Largest Histogram Area

```cpp
int largestRectangleArea(vector<int>& a) {
    stack<pair<int,int>> st; // {index, height}
    int n = a.size();
    int ans = 0;
    for(int i=0; i<n; ++i) {
        int h = a[i];
        int pos = i;
        // maintaing a monotonically increasing stack
        while(!st.empty() && h < st.top().second) {
            // Idea here is that the popped value could have been the start of the new rectangle.
            pos = st.top().first;
            ans = max(ans, st.top().second * (i - st.top().first));
            st.pop();
        }
				// set the index to the previously popped value.
        st.push({pos, h});
    }
    // Taking care of the residual stack.
    while(!st.empty()) {
        ans = max(ans, st.top().second * (n-st.top().first));
        st.pop();
    }
    return ans;
}
```
____________
### Longest Valid Parenthesis

https://leetcode.com/problems/longest-valid-parentheses/

```cpp
class Solution {
public:
    /**
     * @brief 
     * Keep track of the last index where the valid string started from.
     * @param string 
     * @return int 
     */
    int longestValidParentheses(string s) {
        stack<int> st;
        st.push(-1);
        int ans = 0;
        for(int i=0; i<s.size(); i++) {
            if(s[i] == '(') {
                st.push(i);
            }
            else {
                st.pop();
                if(st.size() > 0 ) ans = max(ans, i-st.top());
                else {
                    st.push(i);
                }
            }
        }
        return ans;
    }
};
```
_________________
### Max Freq Stack

https://leetcode.com/problems/maximum-frequency-stack/submissions/

Hash map `freq` will count the frequence of elements.
Hash map `m` is a map of stack.
If element `x` has n frequence, we will push `x` n times in `m[1], m[2] .. m[n]maxfreq` records the maximum frequence.

`push(x)` will push `x` to`m[++freq[x]], pop()` will pop from the `m[maxfreq]`

```cpp
class FreqStack {

private:
    map<int,int> freq;
    map<int, stack<int>> group;
    int maxFreq;
    
public:
    FreqStack() {
        freq.clear();
        group.clear();
        maxFreq = 0;
    }
    
    void push(int val) {
        int curFreq = ++freq[val];
        maxFreq = max(maxFreq, curFreq);
        group[curFreq].push(val);
    }
    
    int pop() {
        int res = group[maxFreq].top();
        group[maxFreq].pop();
        freq[res]--;
        if(group[maxFreq].empty()) {
            maxFreq--;
        }
        return res;
    }
};
```
__________
### Trapping Rain Water

https://leetcode.com/problems/trapping-rain-water/

```cpp
class Solution {
public:
    int trap(vector<int>& a) {
        int n = a.size();
        int l = 0, r = n-1;
        int leftMax = 0, rightMax = 0, ans = 0;
        while(l <= r) {
            if(a[l] < a[r]) {
                rightMax = max(rightMax, a[r]);
                ans += max(0, min(leftMax, rightMax) - a[l]);
                leftMax = max(a[l], leftMax);
                ++l;
            } else {
                leftMax = max(leftMax, a[l]);
                ans += max(0, min(leftMax, rightMax) - a[r]);
                rightMax = max(rightMax, a[r]);
                --r;
            }
        }
        return ans;
    }
};
```