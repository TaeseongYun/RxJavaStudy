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
