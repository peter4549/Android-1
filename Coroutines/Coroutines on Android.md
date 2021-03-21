# Coroutines on Android
## [Coroutines on Android (part I): Getting the background](https://medium.com/androiddevelopers/coroutines-on-android-part-i-getting-the-background-3e0e54d20bb)
> <mark>Coroutines will run on the main thread, and suspend does not mean background.</mark>

## [Coroutines on Android (part II): getting started](https://medium.com/androiddevelopers/coroutines-on-android-part-ii-getting-started-3bff117176dd)
### Keeping track of coroutines
coroutines are a great solution to two common programming problems:
1. **Long running tasks** are tasks that take too long to block the main thread.
2. **Main-safety** allows you to ensure that any suspend function can be called from the main thread.

> A leaked coroutine can waste memory, CPU, disk, or even launch a network request that’s not needed.

To help avoid leaking coroutines, Kotlin introduced <U>**[structured concurrency](https://kotlinlang.org/docs/coroutines-basics.html#structured-concurrency)**</U>. Structured concurrency is a combination of language features and best practices that, when followed, help you keep track of all work running in coroutines.

On Android, we can use structured concurrency to do three things:
1. **Cancel work** when it is no longer needed.
2. **Keep track** of work while it’s running.
3. **Signal errors** when a coroutine fails.
