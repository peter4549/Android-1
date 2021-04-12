# [Fragment Lifecycle과 LiveData](http://pluu.github.io/blog/android/2020/01/25/android-fragment-lifecycle/)
## 생명주기
### Android의 생명 주기

<p align="center">
  <img src="https://developer.android.com/images/topic/libraries/architecture/lifecycle-states.svg" />
</p>
      
> 출처 : https://developer.android.com/topic/libraries/architecture/lifecycle

### 현실의 생명주기 흐름

<p align="center">
  <img src="https://miro.medium.com/max/694/1*ALMDBkuAAZ28BJ2abmvniA.png" />
</p>

> 출처 : [The Android Lifecycle cheat sheet — part III : Fragments](https://medium.com/androiddevelopers/the-android-lifecycle-cheat-sheet-part-iii-fragments-afc87d4f37fd)

> https://github.com/xxv/android-lifecycle/blob/master/complete_android_fragment_lifecycle.png?raw=true 

## Fragment와 Activity의 생명주기 상관관계
Fragment의 생명주기는 Attach 되는 Activity의 생명주기에 영향을 받습니다. 예를 들어, Activity가 일시 정지되면 그 안에 모든 Fragment도 일시 정지되며 Activity가 파괴될 때 모드 Fragment도 마찬가지로 파괴됩니다.

## Fragment에서 LiveData 사용하기
아래는 LiveData와 Fragment 소스의 일부분을 발췌했습니다.

```
// LiveData.java
public abstract class LiveData<T> {
   public void observe(
      @NonNull LifecycleOwner owner, @NonNull Observer<? super T> observer) {
      ...
   }
}
```

```
// Fragment.java
public class Fragment implements ComponentCallbacks, OnCreateContextMenuListener, 
   LifecycleOwner, ViewModelStoreOwner, vedStateRegistryOwner {
   ...
}
```

### 문제의 Lifecycle 사용 패턴
Lifecycle과 LiveData의 문제가 처음 언급된 곳은 [android/architecture-components-samples issue #47](https://github.com/android/architecture-components-samples/issues/47) 이슈입니다. 이슈에서 언급하는 내용은 LiveData#observe로 사용된 Observer가 중복으로 호출된다는 내용입니다.

문제가 되는 코드를 다시 한번 보겠습니다.

```
viewModel.isUpdate.observe(this, Observer<Boolean> {
   // TODO: something
}
```

위 코드는 LiveData#observe의 LifecycleOwner에 해당하는 파라미터로 Fragment 자신을 넘기는 코드입니다. 다음으로 또 다른 Activity와 Fragment의 Lifecycle의 흐름을 살펴보겠습니다.

<p align="center">
  <img src="https://miro.medium.com/max/1596/1*hK_YRdty1GoafABfug-r4g.png" />
</p>

> 출처 : [The Android Lifecycle cheat sheet — part III : Fragments](https://medium.com/androiddevelopers/the-android-lifecycle-cheat-sheet-part-iii-fragments-afc87d4f37fd)

위 이미지에서 볼 수 있는 것처럼 Fragment는 Actvitiy와 다르게 `onDestroy` 가 호출되지 않은 상태에서 `onCreateView` 가 여러 번 호출될 수 있습니다. 이로 인해 Fragment의 Lifecycle은 Destroy 되지 않은 상황에서 LiveData에 새로운 Observer가 등록되어 복수의 Observer가 호출되는 현상이 발생할 가능성이 있습니다.

## Fragment의 생명주기 다시 살펴보기
여러분이 놓치고 있는 정보 중 하나는 바로 `Fragment에는 2개의 Lifecycle이 존재`하는 점입니다. 바로 `Fragment Lifecycle`과 `Fragment View Lifecycle`입니다.

### Fragment View Lifecycle의 시작
Fragment Lifecycle은 본 글의 처음에 언급한 Fragment의 생명 주기에 해당하는 내용입니다. 그리고, Fragment View Lifecycle은 `문제의 Lifecycle 사용 패턴`을 개선하기 위해서 도입된 Lifecycle입니다.

먼저, Google Android Developer인 Ian Lake씨의 Fragment 수정 로그를 보겠습니다.

```
Add Fragment#getViewLifecycleOwner

The Fragment's View lifecycle can diverge
from the Fragment's lifecycle in cases of
detached Fragments. This can cause issues with
LiveData where old observers should be cleared
when the View is destroyed to prevent duplication
with new observers created in onCreateView/onViewCreated.

By exposing a separate LifecycleOwner specifically for
the Fragment's View, developers can use that in place
of the Fragment itself to better model the Lifecycle
they actually care about.

Also adds a getViewLifecycleOwnerLiveData() for
observing changes in the View LifecycleOwner (i.e.,
creation, destruction, and recreation).
```

> 링크 : https://android.googlesource.com/platform/frameworks/support/+/eb89fcf1decf9044f53330ea4bb689d25d2328b1%5E%21/fragment/src/main/java/androidx/fragment/app/Fragment.java

Fragment는 Fragment View보다 긴 생명주기를 가지며, 일반적으로 UI를 업데이트용으로는 `Fragment View Lifecycle` 이 적절합니다. 그리고, Fragment View Lifecycle 도입으로 LiveData#observe에서 사용하는 Observer 중복 호출 문제도 해결할 수 있습니다. Fragment 사용 시 데이터 갱신에 대한 Lifecycle을 Fragment Lifecycle보다 Fragment View Lifecycle이 올바르다고 언급하고 있습니다.

* Fragment Lifecycle : Create ~ Destroy
* Fragment View Lifecycle : createView ~ destoryView

### 올바른 Fragment에서 LiveData 사용

```
// Bad 
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
   viewModel.liveData.observe(this, Observer {
      // Update UI
   })
}
```

기존 LiveData 사용에서 바뀌는 부분은 liveData를 observe 함수의 첫 번째 파라미터를 this에서 viewLifecycleOwner를 호출하도록 수정하면 됩니다.

```
// Good 
override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
   viewModel.liveData.observe(viewLifecycleOwner, Observer {
      // Update UI
   })
}
```

## Lifecycle 문제에 대한 자세한 발표 영상
아래 영상은 I/O ‘19 what’s new in Architecture Components 세션이며 Fragment와 LiveData 사용에 필요한 Lifecycle에 대해서 언급하고 있습니다.

[![pErTyQpA390](https://img.youtube.com/vi/pErTyQpA390/maxresdefault.jpg)](https://youtu.be/pErTyQpA390)

## Updated, Fragment 1.2.0
FragmentContainerView, ViewModel, FragmentManager 등 몇 가지 변화가 되었습니다. 그중 이번 글과 관련된 기능 하나가 추가되었습니다.

> 링크 : [Fragment 1.2.0 Release note](https://developer.android.com/jetpack/androidx/releases/fragment#1.2.0)

#### Fragment Version 1.2.0

* **New Lint checks**: Added a new Lint check that ensures you are using `getViewLifecycleOwner()` when observing `LiveData` from `onCreateView()`, `onViewCreated()`, or `onActivityCreated()`.

Fragment 1.2.0부터 새로운 Lint가 추가되었습니다. onCreateView(), onViewCreated(), onActivityCreated()에서 LiveData 사용 시에는 ViewLifecycleOwner를 사용하도록 권장하는 것입니다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/80195353/114423249-f7b21980-9bf1-11eb-90fc-3d89dde92aa5.png" />
</p>

LiveData#observe에서 this를 사용하는 경우 위와 같은 메시지를 경험합니다. 빨간 밑줄과 안내 메시지가 노출되지만, 빌드 실패는 아닙니다. 이제 우리가 해야 할 일은 바로 Fragment View Lifecycle 객체를 반환하는 `viewLifecycleOwner`를 사용하는 것입니다.

> 위에서 언급한 Fragment에서 LiveData#observe 사용에 관한 Lint 구현이 궁금하신 분은 아래 링크를 참고하세요.
> 
> https://android.googlesource.com/platform/frameworks/support/+/androidx-master-dev/fragment/fragment-lint/src/main/java/androidx/fragment/lint/UnsafeFragmentLifecycleObserverDetector.kt


