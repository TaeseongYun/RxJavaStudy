## 흐름제어

### 1. throttleFirst() / throttleLast()함수

이름만 들어서는 절대 알 수 없는 함수이다. 이번에 핸들러를 rxjava로 교체하는 과정에서 여러 ui이벤트가 발생했는데 처음 rxjava로 변경했을 때 ui 튀는 이슈가 생겨 이를 어떻게 고칠까 하다
처음엔 delay도 줘보고 filter로 해보고 이것저것 여러가지방식으로 해 보았지만 결국 이 함수를 알기전에는 해결하지못했다.

이제 이 함수에대해 깊게 들어가보도록 하겠다.

- RxJava책을 보면 이렇게 나와있다 throttle은 조절판 그것에 맞게 `throttleFirst()`는 괄호안에 들어간 매개변수의 조건에 가장 먼저 입력된 값을 발행하고 `throllteLast()`
  는 가장 마지막에 입력된 값을 발행한다.

ex)

```kotlin
val array = arrayOf("1", "2", "3", "4", "5", "6")

    val first = Observable.just(array[0])
            .zipWith<Long, String>(
                Observable.timer(100L, TimeUnit.MILLISECONDS),
                BiFunction { t1, _ ->
                    t1
                })

    val second = Observable.just(array[1])
            .zipWith<Long, String>(
                Observable.timer(300L, TimeUnit.MILLISECONDS),
                BiFunction { t1, _ ->
                    t1
                })

    val third = Observable.just(array[2], array[3], array[4], array[5])
            .zipWith<Long, String>(
                Observable.interval(100L, TimeUnit.MILLISECONDS),
                BiFunction { t1, _ ->
                    t1
                })
            .doOnNext {
                Log.d("debug", it)
            }


    disposable += Observable.concat(first, second, third).throttleLast(200L, TimeUnit.MILLISECONDS)
                .subscribe { Log.d("value",it) }

    sleep(1000)
```

결과값은 어떻게 될까? 1,2,4,6 이 Log value로 찍힌다.

이유 -> throttleFirst 조건은 2초에 한 개씩 처음에 들어온 데이터 발행이다. 우선 첫 번째 1은 무조건 value로 들어오고 3초 뒤 2도 발행된다. (2초간 텀이 존재하기 때문)
그 후 2가 들어오고 2초 동안 들어온 데이터는 모두 무시된다. 즉 3이 무시된다.

`2가 들어온 순간부터 2초동안 즉 4가 들어온 순간은 2초가 된다.` 4는 발행!

그 반대로 throttleLast를 알아보자

1이 들어오고 2가 들어오고 반대로 2초가 지나기전에 들어왔던 숫자 3, 3이 들어오고 2초가 지나기전 숫자 5가 찍히게 된다.

### debounce 함수

이전에 behaviorsubject를 이용해서 버튼을 빠르게 눌렀을 때 마지막 누른 값을 받아오게 하는 코드를 작성하려 했었다. 처음엔 다른 흐름제어함수를 사용하지않고 그저 onClick 이벤트가 발생하면 그 즉시 onNext로 다음 데이터 넘겨주고 subscribe로 넘겨준 마지막 가장 최근 데이터를 UI에 뿌려주는 형식으로 구현했으나 데이터를 구독하는 속도보다 발행하는 속도가 너무 빨라 UI가 튀는 현상이 주로 발견되었다.

흐름제어 함수를 알기전에는 delay도 사용해보고 zipWith으로 다른 Observable.timer도 이용해보고 수많은 여러 방법들을 시도해봤지만 똑같은 결론이 나왔다. -> UI튀는현상!

수많은 시행착오끝에 debouce를 알게되었다.

일반 POJO Java 처럼 콘솔에서는 크케 활용할 일이 없지만 안드로이드와 같이 UI 에서는 활용도가 뛰어나다 라고한다. 실행 결과를 알아보자

ex)

```kotlin
val listData = listOf("1", "2", "3", "5")

val source = Observable.concat(
        Observable.timer(100L, TimeUnit.MILLISECONDS).map { listData[0] },
        Observable.timer(100L, TimeUnit.MILLISECONDS).map { listData[1] },
        Observable.timer(300L, TimeUnit.MILLISECONDS).map { listData[2] },
        Observable.timer(300L, TimeUnit.MILLISECONDS).map { listData[3] }
    ).debounce(200L, TimeUnit.MILLISECONDS)

        disposable += source.subscribe {
            LogUtil.d(it)
        }
```

결과물 -> 2, 3, 5

왜 ? debounce는 매개변수로 지정된 시간이 지나서 어떤 이벤트가 발생하지 않으면 예를들어 onNext, onClick... 마지막에 입력된 데이터를 발행한다. 마지막에 입력된 데이터를 발행하기 때문에 debouce가 2초 일 때 listData[0], listData[1]이 두개 동시에 발행됬지만 마지막 데이터를 발행하기 때문에 listData[1] 을 발행

## debounce에 또 하나 알게된 점.

처음에는 debounce가 모든 Observable 클래스에 존재하는 줄 알았지만 Single에는 존재하지 않는 함수이다. 그 이유는 Single이란 클래스는 한번만 호출하는 Observable 연산자 이기 때문에 여러번의 비동기적인 이벤트가 발생하는경우가 없기 때문에 debounce로 비동기적 으로 일어난 이벤트에 대해 시간을 두고 마지막에 일어난 이벤트를 처리할 필요가 없다(개인적인 생각)
