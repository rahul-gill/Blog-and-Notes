---
layout: note
name: Exoplayer
categories: Android
date: 2021-10-20
---


- needs refining and fixing
```kotlin
r Composable and its implementation

class ExoPlayerState(context: Context) {
    val exoPlayer = SimpleExoPlayer.Builder(context).build()
    val duration by mutableStateOf(exoPlayer.duration)
    val bufferedPosition by mutableStateOf(exoPlayer.bufferedPosition)
    var position by mutableStateOf(exoPlayer.currentPosition)
}

@Composable
fun Player(
    modifier: Modifier = Modifier,
    sourceUrl: String
) {
    val context = LocalContext.current
    val exoPlayerState by remember(context) { mutableStateOf(ExoPlayerState(context)) } // <---- Problem 1?
    LaunchedEffect(sourceUrl) {
        exoPlayerState.exoPlayer.addListener(object : Player.Listener {
            override fun onPlaybackStateChanged(playbackState: Int) {
                when (playbackState) {
                    Player.STATE_READY -> {
                        Log.d("Player", "STATE_READY- duration: ${exoPlayerState.duration}") // <----- Problem 2
                    }

                    Player.STATE_ENDED -> {}

                    Player.STATE_BUFFERING, Player.STATE_IDLE -> {}
                }
            }
        })

        val mediaSource = generateMediaSource(context, sourceUrl)
        exoPlayerState.exoPlayer.setMediaSource(mediaSource)
        exoPlayerState.exoPlayer.prepare()
    }

    AndroidView(factory = {
        PlayerView(it).apply {
            player = exoPlayerState.exoPlayer
            useController = false
            (player as SimpleExoPlayer).playWhenReady = true
        }
    })

    // my custom player controller composable

    PlayerOverlay(
        exoPlayerState = exoPlayerState,
        onValueChangeFinished = {
            exoPlayerState.exoPlayer.seekTo(exoPlayerState.position)
        },
        modifier = modifier
    )
}

private fun generateMediaSource(context: Context, videoUrl: String): MediaSource {
    val mediaItem = MediaItem.Builder()
        .setUri(Uri.parse(videoUrl))
        .setDrmSessionForClearPeriods(true)
        .build()
    return DefaultMediaSourceFactory(buildDataSourceFactory(context)).createMediaSource(mediaItem)
}

private fun buildDataSourceFactory(context: Context): DataSource.Factory {
    return DefaultDataSourceFactory(
        context,
        getDefaultHttpDataSourceFactory(context)
    )
}

private fun getDefaultHttpDataSourceFactory(context: Context): HttpDataSource.Factory {
    return DefaultHttpDataSource.Factory()
        .setUserAgent(Util.getUserAgent(context, context.packageName))
}

```