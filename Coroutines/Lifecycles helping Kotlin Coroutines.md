# [Lifecycles helping Kotlin Coroutines](https://fornewid.medium.com/lifecycles-helping-kotlin-coroutines-275991883ba8)
## Android에서 제공하는 모든 LifecycleOwner를 정리해봅니다.

AndroidX에서 제공하는 대부분의 Component가 Coroutines을 지원하고 있습니다. 심지어 최근에는 Kotlin 만으로 작성된 컴포넌트들이 속속 추가되고 있기도 합니다.

## 시작하기 전에
Android에는 Lifecycle이 있어서, 앱 종료 / 화면 전환 등으로 더이상 결과가 필요하지 않게 되면 호출을 멈춰줘야 합니다. 물론 직접 Lifecycle에 따라 CoroutineScope를 잘 관리해주면 됩니다만, 이왕이면 Android Platform에서 직접 제공하는 라이브러리를 사용하는 것이 안정성이나 유지보수 측면에서 더 좋겠죠.

## AndroidX Lifecycle
Lifecycle은 Android Platform에서 직접 제공하는 라이브러리로, KTX에서는 다양한 컴포넌트들의 Lifecycle과 연동되는 CoroutineScope를 제공합니다.

### ViewModelScope
ViewModel에서 사용할 수 있는 CoroutineScope입니다.  
사용하려면 아래처럼 KTX 라이브러리를 설치해야 합니다.

<pre>
implementation "androidx.lifecycle:**lifecycle-viewmodel-ktx**:$version"
</pre>

`onCleared()`가 호출되면 중지됩니다.

### LifecycleScope
ViewModel을 사용하지 않는다면, LifecycleScope를 고려해볼 수 있습니다. Activity, Fragment 같은 [LifecycleOwner](https://developer.android.com/reference/androidx/lifecycle/LifecycleOwner) 구현체에서 사용할 수 있습니다.  
사용하려면 아래처럼 KTX 라이브러리를 설치해야 합니다.

```
implementation "androidx.lifecycle:lifecycle-runtime-ktx:$version"
```

`DESTROYED` 상태가 되면 중지됩니다.

```
class MyActivity : AppCompatActivity() {
  private fun method() {
    lifecycleScope.launch {
      doSomething()
    }
  }
}
```

주의해야 할 점은 Fragment는 2개의 Lifecycle이 있으므로 LifecycleOwner를 잘 선택해줘야 합니다. 대부분의 경우에는 ***viewLifecycleOwner***를 사용하는 것을 권장드립니다.
<pre>
class MyFragment : Fragment() {
  private fun method() {
    viewLifecycleOwner.lifecycleScope.launch {
      doSomething()
    }
  }
}
</pre>
