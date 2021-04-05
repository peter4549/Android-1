# [Lifecycles helping Kotlin Coroutines](https://fornewid.medium.com/lifecycles-helping-kotlin-coroutines-275991883ba8)
## Android에서 제공하는 모든 LifecycleOwner를 정리해봅니다.

AndroidX에서 제공하는 대부분의 Component가 Coroutines을 지원하고 있습니다. 심지어 최근에는 Kotlin 만으로 작성된 컴포넌트들이 속속 추가되고 있기도 합니다.

## 시작하기 전에
Android에는 Lifecycle이 있어서, 앱 종료 / 화면 전환 등으로 더이상 결과가 필요하지 않게 되면 호출을 멈춰줘야 합니다. 물론 직접 Lifecycle에 따라 CoroutineScope를 잘 관리해주면 됩니다만, 이왕이면 Android Platform에서 직접 제공하는 라이브러리를 사용하는 것이 안정성이나 유지보수 측면에서 더 좋겠죠.
