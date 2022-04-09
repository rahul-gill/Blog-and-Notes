---
layout: note
name: Datastore, notification and work manager
categories: Android
date: 2021-10-20
---


# Storing key-value pairs with preferences DataStore
- dependencies
```
dependencies {
	implementation "androidx.datastore:datastore-preferences:1.0.0"
```
- creating a store
```kotlin
val Context.dataStore: DataStore<Preferences> by	
	preferencesDataStore(name = "settings")
```
- writing to it
```kotlin
private suspend fun save(key: String, value: Int){
	val dataStoreKey = preferencesKey<String>(key)
	dataStore.edit{ settings ->
		settings[dataStoreKey] = value
	}
}
```
- reading from it
```kotlin
private suspend fun read(key: String): Int? {
	val dataStoreKey = preferencesKey<String>(key)
	val preferences: Flow<Preferences> = dataStore.data
	return preferences.first()[dataStoreKey]	//not typesafe
}
```

# Notifications
```kotlin
const val NOTIFICATION_CHANNEL_ID = "music_playback_channel"
const val NOTIFICATION_CHANNEL_NAME = "Music Playback notifications"
const val NOTIFICATION_ID = 1
const val ACTION_SHOW_CONTEST_INFO_SCREEN = "ACTION_SHOW_CONTEST_INFO_SCREEN"

// create notification channel(notification categories) for android oreo and later
private fun createNotificationChannel(notificationManager: NotificationManager){
	val channel = NotificationChannel(
		NOTIFICATION_CHANNEL_ID,
		NOTIFICATION_CHANNEL_NAME,
		IMPORTANCE_LOW
	)
	notificationManager.createNotificationChannel(channel)
}

private fun setupNotification(){
	val notificationBuilder = NotificationCompat.Builder(requireContext(), NOTIFICATION_CHANNEL_ID)
		.setAutoCancel(false)
		.setSmallIcon(R.drawable.codeforces_icon)
		.setContentTitle("Codeforces div 2")
		.setContentIntent(getContestsActivityPendingIntent())
	
	with(NotificationManagerCompat.from(requireContext())) {
		if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O)
				createNotificationChannel(this)
		notify(0, notificationBuilder.build())
	}
}

private fun getContestsActivityPendingIntent() = PendingIntent.getActivity(
	requireContext(),
	0,
	Intent(requireContext(), ContestsActivity::class.java).also{
		action = ACTION_SHOW_CONTEST_INFO_SCREEN
	},
	FLAG_UPDATE_CURRENT
)
```

# Work Manager
- combines different solutions for background work on different api levels
- work manager is good for deferrable(useful work but can be done later) and guaranteed(task runs event if device is restarted or app is killed) background work
- not for work manager usecases
	- for exact time work : use `AlarmManager`
	- immediate, user expected work: use `ForegroundService`
- WorkManager supports:
	- One-off and periodic tasks
	- Constraints, such as network availability
	- Chaining tasks, parallel or sequentially
	- Observable task state for display in UI
	- Customized threading strategy
- implementation: example 
	- first create a worker class
	```kotlin
	class RefreshDataWorker @Inject constructor(
		appContext: Context,
		params: WorkerParameters,
		private val eventRepository: EventRepository
	) : CoroutineWorker(appContext, params){
		companion object{
			const val WORK_NAME = "RefreshDataWorker"
		}
		override suspend fun doWork(): Result{
			return try{
				eventRepository.loadContestDataFromNetwork()
				Result.success()
			}catch (e: HttpException){
				Result.failure()
			}
		}
	}
	
	val constraints = Constraints.Builder()
		.setRequiresBatteryNotLow(true)
		.build()
	val dataRefreshRepeatingWork = 
		PeriodicWorkRequestBuilder<RefreshDataWorker>(12, TimeUnit.HOURS)
			.setConstraints(constraints)
			.build()
	WorkManager.getInstance(applicationContext).enqueueUniquePeriodicWork(
		RefreshDataWorker.WORK_NAME,
		ExistingPeriodicWorkPolicy.KEEP,
		refreshRepeatingRequest
	)
```