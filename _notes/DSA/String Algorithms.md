---
name: String Algorithms
updated: 2022-04-07 17:50:31Z
created: 2022-04-06 10:37:17Z
layout: note
categories: DSA
---

# Comparing strings efficiently with hashing
- just comparing characters of each string: $\mathcal{O}(s1Size, s2Size)$
- instead compare $hash(s1)$ and $hash(s2)$ in $\mathcal{O}(1)$(hash function return an integer)
- if `hash(s1) != hash(s2)` then `s1 != s2` but `hash(s1) == hash(s2)` doesn't always mean `s1 == s2` . This is because we want to compare in  $\mathcal{O}(1)$ and for that we keep the output of hash function in fixed integer range $[0, m)$
- polynomial rolling hash of a string `s` of length `n` is  

$$
\text{hash}(s) = s[0] + s[1] \cdot p + s[2] \cdot p^2 + ... + s[n-1] \cdot p^{n-1} \mod m \\
= \sum_{i=0}^{n-1} s[i] \cdot p^i \mod m,
$$
where $p$ and $m$ are chosen positive numbers
- $p$ is usually taken roughly equal to number of possible characters appearing in string
- good choice for $m$ is some large prime number. for example $10^9+9$

```cpp
long long rolling_hash(string const& s) {
	//we can precompute powers of p
	const int p = 31;
	const int m = 1e9 + 9;
	long long hash_value = 0;
	long long p_pow = 1;
	for (char c : s) {
		hash_value = (hash_value + (c - 'a' + 1) * p_pow) % m;
		p_pow = (p_pow * p) % m;
	}
	return hash_value;
}

```

## Fast hash calculation of substrings of given string
- Given a string $s$ and indices $i$ and $j$, find the hash of the substring  $s[i...j]$.
$$\text{hash}(s[i \dots j]) = \sum_{k = i}^j s[k] \cdot p^{k-i} \mod m$$

Multiplying by $p^i$ gives:

$$
\text{hash}(s[i \dots j]) \cdot p^i = \sum_{k = i}^j s[k] \cdot p^k \mod m \\
= \text{hash}(s[0 \dots j]) - \text{hash}(s[0 \dots i-1]) \mod m
$$
- So by knowing the hash value of each prefix of the string , we can compute the hash of any substring directly using this formula.
- since our goal is comparing string we can skip the part where we multiply right side by modular multiplicative inverse of $p^i$

## Counting number of unique substring in $\mathcal{O}(n^2\log n)$
```cpp
int count_unique_substrings(string const& s) {
	int n = s.size();

	const int p = 31;
	const int m = 1e9 + 9;

	//precompute powers of p
	vector<long long> p_pow(n);
	p_pow[0] = 1;
	for (int i = 1; i < n; i++)
		p_pow[i] = (p_pow[i-1] * p) % m;

	//hashes of prefixes
	vector<long long> h(n + 1, 0);
	for (int i = 0; i < n; i++)
		h[i+1] = (h[i] + (s[i] - 'a' + 1) * p_pow[i]) % m;

	int cnt = 0;
	for (int l = 1; l <= n; l++) {
		set<long long> hs;
		for (int i = 0; i <= n - l; i++) {
			long long cur_h = (h[i + l] + m - h[i]) % m;
			//need to understand this part
			cur_h = (cur_h * p_pow[n-i-1]) % m;
			hs.insert(cur_h);
		}
		cnt += hs.size();
	}
	return cnt;
}
```

## Rabin Karp Algorithm
```cpp
vector<int> rabin_karp(string const& s, string const& t) {
	const int p = 31; 
	const int m = 1e9 + 9;
	int S = s.size(), T = t.size();
	
	//precompute powers of p
	vector<long long> p_pow(max(S, T)); 
	p_pow[0] = 1; 
	for (int i = 1; i < (int)p_pow.size(); i++) 
		p_pow[i] = (p_pow[i-1] * p) % m;

	//calculating hashes of prefixes of t
	vector<long long> h(T + 1, 0); 
	for (int i = 0; i < T; i++)
		h[i+1] = (h[i] + (t[i] - 'a' + 1) * p_pow[i]) % m; 
	
	//calculating hash of pattern
	long long h_s = 0; 
	for (int i = 0; i < S; i++) 
		h_s = (h_s + (s[i] - 'a' + 1) * p_pow[i]) % m; 

	vector<int> occurences;
	for (int i = 0; i + S - 1 < T; i++) { 
		long long cur_h = (h[i+S] + m - h[i]) % m; 
		if (cur_h == h_s * p_pow[i] % m)
			occurences.push_back(i);
	}
	return occurences;
}

```

# Prefix function and KMP Algorithm

## Prefix function
- prefix function of a string of length $n$ is an array $\pi[n]$ where
	$\pi[i] =$ longest proper prefix(not equal to string itselft) of substring $s[0\dots i]$ which is also a suffix of that substring 
	or 
$$\pi[i] = \max_ {k = 0 \dots i-1} \{k : s[0 \dots k] = s[i-k \dots i] \}$$

- in naive algorithm of prefix function computation algorithm, we'll check for every substring its prefix function which is $\mathcal{O}(n)$; optimizations:
- first: the value of prefix function can only increase by at most one means $\pi[i+1] \leq \pi[i]+1$
	$$\underbrace{\overbrace{s_0 ~ s_1 ~ s_2 }^{\pi[i] = 3} ~ s_3}_{\pi[i+1] = 4} ~ \dots ~ \underbrace{ ~ \overbrace{s_{i-2} ~ s_{i-1} ~ s_{i}}^{\pi[i] = 3} ~ s_{i+1}}_{\pi[i+1] = 4}$$
	
- second: 
	$$\overbrace{s_0 ~ s_1 ~ s_2}^{\pi[i]} ~ s_3 ~ \dots ~ \overbrace{s_{i-2} ~ s_{i-1} ~ s_{i}}^{\pi[i]} ~ \overbrace{s_{i+1}}^{s_3 = s_{i + 1}}$$
	- here we already have $\pi[i]$ and the next element after the prefix is $s[\pi[i]]$
	- now if $s[\pi[i]] = s_{i+1}$ then $\pi[i+1] = \pi[i]+1$
	- else if $s[\pi[i]] \neq s_{i+1}$ we found next best prefix == suffix length $k$($s[0\dots k-1] = s[i-(k-1)\dots i]$) and check if $s[k] = s[i]$. 
	- if its true then $\pi[i+1] = k+1$ else we repeat this process until $k=0$ 
	- If then $s[i+1] = s[0]$, we assign $\pi[i+1] = 1$, and $\pi[i+1] = 0$ otherwise.
	- How to find $k$ from previous best length $j$(in begininng $j=\pi[i]$):	
	$$\overbrace{\underbrace{s_0 ~ s_1}_{k} ~ s_2 ~ s_3}^j ~ \dots ~ \overbrace{s_{i-3} ~ s_{i-2} ~ \underbrace{s_{i-1} ~ s_{i}}_k}^j ~s_{i+1}$$
	here $s[j-k\cdots j]=s[i-k\cdots i]$ so finding longest matching prefix suffix with length $k$ in $s[0\cdots i]$ is similar to that in $s[0\cdots j]$ so $k =\pi[j-1]$
- implementation
```cpp
vector<int> prefix_function(const string& s) {
    int n = (int)s.size();
    vector<int> pi(n);
    for (int i = 1; i < n; i++) {
        int j = pi[i-1];
        while (j > 0 && s[i] != s[j])
            j = pi[j-1];
        if (s[i] == s[j])
            j++;
        pi[i] = j;
    }
    return pi;
}
```

## Knuth Morris Pratt Algorithm
- problem: Given a text $t$ and a string $s$, we want to find and display the positions of all occurrences of the string $s$ in the text $t$ . length of $s$ = $n$, length of $t$ = $m$
- solution with prefix function: we generate the string $s + \# + t$ and compute the prefix function for it. then if at position $i$ of it we have $\pi[i] = n$ then the string $s$ appears in $t$ at position $i - (n + 1) - n + 1 = i - 2n$
- since $\pi[i]$ doesn't exceeds $n$ so the KMP Algorithm solves it in $\mathcal{O}(n+m)$ in time and $\mathcal{O}(n)$ in memory
```cpp
vector<int> kmp(const string& t,const string& s){
	vector<int> ret;
	vector<int> pi = prefix_function(s);
	int j = 0;
	int n = (int)s.size();
	
	for(int i=0; i< t.size(); i++){
		while (j > 0 && s[j] != t[i] )
			j = pi[j-1];
		if(s[j] == t[i])
			j++;
			
		if(j == n)
			ret.push_back(i - (n-1));
	}
	return ret;
}
```