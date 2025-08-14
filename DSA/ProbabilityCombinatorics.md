## Combinatorics

### Binomial Coefficient

aCb = a! / b!(a-b)!

Problem Link - https://cses.fi/problemset/task/1079/

```cpp
ll mod = 1e9 + 7;
const ll limit = 1e6 + 5;
ll f[limit];
ll iv[limit];

ll binExponent(ll a, ll b) {
    ll ans = 1;
    while(b > 0) {
        if(b & 1) {
            ans = (ans * a) % mod;
        }
        a = (a * a) % mod;
        b >>= 1;
    }
    return ans;
}

void precompute() {
    f[0] = 1;
    for(int i=1; i<limit; i++) {
        f[i] = (i * f[i-1]) % mod;
    }
    iv[0] = iv[1] = 1;
    for(int i=2; i<limit; ++i) {
        iv[i] = binExponent(f[i], mod - 2);
    }
}

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    precompute();
    int t;
    cin >> t;
    while(t--) {
        ll a, b;
        cin >> a >> b;
        ll ans = (((f[a] * iv[b]) % mod) * iv[a-b]) % mod;
        cout << ans <<  "\n";
    }
    cout << endl;
    return 0;
}
```

### Distributing Apples

**x1 + x2 + x3 + x4 + ………….. + xn = m; where 0 ≤ xi ≤ m;**

**Number of ways to do it = m+n-1(C)n-1** 

### Derangements

a1, a2, …………., an

b1, b2, …………., bn

Number of ways such that for **each i, ai is not mapped to bi**

```cpp
// Recurrence
D(n) = (n-1) * (D(n-1) + D(n-2))
```

### Catalan Numbers

Nth catalan number - 

```cpp
Cn = (1/(n+1))(2nCn)
```

### Number of Balanced Sequences

(()()()) - balanced string

For the string of length 2n, the number of balanced sequence is = 1/(n+1) * 2nCn

If there are k types of brackets allowed then the number of such strings = 1/(n+1) * (2nCn.k^n)

Using Dynamic Programming 

Let **dp[n]** be the number of regular bracket sequences with pairs of bracket. Note that in the first position there is always an opening bracket. And somewhere later is the corresponding closing bracket of the pair. It is clear that inside this pair there is a balanced bracket sequence, and similarly after this pair there is a balanced bracket sequence. So to compute dp[n], we will look at how many balanced sequences of ***i*** pairs of brackets are inside this first bracket pair, and how many balanced sequences with ***n-1-i*** pairs are after this pair. Consequently the formula has the form

```cpp
for(i=0; i<n; i++) {
	dp[n] += dp[i].dp[n-1-i];
}
```

### Find Number of Ways with Mirroring

(x, y) ⇒ (m, n), with restriction being y = -p
