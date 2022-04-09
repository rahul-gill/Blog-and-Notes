---
layout: note
name: Kotlin II  
categories: Android
date: 2021-10-20
---


<br>

# Classes Basics
```kotlin
//primary constructor only
class Person(val name: String){/* */}
//primary constructor with init block
class Person(name: String){
	init{/**/}
}
//constructor keyword required with annotations or modifiers
class Person public @Inject constructor(name: String){/***/}
//secondary constructor
class Pet {
	constructor(owner: Person) {
		owner.pets.add(this)
	}
}
//primary constructor & secondary constructor
class Pet(owner: Person){
	init{ owner.pets.add(this) }
	constructor(owner: Person,name:String): this(owner){
		/* */
	}
}
//name is property now, not argument to constructor
class Person(val name:String){
	fun showName(){ println("Person name is $name.") }
}
```
- multiple `init` blocks are possible; executed in same order as they appear in class body
- as in the last code snippet properties can be directly initialized
- if class has primary constructor then secondary constructors need to delegate it as in second last code snippet; initializer blocks are executed before anything in primary constructor
# Inheritance
- All classes in Kotlin have a common superclass `Any` which has three methods `equals()`, `hashcode()` and `toString()`
- to make a class inheritable and inherit from it
```
open classPolygon(){/**/}
class Rectangle(): Polygon(){/**/}
```
- if parent class has primary constructor then child class must have that same parameter primary constructor
- if parent class don't have primary constructor then secondary constructor has to initialize the base type using the `super` keyword or it has to delegate to another constructor which does.
- Kotlin requires explicit modifiers for overridable members and overrides:
```
open class Shape {
    open fun draw() { /*...*/ }
    fun fill() { /*...*/ }
}
class Circle() : Shape() {
    override fun draw() { /*...*/ }
}
```
- `val` property can be overided as a `var` property
- base class initialization is done first so avoid using open members in the constructors, property initializers, or init blocks
-  if a class inherits multiple implementations of the same member from its immediate superclasses, it must override this member and provide its own implementation (perhaps, using one of the inherited ones)
```
open class Rectangle {
    open fun draw() { /* ... */ }
}
interface Polygon {
    fun draw() { /* ... */ } // interface members are 'open' by default
}
class Square() : Rectangle(), Polygon {
    override fun draw() {
        super<Rectangle>.draw() // call to Rectangle.draw()
        super<Polygon>.draw() // call to Polygon.draw()
    }
}
```
# Properties
- The full syntax for declaring a property is as follows:
```
var <propertyName>[: <PropertyType>] [= <property_initializer>]
    [<getter>]
    [<setter>]
```
- mutable `val` doesn't allow setter(setter is called everytime value assigned to the property except initialization)
- getter and setter
```
var stringRepresentation: String
    get() = this.toString()
    set(value) {
        setDataFromString(value)
    }
```
- visibility and annotation without body
```
var setterVisibility: String = "abc"
    private set
var setterWithAnnotation: Any? = null
    @Inject set
```
- a field is only used as a part of a property to hold its value in memory;  backing field will be generated for a property if it uses the default implementation of at least one of the accessors, or if a custom accessor references it through the field identifier.
```
var counter = 0 // the initializer assigns the backing field directly
    set(value) {
        if (value >= 0)
            field = value
// counter = value // ERROR StackOverflow: Using actual name 'counter' would make setter recursive
    }
```
- compile time constants(value known at compile time; has to be primitive type or String)
```
const val SUBSYSTEM_DEPRECATED: String = "system is deprecated"
```
- when you're sure you won't be accessing the property before initialization; must be non-null and non-pimitive type
```
lateinit var subject: TestSubject
```
- `.isInitialized` can be used to know if lateinit is initialized or not

# Interfaces
- can contain declarations of abstract methods, as well as method implementations
- Properties declared in interfaces can't have backing fields
```
interface Human(){/**/}
```
## Functional or SAM(single abstract method) interfaces
- have only one abstract method;  can have several non-abstract members but only one abstract member
```
fun interface IntPredicate {
   fun accept(i: Int): Boolean
}
```
- kotlin SAM conversion
	- without SAM
	```
	val isEven = object : IntPredicate {
		override fun accept(i: Int): Boolean {
			return i % 2 == 0
		}
	}
	```
	- with it
	```
	val isEven = IntPredicate { it % 2 == 0 }
	```
# Visibility Modifiers
- `private`, `protected`, `internal`, `public`(default)
- for top level declarations
	- `private`: can use only in that file
	- `protected`: not applicable 
	- `internal`: only in that module
	- `public`: everywhere
- in classes and interfaces
	- `private`: class only
	- `internal`: any client inside this module who sees the declaring class sees its internal members
	-  `public`: any client who sees the declaring class sees its public members
	-  `protected`: classes and subclasses
# Extensions
- extension functions
```
//adding swap function for MutableList<Int>
fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' corresponds to the list
    this[index1] = this[index2]
    this[index2] = tmp
}
```
- for extensions intializers are not allowed
```
val <T> List<T>.lastIndex: Int	//OK
    get() = size - 1
val House.number = 1 // error
```
- extension receiver and dispatch receiver
```
class Host(val hostname: String) {
    fun printHostname() { print(hostname) }
}
class Connection(val host: Host, val port: Int) {
    fun printPort() { print(port) }

    fun Host.printConnectionString() {
        printHostname()   // calls Host.printHostname()
        print(":")
        printPort()   // calls Connection.printPort()
    }
    fun connect() {
        /*...*/
        host.printConnectionString()   // calls the extension function
    }
}
fun main() {
    Connection(Host("kotl.in"), 443).connect()
    //Host("kotl.in").printConnectionString()  // error, the extension function is unavailable outside Connection
}
```
- precedence or receivers
```
class Connection {
    fun Host.getConnectionString() {
        toString()         // calls Host.toString()
        this@Connection.toString()  // calls Connection.toString()
    }
}
```

# Data Classes
-  compiler automatically derives the following members from all properties declared in the primary constructor
	-	`equals()`/ `hashCode()`
	-	`toString()` of the form `User(name=John, age=42)`
	-	`componentN()` for destructuring declaration
	```
	data class Person(val name:String, val age)
	var person = Person("Rahul",18)
	val (nameS, ageS) = person
	//this line is compiled down to
	val nameS = person.component1()
	val ageS = person.component2()
	```
	-   `copy()`
- The primary constructor needs to have at least one parameter, its parameters need to be marked as val or var.
- Data classes cannot be abstract, open, sealed, or inner.
- functions are not generated for properties declared inside body, and two object with only properties different which are inside body will be considered equal
- copying(to alter only some of the properties)
```
fun copy(name: String = this.name, age: Int = this.age) = User(name, age)
val jack = User(name = "Jack", age = 1)
val olderJack = jack.copy(age = 2)
```

# Sealed and Enum classes
- enums constants can't have multiple instances but subclasseses of sealed class can have
- sealed classes are abstract by themselves; can't be instanciated directly; constructor is either private or protected
- sealed classes model restricted class hierarchies 
```
sealed class Status{
	data class Success(val successString: String): Status()
	data class Error(val errorCode: Int, val errorsString: String): Status()
	object InProgress: Status()
}
```
- sub classes must be in the same file
- ide can recognize all when branches

## enums
```
enum class Size(val quantity: Int, val quality: String){
	//these are objects
	MEGA(5,"BEST"){
		override fun description() = "$Mega Sized thing total ${quantity}"
	},
	LARGE(4,"GOOD"){
		override fun description() = "$Large Sized thing total ${quantity}"
	},
	SMALL(3,"OK"){
		override fun description() = "$Small Sized thing total ${quantity}"
	};	//separate the constant definitions from the member definitions with a semicolon
	abstract fun description(): String
}
val obj = Size.MEGA
println("${obj.name}, ${obj.ordinal}, ${obj.quantity}")
```

# Nested and inner classes
- nested class; We can nest classes in classes, interfaces in classes, classes in interfaces, and interfaces in interfaces.
- inner class can access members of its outer class and carry reference to an object or outer class(qualified this)
- anonymous inner class created using object expression
# Inline classes
- subset or value-based classes; don't have an identity and can only hold values(when wrapper around data is neccessary and we want to avoid the class overhead)
- declare with `@JvmInline` annotation and `value` modifier
```
@JvmInline
value class Password(private val s: String)//inline modifier is deprecated
```
- must have a single property initialized in the primary constructor
- can have properties, functions and init blocks but properties cannot have backing fields and these only have simple computable properties (no lateinit /delegated properties).
- at compile time they can have boxed or unboxed representation
- type aliases are assignment-compatible with their underlying type  while inline classes are not

## type aliases
```
typealias FileTable<K> = MutableMap<K, MutableList<File>>
//for function type
typealias MyHandler = (Int, String, Any) -> Unit
```

# Object expression and declarations
## expressions
```
val helloWorld = object {
    val hello = "Hello"
    val world = "World"
    // object expressions extend Any, so `override` is required on `toString()`
    override fun toString() = "$hello $world"
}
//inheriting
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) { /*...*/ }
    override fun mouseEntered(e: MouseEvent) { /*...*/ }
})
```
- When an anonymous object is used as a type of a `local` or `private` but not `inline` declaration (function or property), all its members are accessible via this function or property:
```
class C {
    private fun getObject() = object {
        val x: String = "x"
    }
    fun printX() {
        println(getObject().x)
    }
}
```
- If this function or property is `public` or `private inline`, members added won't be accessible. type of it can be `Any`, declared supertype or explicitly declared type
## Declarations
- declarations can't be local
```
object DataProviderManager {
    fun registerDataProvider(provider: DataProvider) {
        // ...
    }
    val allDataProviders: Collection<DataProvider>
        get() = // ...
}
DataProviderManager.registerDataProvider(...)
```
### companion objects
```
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}
```
- members of it can be called with just class name
```
val instance = MyClass.create()
```
- if the name of object is omitted `Companion` will be used
- class members can access companion object's private members
<br>
- Object expressions are executed (and initialized) immediately, where they are used but
declarations are initialized lazily, when accessed for the first time.

# Delegation
```
interface Base {
    fun print()
}

class BaseImpl(val x: Int) : Base {
	override fun print() { print(x) }
	override fun dontPrint() {  }
}
//Derived class implements Base by delegating public members to b object
class Derived(b: Base) : Base by b{
	//if you override member of b then this overrided member will be used
	override fun dontPrint() { print("NOu...") }
}
```
## delegating property
```
import kotlin.reflect.KProperty

class Student {
	var firstName: String? by NameDelegate()
	var lastName: String? by NameDelegate()

	override fun toString(): String {
		return "$firstName $lastName"
	}
}

class NameDelegate {
	var formattedValue: String? = null
	operator fun setValue(
		thisRef: Any?,
		property: KProperty<*>,
		value: String?
	){
		if (value != null && value.length > 5) {
			formattedValue = value.trim().toUpperCase()
		}
	}
	operator fun getValue(thisRef: Any?, property: KProperty<*>): String? {
		return formattedValue
	}
}

fun main() {
	val student = Student()
	student.firstName = "be"
	student.lastName = " "
	println(student)
}
```
- lazy delegate(won't be computed until accessed)
```
val lazyValue: String by lazy {
    println("computed!")
    "Hello"
}
```
- observable delegate
```
import kotlin.properties.Delegates
var name: String by Delegates.observable("intial name") {
	prop, old, new ->
	println("$old -> $new transform")
}
```
- vetoable(if the condition is true then only assignment is done)
```
var age: Int by Deletates.vetoable(14){property, oldValue, newValue ->
	newValue >= 14
}
```

# Generics
- class
```
class Box<T>(t: T) {
    var value = t
}
```
- function
```
fun<T> sort(Array<T>){
	...
}
```