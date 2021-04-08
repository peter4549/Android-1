# [Lifecycles helping Kotlin Coroutines](https://fornewid.medium.com/lifecycles-helping-kotlin-coroutines-275991883ba8)
## Android에서 제공하는 모든 LifecycleOwner를 정리해봅니다.

AndroidX에서 제공하는 대부분의 Component가 Coroutines을 지원하고 있습니다. 심지어 최근에는 Kotlin 만으로 작성된 컴포넌트들이 속속 추가되고 있기도 합니다.

## 시작하기 전에
Android에는 Lifecycle이 있어서, 앱 종료 / 화면 전환 등으로 더이상 결과가 필요하지 않게 되면 호출을 멈춰줘야 합니다. 물론 직접 Lifecycle에 따라 CoroutineScope를 잘 관리해주면 됩니다만, 이왕이면 Android Platform에서 직접 제공하는 라이브러리를 사용하는 것이 안정성이나 유지보수 측면에서 더 좋겠죠.

## AndroidX Lifecycle
Lifecycle은 Android Platform에서 직접 제공하는 라이브러리로, KTX에서는 다양한 컴포넌트들의 Lifecycle과 연동되는 CoroutineScope를 제공합니다.

![https://miro.medium.com/max/1400/1*RLQ4Ue-togVAUKSlDPmQqw.png](https://miro.medium.com/max/1400/1*RLQ4Ue-togVAUKSlDPmQqw.png)

### ViewModelScope
ViewModel에서 사용할 수 있는 CoroutineScope입니다.  
사용하려면 아래처럼 KTX 라이브러리를 설치해야 합니다.

<pre>
implementation "androidx.lifecycle:<b>lifecycle-viewmodel-ktx</b>:$version"
</pre>

`onCleared()`가 호출되면 중지됩니다.

### LifecycleScope
ViewModel을 사용하지 않는다면, LifecycleScope를 고려해볼 수 있습니다. Activity, Fragment 같은 [LifecycleOwner](https://developer.android.com/reference/androidx/lifecycle/LifecycleOwner) 구현체에서 사용할 수 있습니다.  
사용하려면 아래처럼 KTX 라이브러리를 설치해야 합니다.

<pre>
implementation "androidx.lifecycle:<b>lifecycle-viewmodel-ktx</b>:$version"
</pre>

`DESTROYED` 상태가 되면 중지됩니다.

<pre>
class MyActivity : AppCompatActivity() {
  private fun method() {
    <b><i>lifecycleScope.</i></b>launch {
      doSomething()
    }
  }
}
</pre>

⚠️ 주의해야 할 점은 Fragment는 2개의 Lifecycle이 있으므로 LifecycleOwner를 잘 선택해줘야 합니다. 대부분의 경우에는 ***viewLifecycleOwner***를 사용하는 것을 권장드립니다.
<pre>
class MyFragment : Fragment() {
  private fun method() {
    <b><i>viewLifecycleOwner.lifecycleScope.</i></b>launch {
      doSomething()
    }
  }
}
</pre>

### LifecycleService
간혹 Background 작업이 필요한 경우도 있는데요. WorkManager를 사용하는 경우에는 CoroutineWorker를 지원하므로 손쉽게 사용할 수 있겠지만, Service는 Platform API라서 CoroutineScope를 직접 관리해야만 할 것 같습니다.

다행히도 AndroidX Lifecycle에서는 Service도 지원하고 있습니다. 👏 LifecycleService는 LifecycleOwner를 구현한 Service 확장 클래스입니다.
사용하려면 아래처럼 확장 라이브러리를 추가로 설치해야 합니다.

<pre>
implementation "androidx.lifecycle:<b>lifecycle-service</b>:$version"
</pre>

기존에 사용하던 Service를 **LifecycleService**로 변경한 후, ***lifecycleScope***를 사용하면 됩니다.

<pre>
class MyService : <b>LifecycleService()</b> {
  private fun method() {
    <b><i>lifecycleScope.</i></b>launch {
      doSomething()
    }
  }
}
</pre>

### ProcessLifecycleOwner
더 나아가 Application 전역으로 처리하고 싶은 경우도 있을 수 있는데요.  
Kotlin에서 제공하는 [GlobalScope](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-global-scope/)를 사용해야 하나, 아니면 항상 WorkManager와 같은 대체 API를 이용해야 하나 고민이 됩니다.

이런 경우에 사용할 수 있는 LifecycleOwner가 제공됩니다.  
사용하려면 확장 라이브러리를 추가로 설치해야 합니다.

<pre>
implementation "androidx.lifecycle:<b>lifecycle-process</b>:$version"
</pre>

<pre>
<b>ProcessLifecycleOwner</b>.get()<b><i>.lifecycleScope.</i></b>launch {
    doSomething()
}
</pre>

### ViewTreeLifecycleOwner (alpha)
마지막으로 **View**에서도 LifecycleOwner가 제공됩니다.  
사용하려면 라이브러리 버전을 올려야 합니다.

<pre>
implementation "androidx.lifecycle:<b>lifecycle-runtime:2.3.0</b>-alpha07"
</pre>

정확하게는 View의 Lifecycle이 아닌, View가 붙어있는 Activity/Fragment의 Lifecycle을 가져옵니다. 따라서 View가 attach되지 않은 경우에는 `null`이 반환될 수 있습니다.

<pre>
<b>ViewTreeLifecycleOwner</b>.get(view)?.lifecycleScope?.launch {
    doSomething()
}

// Using KTX
view.<b><i>findViewTreeLifecycleOwner</i></b>()?.lifecycleScope?.launch {
    doSomething()
}
</pre>

### 정리
사용처에 따른 API를 매치하고 마무리하겠습니다.

* Activity, Fragment — `lifecycleScope`
* View (in ⍺)— `ViewTreeLifecycleOwner` + `lifecycleScope`
* ViewModel — `viewModelScope`
* Service — `LifecycleService` + `lifecycleScope`
* Application — `ProcessLifecycleOwner` + `lifecycleScope`
