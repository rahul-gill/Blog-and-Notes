---
layout: note
name: Kotlin III
categories: Android
date: 2021-10-20
---


# nulls
```kotlin
//making variables nullable
var variable: String? = null

//safe method calls and property access
val len = team?.players?.length //len will be null if team or players is null
ui?.populate()             //populate is called only if ui is not null
person?.department?.head = managerPool.getManager()

//null safe casts
val aInt: Int? = a as? Int// a can be null
val aInt: Int = a as Int  // a can't be null

//elvis operator ?: return length if not null else return -1
val len = array?.length ?: -1

//do things in let block only if item is not null
item?.let{ 
	println(it)
	doSomethingWithItem(it)
}

//return length of b if b is not null else throw null pointer exception
val len = b!!.length
```

# Annotations
- means of attaching meta data

# Reflection
```kotlin
(String::class).{qualifiedName, isData, isCompanion, members, constructors}
String::class.java
```

- this way functions, properties & constructors can also be referenced

# Scope functions
With, run, apply, also, let

  - `with` and `run` both have the context object available as `this`, both return lambda result. But run is an extension function and with is not(object passed as an argument). So run is 
 useful when you first want to initialize object then return the lambda value
```kotlin
fun bind(customer: customerModel) = with(binding){
	customerName.text = customer.name
	customerPoints = customer.points
	modifyCustomerButton.setOnClickListener{
		navigator.navigateTo(ModifyCustomerScreen(customer))
	}
}

fun setupAppLaunch() = repo.getLoggedInCustomer().run {
	if(this == null)
		showLoginScreen()
	else
		showUserScreen(this)
}
```
- also `run` can be used as non-extension function & lets you execute a block of several statements where an expression is required.
```kotlin
val hexNumberRegex = run {
    val digits = "0-9"
    val hexDigits = "A-Fa-f"
    val sign = "+-"

    Regex("[$sign]?[$digits$hexDigits]+")
} 
```

  - `apply` has the context object as `this` & returns the object. Common usecase of it is object configuration.
```kotlin
val reminder = Reminder()
val customNotificationBuilder = NotificationBuiler().apply{
	setNotificationIcon(someIcon)
	setTitle(reminder.title)
	setActions(reminder.actions)
	setColor(Color.Red)
}
NotificationDispatcher.dispatch(customNotificationBuilder.build())
```
  - `also` has the context object as `it` & return it. Its usecase is *also do the following with the object*
```kotlin
fun getUserDetails(){
	return apiService.getUserDetails().also{
		Log.d("getUserDetails", "User details got from api $it")
	}
}
```
  - usecase of `let` can be improving code readability with local variables. Object available as `it` and return lambda value; also to do something only when object is not null
```kotlin
val numbers = mutableListOf("one", "two", "three", "four", "five")
numbers.map { it.length }.filter { it > 3 }.let { 
    println(it)
} 

apiservice.getSomeData?.let {
    showDataInUI(it)
}
```
  - also standard library has `takeIf` and `takeUnless` has `it` object and return the object or null
```kotlin
val someRemoteData = apiService
	.fetchRemoteData()
	.takeIf{ it.isNotEmpty() }
```
