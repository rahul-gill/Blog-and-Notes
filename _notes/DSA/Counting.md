---
name: Counting
updated: 2022-03-19 15:32:00Z
created: 2021-09-24 15:24:39Z
layout: note
categories: DSA
---

# Counting
- **basic counting principle**: $r$ stages with $n_i$ choices at state $i$; the total number of choices are $n_1\cdot n_2\cdot n_3\cdot ...\cdot n_r$
- **permutations**: number of ways of ordering the n items: $n!$
- number of  ordered sequences of k elements from an n-element set is $\frac{n!}{(n-k)!}$
- **combinations** $\binom{n}{k}$: number of k element subsets of n-element set
$$\binom{n}{k} = \frac{n!}{k!(n-k)!}
$$
- these also called **binomial coefficients** because
$$(a+b)^n =  \sum\limits_{k=0}^n{\binom{n}{k}a^kb^{n-k}}
$$
-  total number of subsets = $\sum\limits_{k=0}^n{\binom{n}{k}} = 2^n$
<br>
- other properties of it
	- $\binom{n}{k} = \binom{n}{n-k}$
	- $\binom{n}{k} = \frac{n}{k}\binom{n-1}{k-1}$ 
- $n$ distinct items; $r$ persons; $i^{th}$ person given $n_i$ items, then number of partitions(multinomial coefficient)
$$ \text{number of partitions}= \frac{n!}{n_1!\cdot n_2!\cdot ...\cdot n_r!}
$$

## Largest power of $k$, $x$ such that $n!$ is divisble by $k^x$
- for example for $k=2$ and $n! = 1\times2\times3\times4\times5\times6\times7\times8\times9$
	2 occurs in $n!$, $1+2+1+3=7$ times
- general answer for $k$ being prime is $\lfloor\frac{n}{k}\rfloor + \lfloor\frac{n}{k^2}\rfloor +...+\lfloor\frac{n}{k^i}\rfloor+...$ 
- this sum is finite and only approximately first $\log_k n$ are non-zero, so runtime is $\mathcal{O}(\log_k n)$ 
```cpp
int fact_pow_prime (int n, int k) {
	int res = 0;
	while (n) {
		n /= k;
		res += n;
	}
	return res;
}
```
- for composite numbers we first calculate its prime divisors $k=k_1^{p_1}\cdot_2^{p_2}\cdot...k_m^{p_m}$ and for each prime $k_i$ we do the above `fact_pow_prime` procedure and lets call its return value $a_i$.
	then answer for $k$ being composite is $\min\limits_{i=1...m}\frac{a_i}{p_i}$
- we can modify the prime divisor procedure this way
```cpp
vector<pair<long long, int>> primeFactors(long long n) {
	vector<pair<long long, int>> factors;
	for (int i = 0; i < primes.size() && primes[i]*primes[i] <= n; ++i){
		bool first = 1;
		while (n % primes[i] == 0) {
			n /= primes[i];
			if(first == 1) factors.push_back({primes[i], 1});
			else ++(factors.back().second);
			first = 0;
		}
	}
	if (n != 1) factors.push_back({n,1});         
	return factors;
}
//in main
sieve()
auto factors = primeFactors(k);
int ans = INT_MAX;
for(int i=0; i<factors.size(); ++i)
	ans = min(ans, fact_pow_prime(n, factors[i].first) / factors[i].second);
cout<<ans<<'\n';
```

# Inclusion-exclusion principle
- relation between number of elements in sets and their unions and intersections
$$\left| \bigcup_{i=1}^n A_i \right| = \sum_{1\leq i\leq n}|A_i| - \sum_{1\leq i<j\leq n} |A_i \cap A_j| + \sum _{1\leq i<j<k\leq n}|A_i \cap A_j \cap A_k| - \cdots + (-1)^{n-1} | A_1 \cap \cdots \cap A_n |$$
<img src="../_resources/33f7df8f8abc28f818629f3e79facb12.png" style="width:200px; height:200px">

$$S(A \cup B \cup C) = S(A) + S(B) + S(C) - S(A \cap B) - S(A \cap C) - S(B \cap C) + S(A \cap B \cap C)$$
- generalization:number of elements which are present in exactly $r$ sets out of total $n$ sets
$$\left|\bigcup_{|B|=r}\left[\bigcap_{i \in B} A_i \cap \bigcap_{j \not\in B} \overline{A_j}\right]\right|=\sum_{m=r}^n (-1)^{m-r}\dbinom{m}{r} \sum_{|X|=m} \left|\bigcap_{i \in X} A_{i}\right|$$
- Task: count how many permutations of numbers from 0 to 9 exist such that the first element is greater than 1 and the last one is less than 8. 
ans: $10!-(2\times9!+1\times 9! - 2\times2\times8!)$
- Task: count how many sequences of length $n$ exist consisting only of numbers 0,1,2 such that each number occurs at least once.
	if $A_i(i=0,1,2)$ denotes sets of sequences in which digit i not occur then $\vert A_0\cup A_1\cup A_2\vert$ will be the answer. Here every $\vert A_i\vert=2^n, \vert A_i\cap A_j \vert=1$ and $\vert A_i\cap A_j\cap A_k \vert=0$
	so answer is $3^n-(3\times 2^n - 3\times 1 + 0)$
- Task: number of integer solutions to the equation $x_1+x_2+x_3+x_4+x_5+x_6=20$ and $0\leq x_i\leq 8$