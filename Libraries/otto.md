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

### THREAD ENFORCEMENT
Since at times it may be ambiguous on which thread you are receiving callbacks, Otto provides an enforcement mechanism to ensure you are always called on the thread you desire. By default, all interaction with an instance is confined to the main thread.
```
// Both of these are functionally equivalent.
Bus bus1 = new Bus();
Bus bus2 = new Bus(ThreadEnforcer.MAIN);
```
If you are not concerned on which thread interaction is occurring, instantiate a bus instance with `ThreadEnforcer.ANY`. You can also provide your own implementation of the `ThreadEnforcer` interface if you need additional functionality or validation.

## [otto](https://github.com/square/otto)
## [[안드로이드] otto 라이브러리 ( An event bus by Square )](https://youngest-programming.tistory.com/80)
다른 액티비티에 있는 값들을 **static으로 선언한것처럼** 다른 액티비티의 원하는 값을 갖고 오는 듯한 효과를 가질 수 있습니다.

오토는 **이벤트버스**라고도 불립니다. 버스에 다른 액티비티 또는 프래그먼트에 전달할 데이터를 넣습니다. 그리고 버스는 이 데이터를 싣고 정류장마다(액티비티) 멈춥니다. 여기서 **정류장은** 액티비티마다 하나씩 존재할 수 있는데 액티비티에서 정류장을 설치해놀수도 있고 안해놓을 수도 있습니다. 즉 정류장을 설치해논 액티비티에만 데이터를 싣은 버스가 도착해서 그들이 원하는 데이터가 있으면 주게됩니다.

이벤트를 감지하고자하는 액티비티 혹은 프래그먼트가 있고 이벤트가 발생하면 이벤트버스는 이벤트를 감지하는 액티비티 혹은 프래그먼트들에게 이벤트를 전달해줍니다. 그럼 받는쪽은 자신이 감지하고 있던 이벤트가 맞으면 데이터를 가져올 수 있습니다
