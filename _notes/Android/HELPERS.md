---
layout: note
name: HELPERS
categories: Android
date: 2021-10-20
---


# Loading Button
```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">
    <data>
        <variable
            name="buttonName"
            type="java.lang.String" />
        <variable
            name="isLoading"
            type="boolean" />
        <variable
            name="onButtonClick"
            type="android.view.View.OnClickListener" />
    </data>

    <FrameLayout
        android:layout_width="175dp"
        android:layout_height="48dp">
        <androidx.cardview.widget.CardView
            android:id="@+id/loading_card"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:visibility="@{isLoading ? android.view.View.VISIBLE : android.view.View.GONE, default = gone}"
            android:layout_marginVertical="6dp"
            app:cardCornerRadius="4dp">
        <ProgressBar
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:background="@color/primaryTextColor"
            android:padding="8dp"/>
        </androidx.cardview.widget.CardView>
        <com.google.android.material.button.MaterialButton
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:id="@+id/btnDownload"
            tools:text="@string/download"
            app:backgroundTint="@color/primaryTextColor"
            android:visibility="@{isLoading ? android.view.View.GONE : android.view.View.VISIBLE, default = visible}"
            android:onClick="@{(view) -> onButtonClick.onClick(view)}">
        </com.google.android.material.button.MaterialButton>
    </FrameLayout>
</layout>
```