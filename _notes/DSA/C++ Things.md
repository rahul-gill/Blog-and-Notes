---
name: C++ Things
updated: 2022-02-10 11:30:22Z
created: 2021-07-15 07:28:35Z
layout: note
categories: DSA
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

- literals
```cpp
auto raw_string = R"(Raw String)"
auto binary_num = 0b11
auto hex_num = 0x11
```
- user defined literal
```cpp
long double operator"" _kg( long double x ){
	return x*1000;
}
long double operator"" _g( long double x ){
	return x;
}
long double operator"" _mg( long double x ){
	return x / 1000;
}
auto amnt = 10_kg + 5_mg
//works only on these:
char const*
unsigned long long
long double
char const*, std::size_t
wchar_t const*, std::size_t
char16_t const*, std::size_t
char32_t const*, std::size_t
```

- scoped enums
```cpp
enum struct nums{
  one= 1,
  ten=10,
  hundred=100,
  thousand= 1000
};
```
- doing this will avoid narrowing
```cpp
char c1{999};   
```
- constexpr for computing at compile time

```cpp
constexpr int cube (int a){return a * a * a;}
float gk[cube(3)]; // Valid since C++11: variable 'gk' has 27 elements
```

- lambdas

```cpp
[scope](params){body}
//scope is either empty, & or =

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

```cpp
auto [var1, var2] = make_tuple(1,2);//tuples,pairs,structs etc.
```