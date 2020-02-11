## flatMap

flatMap 이라 하면은 map이랑은 조금 다른 개념이다. 이 때 map이라하면 어떤 함수인가?

map은 코틀린에서도 주로 사용하는 함수인데 n이라는 리스트가 있으면 해당 리스트의 리턴값을 변경 시켜주는것을 의미한다. 즉 새로운 리스트를 다시 생성하여 map 함수에 넣었던 조건처럼 재 리턴한다. 굳이 리스트에서 리스트 형태를 반환하는것이 아니라 String형이 들어온 후 Integer로 반환해도 되고 String을 String으로 integer를 String 으로 반환 해도 상관없다.

ex)

```kotlin
val mapTestList = listOf("1", "2", "3", "4")

val observable = Observable.fromIterable(mapTestList)

disposable += observable.subscribeOn(Schedulers.io)
                    .map{ "$it is number"}
                    .subscribe({it}, {})

```

결과값

D/MOVIE-APP: 1 is number!!

D/MOVIE-APP: 2 is number!!

D/MOVIE-APP: 3 is number!!
  
  
D/MOVIE-APP: 4 is number!!

flatMap은 map 형태를 발전시킨 함수이다. map이 일대일 함수라고하면 flatMap은 일대다 혹은 일대일 Observable 함수이다. 결과값이 Observable로 나온다.

ex

```kotlin
val mapTestList = listOf("1", "2", "3", "4")

val observable = Observable.fromIterable(mapTestList).flatMap {
            Observable.just<String>("$it<> + $it<->")
        }

        disposable += observable
            .subscribeOn(Schedulers.io())
//            .map {
//                when (it.substring(0..0)) {
//                    "1" -> 1
//                    "2" -> 2
//                    "3" -> 3
//                    "4" -> 4
//                    else -> 5
//                }
//            }
            .subscribe({LogUtil.d(it.toString())}, {})
```

결과값

D/MOVIE-APP: 1<> + 1<->

D/MOVIE-APP: 2<> + 2<->

D/MOVIE-APP: 3<> + 3<->

D/MOVIE-APP: 4<> + 4<->

주석 푼 결과값

D/MOVIE-APP: 1

D/MOVIE-APP: 2

D/MOVIE-APP: 3

D/MOVIE-APP: 4

flatMap 은 정말 중요! 주석푼 결과값은 flatMap에서 나온 결과값을 다시 map으로 0번째 스트링을 잘라서 when 분기를 타 알맞은 곳에서 int형을 리턴시켜주었다.
