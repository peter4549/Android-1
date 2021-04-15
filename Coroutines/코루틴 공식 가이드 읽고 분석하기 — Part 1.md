# [코루틴 공식 가이드 읽고 분석하기 — Part 1](https://myungpyo.medium.com/reading-coroutine-official-guide-thoroughly-part-1-98f6e792bd5b)
```
package com.smp.coroutinesample.basic

import kotlinx.coroutines.GlobalScope
import kotlinx.coroutines.delay
import kotlinx.coroutines.launch


fun main(args: Array<String>) {
    GlobalScope.launch {
        delay(1000L)
        println("World!")
    }
    println("Hello,")
    Thread.sleep(2000L)
}
```

GlobalScope.launch {} 코드 블록은 사실 코루틴을 생성하기 위한 코루틴 빌더이며 이렇게 생성되어 실행되는 코루틴은 호출(실행) 스레드를 블록하지 않기 때문에 그대로 두면 메인 함수가 종료되고 메인 함수를 실행한 메인 스레드 역시 종료되어 프로그램이 끝나게 됩니다. 이를 방지하기 위해 임의의 시간을 지정하여 지연 시킨 것 입니다. 이렇게 스레드를 멈추는 역할을 수행하는 함수를 중단 함수(Blocking function) 이라고 합니다.

이러한 중단 함수가 현재 스레드를 멈추게 할 수 있다는 것을 코드상에 보다 명시적으로 나타내기 위해 다음과 같이 runBlocking {} 블록을 사용할 수 있습니다.

```
fun main(args: Array<String>) {
    GlobalScope.launch {
        delay(1000L)
        println("World!")
    }
    println("Hello,")
    runBlocking {
        delay(2000L)
    }
}
```

runBlocking{} 블록은 주어진 블록이 완료될 때까지 현재 스레드를 멈추는 새로운 코루틴을 생성하여 실행하는 코루틴 빌더 입니다.

> 코루틴 안에서는 runBlocking { } 의 사용은 권장되지 않으며, 일반적인 함수 코드 블록에서 중단 함수를 호출할 수 있도록 하기 위해서 존재하는 장치 입니다. (위 예제에서는 delay() 중단 함수를 사용하기 위해 쓰였습니다.)

메인 함수 자체를 runBlocking {} 코루틴 빌더를 이용하여 작성하면 지연을 위한 delay() 중단 함수의 사용이 보다 자연스러워 집니다.

```
fun main(args: Array<String>) = runBlocking {
    GlobalScope.launch {
        delay(1000L)
        println("World!")
    }
    println("Hello,")
    delay(2000L)
}
```

delay() 는 중단 함수이며 모든 중단 함수들은 코루틴 안에서만 호출될 수 있다는 제약이 있습니다. GlobalScope.launch{ } 코드블록에서 delay(1000L) 를 사용할 수 있었던 이유도 GlobalScope.launch{ } 가 주어진 코드블록을 수행하는 코루틴을 생성하는 코루틴 빌더이며, 해당 코드블록은 코루틴 안에서 수행되고 있기 때문입니다.

지금까지의 예제에서는 GlobalScope.launch{ } 로 실행 된 코루틴의 수행이 완료될때 까지 현재 스레드(main 함수)를 대기시키기 위해서 임의의 지연(2초)을 주었는데 이것은 실제 프로그램에서는 적절한 방법이 아닙니다. 그 이유는 내부적으로 실행중인 코루틴(자식 코루틴)들이 작업을 완료하고 종료 될 때까지 얼마나 대기해야 할 지 부모 코루틴은 예측할 수 없기 때문입니다.

이러한 문제를 해결하기 위해서 우리는 다음과 같이GlobalScope.launch{ } 의 결과로 반환되는 Job 인스턴스를 이용할 수 있습니다.

```
fun main(args: Array<String>) = runBlocking {
    val job = GlobalScope.launch {
        delay(1000L)
        println("World!")
    }
    println("Hello,")
    job.join()
}
```

이제 runBlocking{ } 빌더로 생성 된 코루틴 블록(편의상 이후부터는 메인 코루틴이라 칭함)은 GlobalScope.launch{ } 빌더를 이용해 생성한 코루틴이 종료될 때까지 대기한 후 종료됩니다. 이는 자식 코루틴의 실행 흐름에 연결됨으로써 가능했습니다.

```
job.join()
```
