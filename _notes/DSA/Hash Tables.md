---
name: Hash Tables
updated: 2022-03-19 15:31:41Z
created: 2021-09-27 04:04:11Z
layout: note
categories: DSA
---

# Hash tables

> lot of theory
- direct addressing: having array that have one position for every key possible, key works as index(large space needed or number of keys should be small).
    
- provides constant time operations: insert, delete, seach
    
- `T[k]` contains element with key k, or null if element with key k is not present in the array.
    
- hash table: stored array size proportional to keys actually stored(which is smaller than total number of keys)
    
- keys are not used as index, but index is computed from key with hash functions
    
- if $U$ is universe of keys and $\{0,1,...,m-1 \}$ are indexes, then hash function $h:Y\rightarrow \{0,1,...,m-1 \}$ maps keys to indexes, element with key $k$ hashes to index $h(k)$
    

## Collision resolution with chaining

- collision: when two keys maps to the same index
- chaining: elements with same keys are put in linked list referenced at that index
- because of this, insertion and deletion operations can take $\mathcal{O}(n)$ in worst case(when all of keys map to the same index)
- average case analysis
    - load factor $\alpha =$ number of elements / number of slots(indexes) $= n/m\\ =$ average number of elements stored in a chain
    - in case of uniform hashing(elements are equally likely to go in all m slots) the average case cost is $\mathcal{O}(1+\alpha)$

## Hash functions

- A good hash function satisfies (approximately) the assumption of simple uniform
    hashing: each key is equally likely to hash to any of the m slots, independently of
    where any other key has hashed to
- for example hashed value of keys 'pt' and 'pts' shouldn't be same. Sometimes we might stronger properties like hash values of 'close' keys should be far apart
- hash functions assume that keys are natual numbers(if they're not, then we interpret them as natural numbers)

### Division method

$$
h(k) = k \text{ mod } m

$$

- a good value of m should be chosen, a prime not too close to an exact power of 2 is a good choice

### Multiplication method

- multiply key by a fraction, take its fractional part and multiply with m. Then take the floor of it.

$$
h(k) = \lfloor m(kA \text{ mod } 1) \rfloor,\quad 0<A<1

$$

- advantage: value of m is not critical
- easy computational:
    - let $w$ be word size of machine and $k$ fits in $w$ bits
    - we can take m to be a power of 2 ($2^p$ for some $p$) and restrict A to be a fraction of type $s/2^w$ where $0<s<2^w$
    - then first we multiply $k$ with $s = A\cdot 2^w$ which results in $r_12^w+r_2$
    - $r_2$ represent $kA \text{ mod } 1$(because if we divide the above result by $2^w$, $r_1$ will be integer part and $r_2$ will be fractional part)
    - muliplying $r_2$ with $m=2^p$ is equivalent to taking most significant $p$ bits of it. So most significant $p$ bits of $r_2$ is the hash value
    - Knuth suggests $A\approx (\sqrt{5} - 1)/2=0.6180339...$

### Universal hashing

- hash function chosen randomly each time from a collection of hash functions.
- handler can chose a single hash function for a hash table, rehash the table with another random hash function when there are many collisions etc.
- A collection of hash functions $\mathcal{H}$ is called universal if for a pair of keys $k,l$ the number of hash functions that hash these keys to same value are at most $|\mathcal{H}|/m$
- that means chance of collision of two keys is at most $\frac{|\mathcal{H}|/m}{|\mathcal{H}|}= 1/m$(which is equal to chance of collision of two random keys from set $\{0,1,2,...,m-1 \}$)
- since on each operation, a different hash function will be used, cost of $n$ operations(insert, search delete) is $\Theta(n)$ on hash table containing $\mathcal{O}(m)$ elements
    - probability of collision of two keys is $1/m$
    - expected value of number of keys other than $k$, that hash to to same value as $k$ is $n/m = \alpha$
    - so operations cost $\Theta(\alpha)$
- universal hash functions

let p is a prime number greater than every possible key, $\mathcal{Z}_p = \{0,1,2,...,p-1 \}, \mathcal{Z}_p^* = \{0,1,2,...,p-1 \}$,
so $k\in \mathcal{Z}_p$ and $p>m$

$$
h_{ab}(k) = 	((ak+b) \text{ mod } p) \text{ mod } m\\
\text{}\\
\mathcal{H}_{pm} = \{h_{ab}: a\in\mathcal{Z}_p^* , b\in\mathcal{Z}_p \}

$$

- $\mathcal{H}_{pm}$ contains $p(p-1)$ hash functions
- $\mathcal{H}_{pm}$ is universal

let $k,l\in \mathcal{Z}_p, k\neq l$
for a given hash function $h_{ab}$

$$
r = (ak+b) \text{ mod } p\\
s =(ak+b) \text{ mod } p\\
r-s \equiv a(k-l) (\text{ mod } p)

$$

so $r\neq s$ because $p$ is prime and both $a$ and $k-l$ are nonzero modulo p, so there product is also nonzero modulo p
so pairs of $(k,l)$ and $(r,s)$ have one to one correspondence

the probability that $k$ and $l$ collide is probability that $r\equiv s(\text{mod } m)$ where r and s are random and distinct values modulo
for given $r$, number of values $s$ ($p-1$ total possible), such that $r\equiv s(\text{mod } m)$ is $\lceil p/m\rceil -1$
since $\lceil \frac{a}{b}\rceil \leq \frac{a+(b-1)}{b}$

$$
\lceil p/m\rceil -1 \leq (p-1)/m

$$

the probability of $r$ and $s$ being congruent modulo $m = \frac{(p-1)/m}{p-1} = 1/m$
so probability of collision is $1/m$

## Open Addressing

- instead of following pointers to find an item(as in chaining), we compute the sequence of slots to be examined and look in them.
- the sequence, instead of being order $0,1,2,...,m-1$ which will require linear search time, the sequence depends on the key being inserted. So the hash function here is

$$
h: U \times \{0,1,...,m-1 \} \rightarrow \{0,1,...,m-1 \}

$$

- we also require that for every key $k$, the probe sequence $\langle h(k,0),h(k,1), ..., h(k,m-1) \rangle$ be a permutation of $\langle 0,1,2,..,m-1\rangle$, so every position is considered a slot to insert the new key.

## c string hash

```cpp
//Bob Jenkins
int joaat_hash(char *key, int len){
    int hash = 0, i;
    for (i = 0; i < len; i++){
        hash += key[i];
        hash += (hash << 10);
        hash ^= (hash >> 6);
    }
    hash += (hash << 3);
    hash ^= (hash >> 11);
    hash += (hash << 15);
    return hash;
}
```