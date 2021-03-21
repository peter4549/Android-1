# Coroutines on Android
## [Coroutines on Android (part I): Getting the background](https://medium.com/androiddevelopers/coroutines-on-android-part-i-getting-the-background-3e0e54d20bb)
> <mark>Coroutines will run on the main thread, and suspend does not mean background.</mark>

## [Coroutines on Android (part II): getting started](https://medium.com/androiddevelopers/coroutines-on-android-part-ii-getting-started-3bff117176dd)
### Keeping track of coroutines
coroutines are a great solution to two common programming problems:
1. **Long running tasks** are tasks that take too long to block the main thread.
2. **Main-safety** allows you to ensure that any suspend function can be called from the main thread.
