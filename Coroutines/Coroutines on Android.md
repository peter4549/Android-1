# Coroutines on Android
## [Coroutines on Android (part I): Getting the background](https://medium.com/androiddevelopers/coroutines-on-android-part-i-getting-the-background-3e0e54d20bb)
> <mark>Coroutines will run on the main thread, and suspend does not mean background.</mark>

## [Coroutines on Android (part II): getting started](https://medium.com/androiddevelopers/coroutines-on-android-part-ii-getting-started-3bff117176dd)
### Keeping track of coroutines
coroutines are a great solution to two common programming problems:
1. **Long running tasks** are tasks that take too long to block the main thread.
2. **Main-safety** allows you to ensure that any suspend function can be called from the main thread.

> A leaked coroutine can waste memory, CPU, disk, or even launch a network request that’s not needed.

To help avoid leaking coroutines, Kotlin introduced [**structured concurrency**](https://kotlinlang.org/docs/coroutines-basics.html#structured-concurrency). Structured concurrency is a combination of language features and best practices that, when followed, help you keep track of all work running in coroutines.

On Android, we can use structured concurrency to do three things:
1. **Cancel work** when it is no longer needed.
2. **Keep track** of work while it’s running.
3. **Signal errors** when a coroutine fails.

### Cancel work with scopes
> A CoroutineScope keeps track of all your coroutines, and it can cancel all of the coroutines started in it.

#### Starting new coroutines
1. [**launch**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/launch.html) builder will start a new coroutine that is “fire and forget” — that means it won’t return the result to the caller.
2. [**async**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/async.html) builder will start a new coroutine, and it allows you to return a result with a suspend function called `await`.

> Launch is a bridge from regular functions into coroutines.

*Warning*: A big difference between `launch` and `async` is how they handle exceptions. `async` expects that you will eventually call `await` to get a result (or exception) so it won’t throw exceptions by default. That means if you use `async` to start a new coroutine it will silently drop exceptions.

Kotlin just doesn’t let you create an untracked coroutine

#### Start in the ViewModel
> Structured concurrency guarantees when a scope cancels, all of its coroutines cancel.

To use coroutines in a `ViewModel`, you can use the `viewModelScope` [extension property](https://khan.github.io/kotlin-for-python-developers/#extension-functionsproperties) from `lifecycle-viewmodel-ktx:2.1.0-alpha04`.

`viewModelScope` will automatically cancel any coroutine that is started by this `ViewModel` when it is cleared (when the `onCleared()` callback is called).

## [Coroutines On Android (part III): real work](https://medium.com/androiddevelopers/coroutines-on-android-part-iii-real-work-2ba8a2ec2f45)
### Solving real-world problems with coroutines
Coroutines are a general purpose programming language feature at the same level as functions — so you can use them to implement anything that you could with functions and objects. However, there are two types of tasks that come up all the time in real code that coroutines are a great solution for:

1. **One shot requests** are requests that are run each time they are called — they always complete after the result is ready.
2. **Streaming requests** are requests that continue to observe changes and report them to caller — they don’t complete when the first result is ready.

### One shot requests
A one shot request is performed once each time it’s called and completes as soon as a result is ready. This pattern is the same as a regular function call — it gets called, does some work, then returns.

> A one shot request is performed each time it’s called. It stops executing as soon as a result is ready.
