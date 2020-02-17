## BehaviorSubject

BehaviorSubject는 구독자가 구독을 하면 가장 최근 값 또는 기본값을 넘겨주는 클래스이다.
예를들어 온도센서에서 변화된 가장 최근값을 받아오는 동작을 구현 할 수 있다.
또한 온도 초기값을 0 으로 설정해두었으면 초기값으로 0을 받아온다.

내가 느끼기엔 BehaviorSubjet는 가장 최근에 onNext한 값 또는 Default 값을 넘겨주긴 하지만 PublishSubject와 비슷하게 onNext보다 subscribe(구독) 이 먼저 일어나면
디폴드 값이 있다면 디폴트 값을 먼저 발행하고 그 뒤로 onNext값을 차레대로 하나씩 발행한다.

ex)

```kotlin
val testSubject = BehaviorSubject.createDefault(1)

disposable += testSubject
            .subscribeOn(Schedulers.io())
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe({Logd("hello1 $it")} ,{...})

testSubect.onNext(4)

testSubect.onNext(6)

disposable += testSubject
            .subscribeOn(Schedulers.io())
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe({Logd("hello2 $it")} ,{...})

testSubject.onNext(7)

testSubject.onNext(9)

disposable += testSubject
            .subscribeOn(Schedulers.io())
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe({Logd("hello3 $it")} ,{...})
```

결과값

hello1 1

hello1 4

hello1 6

hello1 7

hello1 8

hello2 6

hello2 7

hello2 9

hello3 9
