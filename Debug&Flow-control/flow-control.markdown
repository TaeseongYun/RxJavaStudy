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
