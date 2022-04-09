---
layout: note
name: User Permissions
categories: Android
date: 2021-10-20
---



# User Permissions

- in the manifest

```
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
```

- permission checker and requester functions

```kotlin
private fun hasStorageAccessGranted() =
        ActivityCompat.checkSelfPermission(this, android.Manifest.permission.WRITE_EXTERNAL_STORAGE) == PackageManager.PERMISSION_GRANTED
```

```kotlin
private fun askForPermission(){//call in the button onClickListener
    val permissionsToRequest = mutableListOf<String>()
    if(!hasStorageAccessGranted()){
        permissionsToRequest.add(android.Manifest.permission.WRITE_EXTERNAL_STORAGE)
    }
    if(permissionsToRequest.isNotEmpty()){
        ActivityCompat.requestPermissions(this, permissionsToRequest.toTypedArray(), 0)
    }
}
```

- logging result

```kotlin
override fun onRequestPermissionsResult(
        requestCode: Int,////to differentiate different permission requests
        permissions: Array<out String>,
        grantResults: IntArray//granted permissions
    ) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults)
        if(requestCode == 0 && grantResults.isNotEmpty()){
            for(i in grantResults.indices){
                if(grantResults[i] == PackageManager.PERMISSION_GRANTED){ Log.d("MainActivityPermissions"," ${permissions[i]} granted") }
            }
        }
    }
```