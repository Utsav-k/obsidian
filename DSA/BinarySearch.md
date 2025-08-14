# Binary Search
## Resources
Topcoder Article For Basics - https://www.topcoder.com/thrive/articles/Binary%20Search

Leetcode (Problem Focused) - https://leetcode.com/discuss/study-guide/786126/Python-Powerful-Ultimate-Binary-Search-Template.-Solved-many-problems

### Implementation

```cpp
int binSearch(vector<int> a, int x) {
    int r = a.size() - 1;
    int l = 0;
    while(l <= r) {
        mid = l + (r-l)/2;
        if(a[mid] == x) {
            return mid;
        } 
        else if (a[mid] < target) {
            l = mid + 1;
        }
        else {
            r = mid - 1;
        }
    }
    return -1;
}
```

### Implementation of Discrete Algorithm

p(x) = [0 0 0 0 1 1 1 1]

1. Finding first True

```cpp
binary_search(lo, hi, p):
  while lo < hi:
  mid = lo + (hi - lo) / 2
if p(mid) == true:
  hi = mid
else:
  lo = mid + 1
if p(lo) == false:
  complain // p(x) is false for all x in S!

return lo // lo is the least x for which p(x) is true
```

2. Finding last False

```cpp
// warning: there is a nasty bug in this snippet!
binary_search(lo, hi, p):
  while lo < hi:
  mid = lo + (hi - lo + 1) / 2 // important change
if p(mid) == true:
  hi = mid - 1
else:
  lo = mid

if p(lo) == true:
  complain // p(x) is true for all x in S!

return lo // lo is the greatest x for which p(x) is false
```

### Lower/Upper Bound

**Lower Bound**

- **Find first value ≥ X**
- x = 4
- a = 2, 3, 5, 6, 8, 10, 12;
- lower_bound(x) is i = 2, a[i] = 5;

```cpp
auto lower = lower_bound(a.begin(), a.end(), target);
```

**Upper Bound** 

returns an iterator to the first element *greater* than a certain value

### Sqrt

```cpp
int mySqrt(int x) {
    int r = x;
    int l = 0;
    int ans = -1;
    while(l <= r) {
        int m = l + (r-l)/2;
        if((long long) m*m <= x) {
            ans = m;
            l = m + 1;
        }
        else {
            r = m - 1;
        }
    }
    return ans;
}
```

### Search in Rotated Array

```cpp
// +inf -inf concept, if target and mid are on same side, proceed. 
// Otherwise, target becomes -inf or +inf
// If target is say in the left half, then when searching we need to make the 
// numbers
// [12, 13, 14, 15, 16, 17, inf, inf, inf, inf, inf, inf, inf, inf]
// if target is in right half then we need to make it as
// [-inf, -inf, -inf, -inf, -inf, -inf, 0, 1, 2, 3, 4, 5, 6, 7]

int binSearchInRotatedArray(vector<int> &a, int target) {
    int l = 0, r = nums.size()-1;
            while(l <= r)
            {
                int mid = (r - l)/2 + l;
                int comparator = nums[mid];
                // Checking if both target and nums[mid] are on same side.
                if((target < nums[0]) && (nums[mid] < nums[0]) || (target >= nums[0]) && (nums[mid] >= nums[0]))
                    comparator = nums[mid];
                else
                {
                    // Trying to figure out where nums[mid] is and making 
                    // comparator as -INF or INF
                    if(target <nums[0])
                        comparator = -INFINITY;
                    else 
                        comparator = INFINITY;

                }
                if(target == comparator) return mid;
                if(target > comparator)            
                    l = mid+1;            
                else
                    r = mid-1;

            }
            return -1;
}
```

### Find min in Rotated Sorted Array

```cpp
/* 
Just compare the mid with last and decide the search space, 
If comparing with first, needs the extra validation in case there are no rotations.
*/
int findMinInRotatedArray(vector<int>& nums) {
    int n = nums.size();
    int l = 0, r = n-1;
    int last = nums.back();
    while(l < r) {
        int mid = l + (r-l)/2;
        if(nums[mid] > last) {
            l = mid + 1;
        }
        else {
            r = mid;
        }
    }
    return nums[l];
}
```


## Practice Problems
LC1011 - https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/description/

LC875 - https://leetcode.com/problems/koko-eating-bananas/description/

LC1283 - https://leetcode.com/problems/find-the-smallest-divisor-given-a-threshold/description/

LC1482 - https://leetcode.com/problems/minimum-number-of-days-to-make-m-bouquets/description/

LC74 - https://leetcode.com/problems/search-a-2d-matrix/description/

LC719 - https://leetcode.com/problems/find-k-th-smallest-pair-distance/description/ 

LC668 - https://leetcode.com/problems/kth-smallest-number-in-multiplication-table/solutions/
