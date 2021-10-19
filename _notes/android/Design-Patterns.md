---
layout: default
name: _notes/android/Design Patterns.md
categories: Android
date: 2021-10-19
---
<script 
    type="text/javascript"
    src="https://unpkg.com/mermaid@8.13.2/dist/mermaid.min.js">
</script>

<link 
  rel="stylesheet" 
  href="https://cdn.jsdelivr.net/npm/katex@0.13.18/dist/katex.min.css" integrity="sha384-zTROYFVGOfTw7JV7KUu8udsvW2fx4lWOsCEDqhBreBwlHI4ioVRtmIvEThzJHGET" crossorigin="anonymous">

<script defer 
  src="https://cdn.jsdelivr.net/npm/katex@0.13.18/dist/katex.min.js" integrity="sha384-GxNFqL3r9uRJQhR+47eDxuPoNE7yLftQM8LcxzgS4HT73tp970WS/wV5p8UzCOmb" crossorigin="anonymous">
</script>

<script defer 
  src="https://cdn.jsdelivr.net/npm/katex@0.13.18/dist/contrib/auto-render.min.js" integrity="sha384-vZTG03m+2yp6N6BNi5iM4rW4oIwk5DfcNdFfxkk9ZWpDriOkXX8voJBFrAO7MpVl" crossorigin="anonymous">
</script>
<script>
    document.addEventListener("DOMContentLoaded", function() {
        renderMathInElement(document.body, {
          // customised options
          // • auto-render specific keys, e.g.:
          delimiters: [
              {left: '$$', right: '$$', display: true},
              {left: '$', right: '$', display: false}
          ],
          // • rendering keys, e.g.:
          throwOnError : false
        });
    });
</script>
${toc}
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
