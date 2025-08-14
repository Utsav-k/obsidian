### Lambda Expressions in C++

https://www.geeksforgeeks.org/lambda-expression-in-c/

```
[ capture clause ] (parameters) -> return-type{   definition of method}
```

```
// function to sort vector, lambda expression is for sorting in
// non-increasing order Compiler can make out return type as
// bool, but shown here just for explanation
sort(v.begin(), v.end(), [](const int& a, const int& b) -> bool
{
    return a > b;
});

/*
v = {4 1 3 5 2 3 1 7}
after sorting - 
v = {7 5 4 3 3 2 1 1} 
*/
```

```cpp
auto square = [](int i) -> int {
    return i * i;
};
// square(5) returns 25
```

[&] : capture all external variable by reference

[=] : capture all external variable by value

[a, &b] : capture a by value and b by reference

```cpp
// Lambda Example - [&] captures all by reference.

auto addElements = [&](vector<int> a) {
    int s = 0;
    for(int x : a) {
        s += x;
    }
    return s;
};

vector<int> a = {1,2,3,4,5,6};
cout << addElements(a);

// prints 21
```

```cpp
int count_N = count_if(v1.begin(), v1.end(), [=](int a)
{
    return (a >= N);
});
```

```cpp
vector<int> v1 = {3, 1, 7, 9};
vector<int> v2 = {10, 2, 7, 16, 9};

//  access v1 and v2 by reference
auto pushinto = [&] (int m)
{
    v1.push_back(m);
    v2.push_back(m);
};

// it pushes 20 in both v1 and v2
pushinto(20);
```

### Priority Queue

```cpp
// pq with custom comparator using lambda.

auto comapre = [](vector<int> &a, vector<int> &b) -> bool {
    return a[0] * a[0] + a[1] * a[1] < b[0] * b[0] + b[1] * b[1];
};

// using custom comparater in priority queue
priority_queue<vector<int>, vector<vector<int>>, decltype(comapre)> pq(compare);
```

or

```cpp

priority_queue<vector<int>, vector<vector<int>>, compare> pq;

// Method Overriding
struct compare {
    bool operator()(vector<int>& p, vector<int>& q) {
        return p[0] * p[0] + p[1] * p[1] < q[0] * q[0] + q[1] * q[1];
    }
};
```

### Set

```cpp
auto comp = [](int a, int b) {
    return a < b;
};

// init
set<int, decltype(comp)> st;
```

______________
### LC 150 Evaluate Reverse Polish Notation
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
