# [코루틴 공식 가이드 읽고 분석하기— Part 1 — Dive1](https://myungpyo.medium.com/reading-coroutine-official-guide-thoroughly-part-1-7ebb70a51910)
## CoroutineContext 와 CoroutineScope란 무엇인가?
코루틴을 생성하기 위해서 GlobalScope.launch { … } 블록을 이용하여 코드를 작성했고 launch 중단함수를 코루틴 빌더라고 이야기 했습니다.

코루틴을 이해하기 위해서는 코루틴을 구성하는 두가지 주된 요소인CoroutineContext 와 CoroutineScope 에 대해 먼저 이해해야 합니다.

```
public interface CoroutineContext {
  /**
   * Returns the element with the given [key] from this context or `null`.
   * Keys are compared _by reference_, that is to get an element from the context the reference to its actual key
   * object must be presented to this function.
   */
  public operator fun <E : Element> get(key: Key<E>): E?
  /**
   * Accumulates entries of this context starting with [initial] value and applying [operation]
   * from left to right to current accumulator value and each element of this context.
   */
  public fun <R> fold(initial: R, operation: (R, Element) -> R): R
  /**
   * Returns a context containing elements from this context and elements from  other [context].
   * The elements from this context with the same key as in the other one are dropped.
   */
  public operator fun plus(context: CoroutineContext): CoroutineContext = ...impl...
  /**
   * Returns a context containing elements from this context, but without an element with
   * the specified [key]. Keys are compared _by reference_, that is to remove an element from the context
   * the reference to its actual key object must be presented to this function.
   */
  public fun minusKey(key: Key<*>): CoroutineContext
}

/**
 * Key for the elements of [CoroutineContext]. [E] is a type of element with this key.
 * Keys in the context are compared _by reference_.
 */
public interface Key<E : Element>

/**
 * An element of the [CoroutineContext]. An element of the coroutine context is a singleton context by itself.
 */
public interface Element : CoroutineContext {
  /**
   * A key of this coroutine context element.
   */
  public val key: Key<*>
  
  ...overrides...
}
```

위 코드는 CoroutineContext.kt 파일에 있는 코드 입니다. CoroutineContext 는 4가지 메서드를 가지고 있는데 그 역할은 다음과 같습니다.

* get() : 연산자(operator) 함수로써 주어진 key 에 해당하는 컨텍스트 요소를 반환합니다.
* fold() : 초기값(initialValue)을 시작으로 제공된 병합 함수를 이용하여 대상 컨텍스트 요소들을 병합한 후 결과를 반환합니다.
예를들어 초기값을 0을 주고 특정 컨텍스트 요소들만 찾는 병합 함수(filter 역할)를 주면 찾은 개수를 반환할 수 있고, 초기값을 EmptyCoroutineContext 를 주고 특정 컨텍스트 요소들만 찾아 추가하는 함수를 주면 해당 요소들만드로 구성된 코루틴 컨텍스트를 만들 수 있습니다.
* plus() : 현재 컨텍스트와 파라미터로 주어진 다른 컨텍스트가 갖는 요소들을 모두 포함하는 컨텍스트를 반환합니다. 현재 컨텍스트 요소 중 파라미터로 주어진 요소에 이미 존재하는 요소(중복)는 버려집니다.
* minusKey() : 현재 컨텍스트에서 주어진 키를 갖는 요소들을 제외한 새로운 컨텍스트를 반환합니다.
