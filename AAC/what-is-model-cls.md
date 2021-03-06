## What is Model cls

서버나 로컬 디비, 로컬 파일시스템에서 가져오는 데이터를 가져오는 클래스를 의미 (기존 데이터는 아님).

data source (서버나 로컬에서 가져오는 데이터 소스 클래스)

- 원격해서 가져오는 데이터 RemoteDataSource(네이밍), 로컬 기기에서 가져오는 데이터 LocalDataSource(네이밍) 유지보수로 인해 만들어줌.

- 레포지토리 -> RemoteDataSource, LocalDataSource 두 개를 가지고 데이터를 어디서 가져올것인지 결정한다. 액티비티에서 구현할경우 어디서 가져올것인지 액티비티가 전부 다 알고있는상태. 매우 안 좋은 방법. 액티비티에선 레포지토리에 call에 맞는 데이터만 요청한다. 그럼 레포지토리에선 확인 후 다시 액티비티로 데이터 보내줌.

데이터 가공하는건 패턴이 없을 시 액티비티 에서 가공 패턴이 존재할 시 vm 나 presenter 에서 가공

repository 에선 싱글톤이 따로 필요가 없음. 필요없는 로직.

RxCallAdapter 까보기

## MVP

View 단에서는 무엇을 보여줄지만 구현을하고 비즈니스 로직은 P 단(Presenter)에서 처리를 한다.

리사이클러 어뎁터는 뷰에 가깝다.
Presenter에서 context가 필요하면 ViewModel과 같이 ContextProvider같은 클래스를 하나 만들어서 의존성 주입을 시킨다.

## MVVM

MVVM 이라 하면 Model View ViewModle 이렇게 나눠지는 패턴이다. 뷰에선 뷰 모델의 데이터 갱신이 이루어지면 뷰 모델의 데이터를 관찰하고 있다가 변경과 동시에 변경 감지가 되면
해당 변경에 관련된 뷰를 새롭게 뿌려주는 형식이다.

하지만 보통 개발자들은 AAC ViewModel과 MVVM의 ViewModel을 헷갈려한다. (보통 두개 같은 것 이라고 알고있다.)

두 개 차이점을 보자하면 ViewModel은 정말 View에 대한 데이터를 가지고 있는 ViewModel 클래스이고 AAC ViewModel은 보통 액티비티의 로케이트가 변경 되면 뷰의 데이터가 사라진다.
간단한 데이터 같은 경우는 `onSaveInstanceState()` 에서 번들로 처리하여 `onCreate()`의 번들로 가져오는 형식으로 구현한다.

## Android Clean Architecture

이번 첫 멀티모듈을 도입하게 되면서 클린 아키택처를 고려하게되었다.

개발자에 따라 계층이 나뉘어지는데 보통 계층은 다음과 같이 나누어진다. (엔티티부터 아래로 내려갈 수록 의존성이 생긴다.)

- entity

- data

- domain

- presenter

- remote(개발자가 remote 단 까지 나누었을 때.)

- local (개발자가 local 단 까지 나누었을 때.)

다음 나열한 계층일 하나씩 알아보겠다.

### Entity 레이어

먼저 엔티티 레이어이다. 보통 엔티티 레이어라 함은 오로지 Java와 Kotlin으로만 이루어져 있는 모듈을 말한다. 안드로이드의 의존성은 아예 없다.

만약 개발자가 todo앱을 만든다 함은 Parcelable의 필요성을 못 느끼게 될것이다.

해당 레이어는 제가 따로 구현해보지 않아서 [안드로이드 클릭 아키텍처 구현해보기](https://academy.realm.io/kr/posts/clean-architecture-in-android/)에서 일부를 따왔습니다.

구현을 하게되면 다음과 같은 Entity 코드가 있을것이다.

```kotlin
data class EntityClass(
    val name: String,
    val age: Int
)
```

여기서 가장 중요한점은 해당 `EntitlClass` 의 클래스가 어떤 `Realm`이라던지 `Room`의 개념이 들어가서는 `절대 안된다`.

개념이 들어가게되면 Room 이나 Realm에서 데이터를 바꾸는 순간, Entity 레이어에 영향이 미치기 때문이다.

내용과는 별개로 지금까지 entity 레이어를 구현하지 않고 data 레이어 에서 구현을 하였었다.

### domain 레이어
