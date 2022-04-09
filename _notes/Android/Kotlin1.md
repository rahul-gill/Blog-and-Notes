---
layout: note
name: Kotlin I
categories: Android
date: 2021-10-20
---


${toc}
<br>
# Conditionals and Loops

```
if (a > b){ /*do some*/}
else if(a < b){/*do some2*/}
else{/*do some3*/}
//an expression so can return a value
fun maxOf(a:Int, b:Int) = if(a>b) a else b
val max = if(a>b){
    print("a choosen")
    a
}else{
    print("b choosen")
    b
}
```

```
//this can also be used as an expression or statement
when(x){
    0          -> {print("x is 0")}
    1,2        -> print("x is 1 or 2")
    isPrime(x) -> print("x is prime")
    else       -> print("x is something else")
}
when(inInt){
    in validNumber -> print("valid")
    !in 0..100    -> print("big")
    else           -> print("none of options")
}
//as replacement of if else chain
when{
    x.isOdd()  -> //do some
    y.isEven() -> //do some2
}
//wowo
fun Request.body() = 
    when(val response = executeRequest()){
    isSuccess -> response.body
    else      -> throw HTTPException(response.status)
    }
```

### Loops

```
for(item in collectionList) print(item)
for(i in 1..10) print(i)
for(i in 1..10 step 2) print(i)
for(i in 9 downTo 0 step 3) print(i)
for(i in array.indices) print(array[i])
while(x>0) x--
do{ x++ } while (x<100)
```

- return, break, continue classic + this labelled type:

```
outer@ for(i in 1..10){
    for(j i 1..10){
        if(somethingBad()) break@outer
    }
}
```

```
fun foo(){
    listOf(1,2,3,4,5).forEach lit@{ 
        if(it == 3) return@lit //lambda labelled return
        print(it)
    }
}
```

- implicit label

```
listOf(1,2,3,4,5).forEach{
    if(it == 3) return@forEach
}
```

- or pass anonymous function instead of lambda

```
//implicitt label
listOf(1,2,3,4,5).forEach(fun(val:Int){
    if(it == 3) return
    //dasdasfasfe
})
```


# Functions
- Kotlin functions are first-class, which means they can be stored in variables and data structures, and can be passed as arguments to and returned from other higher-order functions.
- Unit return
```
//no return type(Unit can be omitted)
fun cout(a:Int) : Unit{ 
    print(a) 
}
```
- varargs for variable number of arguments
```
fun foo(vararg strings: String){/**/}
foo(strings = *arrayOf("a","b","c")) // * is the spread operator
foo(strings = *asList("a", "b", "c", *arrayOfStrings, "z"))
```
- infix notation
```
//1. they must be member/extension function
//2. must have single parameter
//3. no variable number of arguments and no default value
//take care of precedence with parantheses
infix fun Int.shl(x: Int): Int { ... }

// calling the function using the infix notation
1 shl 2

// is the same as
1.shl(2)
```
- tail recursive functions
```
tailrec fun findFixPoint(x: Double = 1.0): Double =
	if (Math.abs(x - Math.cos(x)) < eps) x else findFixPoint(Math.cos(x))
```
## High Order Functions
- Kotlin uses function types, such as `(Int) -> String` or `(x: Int, y: Int) -> Point`, for declarations that deal with functions: 
```
val onClick: () -> Unit = ...
```
- the type `A.(B) -> C` represents functions that can be called on a receiver object `A`  with a parameter `B` and return a value `C`
-  a value of type `(A, B) -> C` can be passed or assigned where a value of type `A.(B) -> C` is expected, and the other way around
-  `f.invoke(x)` <-> `f(x)`  invoke operator
-  Lambda expressions and anonymous functions are function literals. Function literals are functions that are not declared but are passed immediately as an expression. 
### Lambdas

```
//{param1:Param1Type, param2:Param2Type -> /*Function Body*/}
val sum = {x:Int, y:Int -> x+y}
//labda expression in {},lambda expression and curly bracket both together are called function literal
```

- Trailing lambdas: If last parameter of function is a function then lambda expression passed can be placed outside the paranthesese

```
val product = item.fold(1) {acc, e -> acc*e}
run{println("..")} //function  is the only argument
```

- `it` is the name of implicit single parameter for lambda
- return from lambda can be explicit using qualified return otherwise value of last expression is implicitly returned
```
ints.filter {
    val shouldFilter = it > 0
    shouldFilter
}
//or
ints.filter {
    val shouldFilter = it > 0
    return@filter shouldFilter
}
```
- **Anonymous functions:** don't have a name
- return in lambda function will return from enclosing function(qualified return for return from lambda) whereas return in anonymous function will return from itself

```
fun(x:Int, y:Int): Int = x+y
//or
fun(x:Int, y:Int): Int{return x+y}
//parameters types can omitted if can be inferred
ints.filter(fun(item) -> item > 0)
```
- useful if return type has to be explicitly stated(can't be done with lambdas)
- Function types with receiver, such as `A.(B) -> C`, can be instantiated with a special form of function literals in which the receiver can be accessed with `this`
```
val sum = fun Int.(other: Int): Int = this + other
//or with lambdas
val sum: Int.(Int) -> Int = { other -> plus(other) }
```


# Operator Overloading
```
operator fun <opName>(<params>){<body>}
```
