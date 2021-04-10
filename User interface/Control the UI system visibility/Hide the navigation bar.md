# [Hide the navigation bar](https://developer.android.com/training/system-ui/navigation)
## [android.view.View.systemUiVisibility deprecated. What is the replacement?](https://stackoverflow.com/questions/62577645/android-view-view-systemuivisibility-deprecated-what-is-the-replacement)
### https://stackoverflow.com/a/64828067/15002852

```
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R) {
    window.setDecorFitsSystemWindows(false)
    window.insetsController?.let {
        it.hide(WindowInsets.Type.statusBars() or WindowInsets.Type.navigationBars())
        it.systemBarsBehavior = WindowInsetsController.BEHAVIOR_SHOW_TRANSIENT_BARS_BY_SWIPE
    }
} else {
    @Suppress("DEPRECATION")
    window.decorView.systemUiVisibility = View.SYSTEM_UI_FLAG_HIDE_NAVIGATION or View.SYSTEM_UI_FLAG_IMMERSIVE_STICKY
}
```
