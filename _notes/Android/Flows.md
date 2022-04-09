---
layout: note
name: Flows
categories: Android
date: 2021-10-20
---


- can emit multiple values sequentially
- flow builder
```kotlin
val latestNews: Flow<List<ArticleHeadline>> = flow {
	emit(newsApi.fetchLatestNews())	
}
```
- A cold stream does not start producing values until one starts to collect them. A hot stream on the other hand starts producing values immediately.
## StateFlow
- hot flow used for state mangement
```kotlin
class LatestNewsViewModel(
    private val newsRepository: NewsRepository
) : ViewModel() {
	val uiState: StateFlow<LatestNewsUiState> = newsRepository.favoriteLatestNews
		.stateIn(
			initialValue = LatestNewsUiState.Loading,
			scope = viewModelScope,
			started = WhileSubsribed(5000) // cancel flow if not collected for 5 seconds
			// so rotating screen won't cancel the flow but navigating to home will
		)
}

class LatestNewsActivity : AppCompatActivity() {
	override fun onCreate(savedInstanceState: Bundle?) {
		lifecycleScope.launch {
			// repeatOnLifecycle launches the block in a new coroutine every time the
			// lifecycle is in the STARTED state (or above) and cancels it when it's STOPPED.
			// Always use it with flows
			// otherwise events will be processed even when the view is not visible => crashes
			// its similar to LiveData.observe
			repeatOnLifecycle(Lifecycle.State.STARTED) {
				latestNewsViewModel.uiState.collect { uiState ->
					when (uiState) {
						is LatestNewsUiState.Success -> showFavoriteNews(uiState.news)
						is LatestNewsUiState.Error -> showError(uiState.exception)
					}
				}
			}
		}
	}
}
```
- one more thing: the launch block which calls `repeatOnLifecycle` will be suspended unitl the view is below the state speficied(stated state here)
	so to collect multiple flows, create multiple coroutines
```kotlin
repeatOnLifecycle(Lifecycle.State.STARTED) {
	launch{
		viewModel.someFlow.collect{ ... }
	}
	launch{
		viewModel.someOtherFlow.collect{ ... }
	}
}
```
- converting callback based apis to flows
```kotlin
class FirestoreUserEventsDataSource(private val firestore: FirebaseFirestore) {
	fun getUserEvents(): Flow<UserEvents> = callbackFlow {
		var eventsCollection: CollectionReference? = null
		try {
			eventsCollection = FirebaseFirestore.getInstance()
				.collection("collection")
				.document("app")
		} catch (e: Throwable) {
			// If Firebase cannot be initialized, close the stream of data
			close(e)
		}

		// Registers callback to firestore
		val subscription = eventsCollection?.addSnapshotListener { snapshot, _ ->
			if (snapshot == null) { return@addSnapshotListener }
			try {
				offer(snapshot.getEvents())
			} catch (e: Throwable) {
				// Event couldn't be sent to the flow
			}
		}
		// Deregister the callback from Firestore if flow is closed or cancelled
		awaitClose { subscription?.remove() }
	}
}
```
- handling exceptions
```kotlin
class NewsRepository(...) {
	val favoriteLatestNews: Flow<List<ArticleHeadline>> =
		newsRemoteDataSource.latestNews
			.map { news -> news.filter { userData.isFavoriteTopic(it) } }
			.onEach { news -> saveInCache(news) }
			// If an error happens, emit the last cached values
			.catch { exception -> emit(lastCachedNews()) }
}
```
- execution in different coroutine context
```kotlin
class NewsRepository(
    private val newsRemoteDataSource: NewsRemoteDataSource,
    private val userData: UserData,
    private val defaultDispatcher: CoroutineDispatcher
) {
    val favoriteLatestNews: Flow<List<ArticleHeadline>> =
        newsRemoteDataSource.latestNews
            .map { news -> // Executes on the default dispatcher
                news.filter { userData.isFavoriteTopic(it) }
            }
            .onEach { news -> // Executes on the default dispatcher
                saveInCache(news)
            }
            // flowOn affects the upstream flow ↑
            .flowOn(defaultDispatcher)
            // the downstream flow ↓ is not affected
            .catch { exception -> // Executes in the consumer's context
                emit(lastCachedNews())
            }
}
```