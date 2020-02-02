# BindFunction(결합연산자)

결합 연산자에도 종류가 다양하다. zip, merge, concat, conbineLatest...
지금부터 하나씩 공부하며 쭉 나열해보겠다.

우선 깊게 공부들어가기 전 미리 잠깐 접해보았단 zip 함수부터 하겠다.

- zip

    zip함수 처음에 이해가 잘 안되서 이해하기 쉽도록 내가 한 방식은 알집을 떠올린 방법이다.

    알집이라하면 여러개의 파일을 하나의 .zip확장자로 하나의 파일로 만드는것인데 해당 함수도 비슷한 형태이다.  여러개의 Observable 클래스를 하나의 Observable 클래스로 결합시킨 뒤 묶어준 n개의 Observable로 n개의 데이터를 발행할 수 있다.

    ex)

    ```Kotlin

        val days = listOf("월요일", "화요일", "수요일")

        val whenIsDay = listOf("2월3일", "2월4일", "2월5일")

        val dayObservable = Observable.fromIterable(days)

        val whenIsDaysObservable = Observable.fromIterable(whenIsDay)

        val dayAndWhenIsDaysZip = Observable.zip<String, String, Any>(
            dayObservable, whenIsDaysObservable, BiFunction{t1, t2 -> LogUtil.d("${t1}-${t2}") }
        )

        disposable += dayAndWhenIsDaysZip.subscribe({
            it
        }, {it.printStackTrace()})

    
    결과값 ->     D/MOVIE-APP: 월요일-2월3일
                D/MOVIE-APP: 화요일-2월4일
                D/MOVIE-APP: 수요일-2월5일
    ```



- zipWith

    이 함수도 zip과 매커니즘은 비슷하다 똑같이 Observable n개를 결합하여 n개의 리턴 값을 subscribe 해주는 함수이다. 단 다른점이 있다면 zipWith은 이미 만들어진 Observable 객체에서 이용할 수 있다.


    ex)


    ```Kotlin

        val days = listOf("월요일", "화요일", "수요일")

        val whenIsDay = listOf("2월3일", "2월4일", "2월5일")

        val dayObservable = Observable.fromIterable(days)

        val whenIsDaysObservable = Observable.fromIterable(whenIsDay)

        disposable += dayObservable.zipWith<String,String>(
            whenIsDaysObservable, BiFunction {t1, t2 -> LogUtil.d("${t1}, ${t2}")}
        ).subscribe({it}, {it.print~...})

    
    결과값 ->     D/MOVIE-APP: 월요일-2월3일
                D/MOVIE-APP: 화요일-2월4일
                D/MOVIE-APP: 수요일-2월5일
    ```



    