---
layout: default
name: _notes/android/Notifications.md
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
- step 1
```kotlin
private final static String manasia_notification_channel = "Manasia Event Reminder";

// Create the NotificationChannel, but only on API 26+ because
// the NotificationChannel class is new and not in the support library
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
  //define the importance level of the notification
  int importance = NotificationManager.IMPORTANCE_DEFAULT;

  //build the actual notification channel, giving it a unique ID and name
  NotificationChannel channel = 
    new NotificationChannel(manasia_notification_channel, manasia_notification_channel, importance);

  //we can optionally add a description for the channel
  String description = "A channel which shows notifications about events at Manasia";
  channel.setDescription(description);
  
  //we can optionally set notification LED colour
  channel.setLightColor(Color.MAGENTA);

  // Register the channel with the system
  NotificationManager notificationManager = (NotificationManager) getApplicationContext().
    getSystemService(Context.NOTIFICATION_SERVICE);
  if (notificationManager != null) {
    notificationManager.createNotificationChannel(channel);
  }
}
```