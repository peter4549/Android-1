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
