---
layout: note
name: C++ Things
categories: DSA
date: 2021-10-20
---
# GCC built-ins
```
int  __builtin_popcount(unsigned int x)//returns number of 1's in x
```
```
int  __builtin_parity(unsigned int x)//parity = (number of 1's) % 2
```
```
int  __builtin_clz(unsigned int x) //count leading zeros(put l or ll for long)
```
```
int  __builtin_ctz(unsigned int x) //count trailing zeros
```
```
int  __builtin_ffs(int x) //least significant 1 bit position + 1
```

# c++ features

- constexpr for computing at compile time

```
constexpr int cube (int a){return a * a * a;}
float gk[cube(3)]; // Valid since C++11: variable 'gk' has 27 elements
```

- lambdas

```
[scope](params){body}
scope is either empty, & or =

std::function<float(float, float)> returnLambda(){
    return [](float a, float b) {return a + b;};
}
int main(){
    auto lmbda = returnLambda();
    std::cout << lmbda(8.2, 6.4) << std::endl;
}
```

- rvalue references
- structured bindings

```
auto [var1, var2] = make_tuple(1,2);//tuples,pairs,structs etc.
```