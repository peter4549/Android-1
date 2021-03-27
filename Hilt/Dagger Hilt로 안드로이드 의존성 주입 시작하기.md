# [Dagger Hilt로 안드로이드 의존성 주입 시작하기](https://hyperconnect.github.io/2020/07/28/android-dagger-hilt.html)
## Dependency Injection in Andorid
인스턴스를 클래스 외부에서 주입하기 위해서는 인스턴스에 대한 전반적인 생명주기(생성부터 소멸되기까지)의 관리가 필요합니다.
프로젝트의 규모가 커질수록 의존성 인스턴스들을 manual 하게 관리하는 것은 생각보다 많은 리소스가 요구되는데, 이를 전반적으로 관리해주는 것이 대표적으로 Google에서 밀어주고 있는 오픈소스 라이브러리 Dagger2 입니다. Dagger2는 자체적으로 Android와 크게 상관관계가 없지만 Android 환경에서 많은 인기를 끌었고, 이를 인지한 Google은 Android 환경에서 사용할 경우 자연스럽게 늘어나는 보일러 플레이트를 줄여주는 Dagger-Android도 함께 지원해주고 있습니다.

Koin은 사용이 간결하지만 엄밀하게 의존성 주입(Dependency Injection) 개념보다는 Kotlin의 DSL을 활용한 Service Locator Pattern에 가깝고, 결과적으로 프로젝트의 규모가 커질수록 사전(컴파일 타임)에 많은 일을 처리하는 Dagger보다는 런타임 퍼포먼스가 떨어질 수 있습니다. 그래서 많은 안드로이드 개발자분들이 Dagger와 Koin을 비교한 피드백을 끊임없이 제시해왔는데, 기존 Dagger 사용자들의 의견을 수렴한 Google은 기존의 Dagger-Android 보다 초기 구축 비용을 훨씬 절감시킬 수 있고 Android Framework에서 더 강력함을 발휘 할 수 있는 Dagger Hilt를 발표하였습니다.
