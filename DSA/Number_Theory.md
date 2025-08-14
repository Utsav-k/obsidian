### Sieve & Prime Factorization

```cpp
vector<ll> primes;

// Precompute primes till sqrt(N) using sieve

void generatePrimes(ll n) {
    vector<bool> isPrime(n+1, true);
    isPrime[0] = isPrime[1] = false;
    for(ll i=2; i*i<=n; i++) {
        if(isPrime[i]) {
            for(int j=i*i; j<=n; j+=i) {
                isPrime[j] = false;
            }
        }
    }
    for(int i=2; i<=n; i++) {
        if(isPrime[i]) primes.push_back(i);
    }
}

vector<ll> factors(ll x) {
    vector<ll> ans;
    for(ll p:primes) {
        if(p*p > x) break;
        while(x % p == 0) {
            ans.push_back(p);
            x /= p;
        }
    }
    if(x > 1) {
        ans.push_back(x);
    }
    return ans;
}
```

### Binary Exponentiation

```cpp
ll binPowerRecursive(ll a, ll b) {
    if(b==0) return 1;
    ll res = binPowerRecursive(a, b/2);
    if(b & 1) {
        return res * res * a;
    }
    return res * res;
}

ll binPower(ll a, ll b) {
    ll res = 1;
    while(b > 0) {
        if(b & 1) {
            res = res * a;
        }
        a = a * a;
        // Right bit shift. Analogous to b /= 2;
        b >>= 1;
    }
    return res;
}
```

### Modular Subtraction

```cpp
// (a-b) % MOD;
int answer = ((a - b) % MOD + MOD) % MOD;
```

### Modular Division \[Inverse Modular\]

- Calculate (a / b) % M; Where M is prime.
    
    **Euluer’s Theorem**
    
    ```cpp
    int divide(int a, int b) {
        return a * binExponent(b, P-2) % P;
    }
    
    // b^-1 % P = b ^ (P-2) % P;
    ```
    

### Euclidean GCD

- gcd(a, b) = gcd (a-b, b)
    - Let g be the GCD then a%g = 0, b %g = 0
    - (a-b)%g = ((a%g) - (b%g)) % g = 0, so g | (a-b)
- Mod is repeated subtraction, so gcd(a, b) = gcd(a%b, b)
- Generally assume a ≥ b, so we use gcd(a,b) = gcd(b, a%b)
- gcd(x, 0) = x, base case
- O(log(min(a,b)))
- if a, b are coprime then, gcd(a, b) = 1;
- **gcd(a, b, c ….) = gcd (gcd(a,b) ,c…)**

```cpp
// calculate the gcd of a,b
int gcd(int a, int b) {
    if(b == 0) {
        return a;
    }
    return gcd(b, a%b);
}

int gcdIterative(int a, int b) {
    while(b) {
        a %= b;
        swap(a, b);
    }
    return a;
}
```

### Bezout’s Identity

if gcd(a, b) == 1

- If gcd(a, b) == 1; there exists x, y such that xa + by = 1;
- in general there exists x, y such that ax + by = gcd(a, b);

### Extended Euclidean

- solves : ax+by = 1 \[a, b given; gcd(a, b) = 1\]
- generally : ax + by = gcd(a, b)
- Bezout’s identity says sol (x, y) will always exist.
- can get this by writing euclidean in reverse.
- ax + by = g;
- b.x1 + (a mod b).y1 = g;

### Chinese Remainder Theorem

- Given a, b, x, y \[0 ≤ a < x; 0 ≤ b < y; x, y coprime\]
- Theorem states that there exists unique z where :
    - z % x = a
    - z % y = b
- How to find Z?
- z = cx + a;
- (cx + a) % y = b;
- cx % y = (b - a) % y;
- dx % y = 1
- z = d (b-a)x + a; where d = (1/x)%y

### Instance of Mobius