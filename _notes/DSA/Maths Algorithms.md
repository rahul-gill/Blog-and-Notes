---
name: Maths Algorithms
updated: 2022-03-19 15:33:50Z
created: 2021-07-15 07:26:09Z
layout: note
categories: DSA
---

${toc}

# Binary Exponentiation

- index shown in binary
    $x^{10011010} = x^{10000000}\cdot x^{10000}\cdot x^{1000}\cdot x^{10}$
    $\text{so } x^n = \begin{cases} 1, & n==0\\ (a^{\frac{n}{2}})^ 2& n>0 \text{ and } n \text{ even}\\ (a^{\frac{n-1}{2}})^2 & n>0 \text{ and } n \text{ odd} \end{cases}\\ $
- implementation

```cpp
//calculating (base^index) mod m
//for (base^index) without mod, remove all the applied mods in algorithm
//after ith iteration: base = base^i, index = index/2^i, if index's lowest bit is on, result is multiplied by base^i
long long binexp(long long base, long long index, long long m){
    base %= m;
    long long res = 1;
    while(index > 0){
        if(index & 1) res = (res * base) % m;
        base = (base * base) % m;
        index >>= 1;
    }
    return res;
}
```

# Euclidian Algorithm for GCD & LCM O(log<sub>10</sub>min(a,b))

```cpp
//use __gcd(c++17 has both inbuilt gcd() and lcm() in numeric header)
long long gcd(long long a, long long b){
    while(b){
        a %= b;
        swap(a,b);
    }
    return a;
}
long long lcm(long long a, long long b){ return (a / gcd(a,b)) * b; }
//recusrsive version
long long gcd(long long a, long long b){return gcd(b,a%b);//assuming a>b}
```

## time complexity proof

lets two sequences $a = \{ a_k,a_{k-1},...,0 \}$ and $b = \{ b_k,b_{k-1},...,0 \}$ where $a_{k-i}$ and $b_{k-i}$ are values of $a$ and $b$ after $i$ iterations; $0\leq i\leq k$. Also let $D = gcd(A,B)$. With that we have:

- $a_k = A, b_k =B$
- $\forall i: 1\leq i \leq k,$ we have
    - $a_{i-1} = b_i$
    - $b_{i-1} = a_i \text{ mod } b_i$
- $a_0 = D, b_0 = 0$

because $a_{i-1} = b_i$ we can remove notation of $a$ replacing $(1)$ $a_0$ with $b_1$, $(2)$ $a_k$ with $b_{k+1}$ and $(3)$ $a_i$ with $b_{i+1}$

- $b_k = B, b_{k+1} + A$
- $\forall i: 1\leq i \leq k,$ we have
    - $b_{i-1} = b_{i+1} \text{ mod } b_i$
- $b_1 = D, b_0 = 0$

now let $p_i = b_{i+1}/b_i\text{(quotient part)}, \quad \forall i: 1\leq i \lt k$, so that
$b_{i+1} = b_{i-1} + b_i\cdot p_i$
fibonacci numbers: $F_{i+1} =F_{i-1} + F_i$
its clear that $p_i>0$ because $b_{i+1} > b_i$, so for same starting values of $b$ and $F$, $b$ will terminate faster. Since grown of fibonacci numbers is $\mathcal{O}(\log_\phi N)$. so this algorithms time complexity is $\mathcal{O}(\text{number of digits in smaller number})$

- Concretely it can be shown by induction that if this algorithm requirs $N$ steps with $a>b>0$ then $a\geq F_{N+2}, b\geq F_{N+1}$

## Stein's GCD algorithm(removes slower modulo operations)

- if both are even remove both's 2<sup>i</sup>
- if one in even and other odd, remove even's 2<sup>i</sup>
- if both are odd then bigger = bigger - smaller

```cpp
long long gcd(long long a, long long b) { 
    if (!a || !b) 
        return a | b; 
    unsigned long long shift = __builtin_ctzll(a | b); 
    a >>= __builtin_ctzll(a); 
    do { 
        b >>= __builtin_ctzll(b); 
        if (a > b) 
            swap(a, b); 
        b -= a; 
    } while (b); 
    return a << shift; 
}
```

## Extended Euclidian Algorithm

find $x$ & $y$ such that $ax+by = gcd(a,b)=g$; existence of solution is guaranteed by [B??zout's lemma](https://brilliant.org/wiki/bezouts-identity/).

- recursive implementation

since the Euclidian algorithm ends with $b=0; a=g$ we can start with $x=1,y=0$($g\cdot 1+0\cdot 0=g$) and go backwards until we get the original $a$ and $b$.
for that we have to figure how $(x,y)$ change to $(x_1,y_1)$ with transition$(a,b)$ to $(b, a\text{ mod } b)$. We have
$a\cdot x + b\cdot y = g$
$b\cdot x_1 + (a-\lfloor \frac{a}{b}\rfloor)\cdot y = g$
doing some hard work we'll have
$x = y_1,y = x_1-y_1\cdot \lfloor \frac{a}{b}\rfloor$

```cpp
int extended_gcd(int a, int b, int& x, int& y) {
    if (b == 0) {
        x = 1, y = 0;
        return a;
    }
    int x1, y1;
    int d = extended_gcd(b, a % b, x1, y1);
    x = y1, y = x1 - y1 * (a / b);
    return d;
}
```

- iterative implementation

lets define sequences $\{q_i\},\{r_i\},\{s_i\},\{t_i\}$
the equation to solve is $ax+by = g$
we'll have at iterations: $\ as_i + bt_i = r_i\quad ...(1)$ with $r_0=a,r_1=b$ & $i=2$ at begining
we'll compute $r_i =r_{i-2}\text{ mod } r_{i-1} = r_{i-2} - r_{i-1}q_i$

using equation $(1)$ and simplifying
$s_i = s_{i-2} - s_{i-1}q_i$
$s_i = t_{i-2} - t_{i-1}q_i$
also using equation $(1)$ gives us
$s_0 = 1,t_0 = 0, s_1 = 0, t_1 = 1$
if in the $n^{th}$ iteration, $r_{n-1}$ becomes zero then:
$gcd(a,b) = gcd(r_0,r_1)= gcd(r_1,r_2)= ... = gcd(r_{n-2},0) = r_{n-2}$
$\implies gcd(a,b) = r_{n-2} = s_{n-2}a + t_{n-2}b$

In the equation $r_i = r_{i-2} - r_{i-1}q_i$, `x,y` plays the role of $s_{i-2},t_{i-2}$ and `x1,y2` plays the role of $s_{i-1},t_{i-1}$

```cpp
int extended_gcd(int a, int b, int& x, int& y) { 
    x = 1, y = 0; 
    int x1 = 0, y1 = 1; 
    while (b) { 
        int q = a / b; 
        tie(x, x1) = make_tuple (x1, x - q * x1); 
        tie(y, y1) = make_tuple (y1, y - q * y1); 
        tie(a, b) = make_tuple (b, a - q * b); 
    } 
    return a; 
}
```

# Linear Diophantine Equations

- for $ax + by = c$
- $a = 0; b = 0$ is a degenerate case

### One solution

- its straight forward that solution exists when $c$ is divible by $gcd(a,b)$ because linear combination of two numbers is divible by their gcd

```cpp
bool find_any_solution (int a, int b, int c, int &x0, int &y0) {
    int g = extended_gcd(abs(a), abs(b), x0, y0);
    if (c % g) return false;
    
    x0 *= c / g;
    y0 *= c / g;
    if (a < 0) x0 = -x0;
    if (b < 0) y0 = -y0;
    return true;
}
```

### All solutions

lets say $g = gcd(a,b)$ and $(x_{0},y_{0})$ is a solution i.e.
$a\cdot x_{0}+b\cdot y_{0} = c$
now adding $b/g$ to $x_0$ and substracting $a/g$ from $y_0$ won't break the inequality
$a\cdot(x_{0} + \frac{b}{g}) + b\cdot(y_0 - \frac{a}{g})\: = \: a\cdot x_{0}+b\cdot y_{0} = c$

so all the solutions are of form with $k\in I$
$x = x_{0} + k\cdot \frac{b}{g}$
$y = y_{0} - k\cdot \frac{a}{g}$

### couting solutions

$a\cdot x+b\cdot y =c$ is made $\frac{a}{g}\cdot x + \frac{b}{g}\cdot y = \frac{c}{g}$ in the beginning which makes gcd of coefficients 1.

Denote the solution by $x_{0},\: y_{0}$

we have range contraint $x\in [x_{min},x_{max}]$ and $y\in [y_{min}, y_{max}]$ so

$$
x_{min} \leq x_{0} + k\cdot \frac{b}{g} \leq x_{max}\\
\implies \frac{x_{min} - x_{0}}{b} \leq k \leq \frac{x_{max} - x_{0}}{b}\: since\: g=1

$$

since $k$ takes integers values

```cpp
void shift_solution(int & x, int & y, int a, int b, int cnt) {
    x += cnt * b;
    y -= cnt * a;
}

int find_all_solutions(int a, int b, int c, int minx, int maxx, int miny, int maxy) {
    int x, y, g;
    // be sure to modify find_any_solution for setting 'g'
    if (!find_any_solution(a, b, c, x, y, g))	
        return 0;
    a /= g;
    b /= g;

    int sign_a = a > 0 ? 1 : -1;
    int sign_b = b > 0 ? 1 : -1;

    shift_solution(x, y, a, b, (minx - x) / b);
    if (x < minx)
        shift_solution(x, y, a, b, sign_b);
    if (x > maxx)
        return 0;
    int lx1 = x;

    shift_solution(x, y, a, b, (maxx - x) / b);
    if (x > maxx)
        shift_solution(x, y, a, b, -sign_b);
    int rx1 = x;

    shift_solution(x, y, a, b, -(miny - y) / a);
    if (y < miny)
        shift_solution(x, y, a, b, -sign_a);
    if (y > maxy)
        return 0;
    int lx2 = x;

    shift_solution(x, y, a, b, -(maxy - y) / a);
    if (y > maxy)
        shift_solution(x, y, a, b, sign_a);
    int rx2 = x;

    if (lx2 > rx2)
        swap(lx2, rx2);
    int lx = max(lx1, lx2);
    int rx = min(rx1, rx2);

    if (lx > rx)
        return 0;
    return (rx - lx) / abs(b) + 1;
}
```

### solution with minimum x+y

$$
x'=x+k\cdot \frac{b}{g} \text{ and } y'=y-k\cdot \frac{a}{g}\\
\implies x' + y' = x+y+k\cdot \left(\frac{b-a}{g}\right)

$$

so if $a>b$ choose largest value on $k$ and if $b>a$ choose smallest value of $k$

# Fibonacci Numbers

- $F_{0} = 0,\: F_{1} = 1,\: F_{n} = F_{n-1} + F_{n-2}\: for\: n\geq 2$
- $det\begin{bmatrix} F_{n+1} & F_{n}\\ F_{n} & F_{n-1} \end{bmatrix} = det\begin{bmatrix} 1 & 1\\ 1 & 0 \end{bmatrix}^n$, can be proved by induction
- **Cassini's Identity** $F_{n-1}F_{n+1}-F{_n}^2=(-1)^n$
    proof:
    $F_{n-1}F_{n+1}-F{_n}^2=det\begin{bmatrix} F_{n+1} & F_{n}\\ F_{n} & F_{n-1} \end{bmatrix} = det\begin{bmatrix} 1 & 1\\ 1 & 0 \end{bmatrix}^n = \left(det\begin{bmatrix} 1 & 1\\ 1 & 0 \end{bmatrix}\right)^n = (-1)^n$
    
- **Addition Rule** $F_{n+k} = F_{k}F_{n+1} + F_{k-1}F_{n}$
    proof:
    from the above determinant indentity and $A^{m+n}=A^{m}A^{n}$ we get:
    
    $$
    \begin{bmatrix} F_{m+n+1} & F_{m+n}\\  F_{m+n} & F_{m+n-1} \end{bmatrix} = \begin{bmatrix} F_{m+1} & F_{m}\\  F_{m} & F_{m-1} \end{bmatrix}\cdot \begin{bmatrix} F_{n+1} & F_{n}\\  F_{n} & F_{n-1} \end{bmatrix}
    
    $$
    
    carry out the multiplication and match corresponding entries in matrices on both sides
    
- $k=n$ case in above: $F_{2n} = F_{n}(F_{n+1}+F_{n-1})$
- from above for $k\in \Z^{+}$, $F_{nk}$ is multiple of $F_{n}$
- **gcd indentity** $gcd(F_{n},F_{m}) = F_{gcd(n,m)}$
- from above if $f_{m}|f_{n}$ then $m|n$
- fibonacci numbers are worst case inputs for the Euclidian algorithm for gcd
    

## fibonacci coding

- by Zeckendorf's theorem, any $n\in\N$ can be uniquely represented as
    $n = F_{k_{1}}+F_{k_{2}}+F_{k_{3}}+...+F_{k_{r}}$
    where $F_{k_{i}}$s are fibonacci numbers such that $k_{i} \geq k_{i+1}+2$ (representation can't contain consecutive fibonaccy numbers)
    
- examples(1 is appended at the end on coding to indicate the end):
    $1 = F_{2} =(11)_{F}$
    $2 = F_{3} = (011)_{F}$
    $3 = F_{2}+F_{3} = (111)_{F}$
    
- encoding
    
    1.  iterate through fibonacci numbers largest to smallest until you find one less than $n$
    2.  substract the found $F_{i}\leq n$ from the number $n$ and turn on the $(i-2)^{th}$ bit of coded value
    3.  keep on until $n>0$
    4.  add a final 1 to indicate end of code
- decoding
    
    1.  remove the ending 1
    2.  for $i^{th}$ bit set add $F_{i+2}$ to the number
        

## formulas for $i^{th}$ fibonacci number

- $F_{n} =$ nearest integer of $\left(\frac{1+\sqrt{5}}{2} \right)^n / \sqrt{5}$
- $\begin{bmatrix} F_{n} \\ F_{n-1} \end{bmatrix}= \begin{bmatrix} 1 & 1 \\ 1 & 0 \end{bmatrix} \begin{bmatrix} F_{n-1} \\ F_{n-2} \end{bmatrix} = P\cdot \begin{bmatrix} F_{n-1} \\ F_{n-2} \end{bmatrix}$ so
    $\begin{bmatrix} F_{n} \\ F_{n-1} \end{bmatrix} = P^{n}\begin{bmatrix} F_{1} \\ F_{0} \end{bmatrix}$

### fast doubling method

by $F_{n+k} = F_{k}F_{n+1} + F_{k-1}F_{n}$
we have
$F_{2k} = F_{k}(2F_{k+1} - F_{k})$
$F_{2k+1} = F_{k+1}^2+ F_{k}^2$

- this way we can find Fibonacci numbers with $\mathcal{O}(\lg n)$ cost, same as matrix binary multiplication but better

```cpp
pair<int, int> fib (int n) {
    if (n == 0)
        return {0, 1};

    auto p = fib(n >> 1);
    int c = p.first * (2 * p.second - p.first);
    int d = p.first * p.first + p.second * p.second;
    if (n & 1)
        return {d, c + d};
    else
        return {c, d};
}
```