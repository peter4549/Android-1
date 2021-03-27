# [Dagger Hilt로 안드로이드 의존성 주입 시작하기](https://hyperconnect.github.io/2020/07/28/android-dagger-hilt.html)
## Dependency Injection in Andorid
인스턴스를 클래스 외부에서 주입하기 위해서는 인스턴스에 대한 전반적인 생명주기(생성부터 소멸되기까지)의 관리가 필요합니다.
프로젝트의 규모가 커질수록 의존성 인스턴스들을 manual 하게 관리하는 것은 생각보다 많은 리소스가 요구되는데, 이를 전반적으로 관리해주는 것이 대표적으로 Google에서 밀어주고 있는 오픈소스 라이브러리 Dagger2 입니다. Dagger2는 자체적으로 Android와 크게 상관관계가 없지만 Android 환경에서 많은 인기를 끌었고, 이를 인지한 Google은 Android 환경에서 사용할 경우 자연스럽게 늘어나는 보일러 플레이트를 줄여주는 Dagger-Android도 함께 지원해주고 있습니다.

Koin은 사용이 간결하지만 엄밀하게 의존성 주입(Dependency Injection) 개념보다는 Kotlin의 DSL을 활용한 Service Locator Pattern에 가깝고, 결과적으로 프로젝트의 규모가 커질수록 사전(컴파일 타임)에 많은 일을 처리하는 Dagger보다는 런타임 퍼포먼스가 떨어질 수 있습니다. 그래서 많은 안드로이드 개발자분들이 Dagger와 Koin을 비교한 피드백을 끊임없이 제시해왔는데, 기존 Dagger 사용자들의 의견을 수렴한 Google은 기존의 Dagger-Android 보다 초기 구축 비용을 훨씬 절감시킬 수 있고 Android Framework에서 더 강력함을 발휘 할 수 있는 Dagger Hilt를 발표하였습니다.

## New weapon: 🗡 Dagger Hilt
Dagger Hilt는 2020년 6월 Google에서 오피셜하게 발표한 Android 전용 DI 라이브러리입니다. Hilt는 Dagger2를 기반으로 Android Framework에서 표준적으로 사용되는 DI component와 scope를 기본적으로 제공하여, 초기 DI 환경 구축 비용을 크게 절감시키는 것이 가장 큰 목적입니다. 따라서 기존에 불가피하게 작성해야 했던 보일러 플레이트를 대량 줄이고 프로젝트의 전반적인 readability를 향상함으로써, 유지보수 면에서도 큰 이득을 취할 수 있습니다. 그뿐만 아니라, Google에서 전격적으로 지원하는 Jetpack의 ViewModel에 대한 의존성 주입도 별도의 큰 비용 없이 구현할 수 있습니다.

## Gradle Setup
아래의 코드를 project-level의 `build.gradle` 파일에 추가합니다.
```
classpath 'com.google.dagger:hilt-android-gradle-plugin:2.28-alpha'
```
app-level의 `build.gradle` 파일 상단에 아래의 plugin을 추가합니다.
```
apply plugin: 'kotlin-kapt'
apply plugin: 'dagger.hilt.android.plugin'
```
app-level의 `build.gradle` 파일 하단에 아래의 의존성을 추가합니다.
```
implementation "com.google.dagger:hilt-android:2.28.1-alpha"
kapt "com.google.dagger:hilt-android-compiler:2.28.1-alpha"
```

## Hilt Application
Dagger Hilt에서는 `@HiltAndroidApp` 어노테이션을 사용하여 컴파일 타임 시 표준 컴포넌트 빌딩에 필요한 클래스들을 초기화합니다. 따라서 Hilt 셋업을 위해서 필수적으로 요구되는 과정입니다. 아래는 `Application` class를 상속받고 있는 `HakunaApplication` 이라는 클래스에 `@HiltAndroidApp` 를 추가한 예시입니다.
```
@HiltAndroidApp
class HakunaApplication : Application()
```

## Component hierachy
기존의 Dagger2는 개발자가 직접 필요한 component들을 작성하고 상속 관계를 정의했다면, Hilt에서는 Android 환경에서 표준적으로 사용되는 component들을 기본적으로 제공하고 있습니다. 또한 Hilt 내부적으로 제공하는 component들의 전반적인 라이프 사이클 또한 자동으로 관리해주기 때문에 사용자가 초기 DI 환경을 구축하는데 드는 비용을 최소화하고 있습니다. 다음은 Hilt에서 제공하는 표준 component hierarchy 입니다.

![hilt-component](https://hyperconnect.github.io/assets/2020-07-14-android-dagger-hilt/hilt-component.png)
