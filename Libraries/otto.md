# [Otto](https://square.github.io/otto/)
## *Introduction*
Otto is an event bus designed to decouple different parts of your application while still allowing them to communicate efficiently.

Forked from Guava, Otto adds unique functionality to an already refined event bus as well as specializing it to the Android platform.

## *Usage*
Otto is designed with Android-specific use cases in mind and is intended for use as a singleton (though that is not required). Create an instance of an event bus with
```
Bus bus = new Bus();
```
Because a bus is only effective if it is shared, we recommend obtaining the instance through injection or another appropriate mechanism.

## [otto](https://github.com/square/otto)
## [[안드로이드] otto 라이브러리 ( An event bus by Square )](https://youngest-programming.tistory.com/80)
