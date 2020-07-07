## Single

Koin에서 자주쓰이는 함수 중 하나인 single이다. 의존성 주입 때 람다안의 객체를 한번만 생성시켜주는 함수로 알고있는데 해당 함수를 알아보기 위해 깊게 들어가보았다.

![single-function](../img/single-function.png)

커스텀 하지 않는 이상 주로 쓰이는 매개변수는 `Definition<T>`

람다식으로 T를 전해준다.

single 함수는 Definition.saveSingle을 사용하게 되고 saveSingle은 createSingle 즉 여기서부터 싱글을 제작한다.

![create-single-function](../img/create-single-function.png)
