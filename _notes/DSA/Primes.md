---
name: Primes
updated: 2022-03-19 15:33:30Z
created: 2022-03-19 15:33:02Z
layout: note
categories: DSA
---

- prime number theorem: if $\pi(x)$ represents number of primes less than or equal to x then

$$
\lim\limits_{x\to \infty} \frac{\pi(x)}{x/\log(x) }=1 \quad\text{or}\quad \pi(x)\sim \frac{x}{\log x}

$$

- this is equivalent to saying that for $n^{th}$ prime number $p_n$
    $p_n\sim n\log n$

# sieve of eratosthenes

```cpp
int n;
vector<bool> is_prime(n+1, true);
is_prime[0] = is_prime[1] = false;
for (int i = 2; i <= n; i++) {
    if (is_prime[i] && (long long)i * i <= n) {
        for (int j = i * i; j <= n; j += i)
            is_prime[j] = false;
    }
}  
```

algorithm performs $n/p$ operations for every prime $p\leq n$
number of primes less than or equal to n approx. $n/\ln n$
nth primes is approx. $n\ln n$
so algorithm complexity

$$
\sum_{\substack{p \le n, \\ p \text{ prime}}} \frac n p = n \cdot \sum_{\substack{p \le n, \\ p \text{ prime}}} \frac 1 p.

$$

$$
\sum_{\substack{p \le n, \\ p \text{ prime}}} \frac 1 p \approx  \sum_{k = 2}^{\frac n {\ln n}} \frac 1 {k \ln k}.

$$

$$
\sum_{k = 2}^{\frac n {\ln n}} \frac 1 {k \ln k} \approx \int_2^{\frac n {\ln n}} \frac 1 {k \ln k} dk.

$$

$$
\int_2^{\frac n {\ln n}} \frac 1 {k \ln k} dk = \ln \ln \frac n {\ln n} - \ln \ln 2 = \ln(\ln n - \ln \ln n) - \ln \ln 2 \approx \ln \ln n.

$$

$$
so\: \sum_{\substack{p \le n, \\ p\ is\ prime}} \frac n p \approx n \ln \ln n + o(n).

$$

- Optimizations:
    1\. to find primes till n we only need to traverse till $\sqrt n$
    2\. sieving odd numbers only
    3\. `vector<char>` consume more memory(8x) but `vector<bool>` has read/write overhead; with usual sieve, `vector<bool>` is faster because we're limited by how fast we load in cache. But with segmented sieve, `vector<char>` gives advantage.

# Segmented Sieve

- we count all primes blockwise for cache efficiency

```cpp
int count_primes(int n) {
    const int S = 10000;//block size
    vector<int> primes;
    int nsqrt = sqrt(n);
    vector<char> is_prime(nsqrt + 2, true);
    
    for (int i = 2; i <= nsqrt; i++) {
        if (is_prime[i]) {
            primes.push_back(i);
            for (int j = i * i; j <= nsqrt; j += i)
                is_prime[j] = false;
        }
    }

    int result = 0;
    vector<char> block(S);
    for (int k = 0; k * S <= n; k++) {
        fill(block.begin(), block.end(), true);
        int start = k * S;
        for (int p : primes) {
            int start_idx = (start + p - 1) / p;
            int j = max(start_idx, p) * p - start;
            for (; j<S;  j += p)
                block[j] = false;
        }
        if (k == 0)
            block[0] = block[1] = false;
        for (int i = 0; i < S && start + i <= n; i++) {
            if (block[i])
                result++;// or do whatever processing needed
        }
    }
    return result;
}
```

- chosing the starting index to mark `false` in the block:

```cpp
int start_index = (start/p)*p;
if(start_index < start) start_index += p;
int j = max(start_index,p*p) - start;
//this can be translated to
int start_idx = (start + p - 1) / p;
int j = max(start_idx, p) * p - start;
```

- primes in a range: just modify the second part of segmented sieve

```cpp
vector<char> segmentedSieve(long long L, long long R) {
    // generate all primes up to sqrt(R)
    long long lim = sqrt(R);
    vector<char> mark(lim + 1, false);
    vector<long long> primes;
    for (long long i = 2; i <= lim; ++i) {
        if (!mark[i]) {
            primes.push_back(i);
            for (long long j = i * i; j <= lim; j += i)
                mark[j] = true;
        }
    }

    vector<char> isPrime(R - L + 1, true);
    for (long long i : primes)
        for (long long j = max(i * i, (L + i - 1) / i * i); j <= R; j += i)
            isPrime[j - L] = false;
    if (L == 1)
        isPrime[0] = false;
    return isPrime;
}
```

- primes in a range with not pre generating primes; cost $\mathcal{O}((R-L-1)\log R)$, but fast enough in practice

```cpp
vector<char> segmentedSieveNoPreGen(long long L, long long R) {
    vector<char> isPrime(R - L + 1, true);
    long long lim = sqrt(R);
    for (long long i = 2; i <= lim; ++i)
        for (long long j = max(i * i, (L + i - 1) / i * i); j <= R; j += i)
            isPrime[j - L] = false;
    if (L == 1)
        isPrime[0] = false;
    return isPrime;
}
```

# Primality test and prime factors

```cpp
static const long long N;// max value of n

vector<bool> isPrime(N+1, true);
vector<int> primes;
void sieve(){
    isPrime[0] = 0;
    isPrime[1] = 0;
		int sqrtN = (int)sqrt(N);// only need sqrt(n) primes check
    for (int i = 2; i <= sqrtN; i++){
        if(isPrime[i]){
            for (long long k = (long long)i * i; k <= sqrtN; k += i)
                isPrime[k] = 0;
            primes.push_back(i);
        }
    }
}
bool isPrime(long long n){
	if (n <= N) return isPrime[n];
  for (int i = 0; i < primes.size() && primes[i]*primes[i] <= n; ++i)
    if (n%primes[i] == 0) return false;
  return true;
}
vector<long long> primeFactors(long long n) {
  vector<long long> factors;
  for (int i = 0; i < primes.size() && primes[i]*primes[i] <= n; ++i)
    while (n % primes[i] == 0) {
      n /= primes[i];
      factors.push_back(primes[i]);
    }
  if (n != 1) factors.push_back(n);         
	return factors;
}
```
- number of different prime factors
```cpp
int nDifferentPrimeFactors(long long n) {
	int ans = 0;
  for (int i = 0; i < primes.size() && primes[i]*primes[i] <= n; ++i){
	  if(n % primes[i] == 0) ++ans;
    while (n % primes[i] == 0) n /= primes[i];
  }
  if (n != 1) ++ans;         
	return ans;
}
```
- number of divisors: if prime factorization of $n$ is $n = a^i\cdot b^j\cdot ... \cdot c^k$ then number of divisors of $n$ is $(i+1)\cdot(j+1)\cdot...\cdot(k+1)$
	also sum of divisors is $\frac{a^{i+1}-1}{a-1}\times\frac{b^{j+1}-1}{b-1}\times ... \times\frac{c^{k+1}-1}{c-1}$
```cpp
int nUniqueDivisors(long long n){
    int ans = 1;
    for(auto x: primes){
        if(x * x > n)
            break;
        int power = 0;
        while( n % x == 0){
            n /= x;
            ++power;
        }
        ans *= (power + 1);
    }
    if(n != 1)
        ans = 2;
    return ans;
}
```
- euler totient of $n$: number of positive integers $<n$ that are relatively prime to $n$
$\varphi(n) = n\prod\limits_{p\vert n}\left(1 - \frac{1}{p}\right)$

```cpp
long long EulerPhi(long long N) {
	long long ans = n;
	for (int i = 0; i < primes.size() && primes[i]*primes[i] <= n; ++i)
		if (n % primes[i] == 0) ans -= ans / PF;
		while (n % primes[i] == 0) n /= primes[i];
	}
	if (n != 1) ans -= ans / n;
	return ans;
}

```
