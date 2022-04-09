---
layout: note
name: Design Pattern
categories: Android
date: 2021-10-20
---
<br>

# Design Patterns
## Creational Patterns
- **Builder**: proceeds step by step and lets you define part of the Object 
```kotlin
AlertDialog.Builder(this)
	.setTitle("Sandwich Dialog")
	.setMessage("Please use the spicy mustard.")
	.setNegativeButton("No thanks") { dialogInterface, i ->
		// "No thanks" action
	}
	.setPositiveButton("OK") { dialogInterface, i ->
		// "OK" action
	}
	.show()
```
- **Dependency Injection**:  an object receives other objects that it depends on, called dependencies. 
	- Receiving object is called client, injected object is called service and code that passes service to client is called injector.
	- Instead of the client specifying which service it will use, the injector tells the client what service to use.
- **Singleton**: single instance
	- Behind the scenes, an INSTANCE static field backs the Kotlin object
```kotlin
object ExampleSingleton {
	fun exampleMethod() {
	// ...
	}
}
```
- **Factory**: 
```kotlin
interface HostingPackageInterface {
  fun getServices(): List<String>
}
enum class HostingPackageType {
	STANDARD,PREMIUM
}
class StandardHostingPackage : HostingPackageInterface {
  override fun getServices(): List<String> {
    return ...
  }
}
class PremiumHostingPackage : HostingPackageInterface {
  override fun getServices(): List<String> {
    return ...
  }
}

object HostingPackageFactory {
	fun getHostingFrom(type: HostingPackageType): HostingPackageInterface {
		return when (type) {
			HostingPackageType.PREMIUM -> PremiumHostingPackage() 
			else ->StandardHostingPackage()
		}
	}
}
```
## Structural Patterns
- **Adapter**: lets two incompatible classes work together; like the recyler view adapeter
- **Facade**: 	provides a higher-level interface that makes a set of other interfaces easier to use; for example using retrofit, we define an interface to the api and activity doesn't have to care about how its implemented, which converter we use etc.
- **Decorator**: dynamically attaches additional responsibilities to an object to extended its functionality at runtime
- **Composit**: Compose objects into tree structure to represent part-whole hierarchies. Composite lets client treat individual objects and compositions of objects uniformly
## Behavioral Patterns
- **Command**: 
- **Observer**
- **Strategy**
- **State**

# App Architectures
