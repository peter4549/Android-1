# [코루틴 공식 가이드 읽고 분석하기— Part 0](https://myungpyo.medium.com/reading-coroutine-official-guide-thoroughly-part-0-20176d431e9d)
Coroutine
* Part-1 (Basics)
- 공식 가이드 읽기 (한국어 번역)
- 관련 기술 분석
+ CoroutineContext 와 CoroutineScope 란 무엇인가?
+ 코루틴은 왜 스레드보다 가볍다고 할까?
+ suspend 함수란 무엇이고 어떻게 동작할까?
+ 코루틴 디스패처 조금 더 살펴보기
* Part-2 (Cancellation and Timeouts)
- 공식 가이드 읽기 (한국어 번역)
- 관련 기술 분석
+ withContext 코루틴 빌더가 하는일은 무엇인가?
+ withTimeout 은 코루틴의 수행시간을 어떻게 제한하고 있을까?
* Part-3 (Channels)
- 공식 가이드 읽기 (한국어 번역)
- 관련 기술 분석
+ producer-consumer 구현을 돕기위한 produce-consumeEach 확장 함수는 어떻게 구현되어 있을까?
+ Fan-out 에서 수신 코루틴 중 하나에 오류가 발생했을 때 수신 방식(for-loop, consumeEach)에 따라 오류 전파 동작이 다른 이유는 무엇일까?
* Part-4 (Composing Suspending Functions)
- 공식 가이드 읽기 (한국어 번역)
- 관련 기술 분석
+ 중단 함수의 비동기 처리를 위한 async { } 코루틴 빌더는 어떻게 구현되어 있을까?
* Part-5 (Coroutine Context and Dispatchers)
- 공식 가이드 읽기 (한국어 번역)
- 관련 기술 분석
+ 콜백, 리액티브 그리고 코루틴
+ 코루틴, 중단함수, 잡(Job) 이들의 관계는 무엇인가?
+ threadLocal.asContextElement() 확장 함수는 어떻게 구현되어 있을까?
* Part-6 (Exception Handling)
- 공식 가이드 읽기 (한국어 번역)
- 관련 기술 분석
+ 코루틴은 어떤 상태들을 가지고 있을까?
+ 코루틴 스코프에서 루트 코루틴의 예외 핸들러만이 예외를 처리할 수 있는 이유는 무엇인가?
* Part-7 (Select Expression)
- 공식 가이드 읽기 (한국어 번역)
- 관련 기술 분석
+ select { } 코루틴 빌더는 어떻게 내부에 정의된 onReceive() 구문들을 인식하고, 모든 채널로부터 데이터를 수신 받을까?
+ onSend() 는 수신 후 처리가 지연될 경우 어떻게 select { } 구문 내에 다음 채널로 처리를 양보하는 걸까?
Part-8 (Shared Mutable State and Concurrency)
- 공식 가이드 읽기 (한국어 번역)
- 관련 기술 분석
+ 컨텍스트 전환 시 withContext(context) 를 통한 전환과 CoroutineScope(context) 를 통한 전환의 차이는 무엇일까?
+ actor 는 어떻게 구현되었으며, 왜 부하 상태에서 Lock 보다 효율적일까?
* Part-9 (Flow)
- 공식 가이드 읽기 (한국어 번역)
- 관련 기술 분석
+ flow { } 빌더로 구현된 함수는 왜 suspend 로 마킹되지 않아도 될까?
+ flowOn 연산자를 이용한 컨텍스트 전환은 어떻게 구현되어 있을까?
+ buffer 연산자는 어떻게 구현되어 있을까?
+ conflate 연산자와 xxxLatest 연산자들의 차이는 무엇인가?
