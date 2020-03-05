## Koin안에서의 뷰 모델

- koin이라 함은 코인 설명글에서 보았듯이 dagger보다 러닝커브가 조금 낮은 의존성에 대한 라이브러리이다. 그 중 viewModel 이라는 함수에 대해 알아보려고 한다.

## koin없이 ViewModel 초기화

- 우선 코인의 viewmodel에 들어가기 앞서 AAC ViewModel은 어떤식으로 초기화가 이루어지는지 알아 볼 필요가있다.

  ViewModel을 초기화 하기 위해선 두 가지가 필요한데 그 두 가지는 바로 ViewModel을 상속받고있는 ViewModel클래스 하나와 ViewModelProvider클래스이다.

  ex)

  ```kotlin
  lateinit var testViewModel: TestViewModel

  fun onCreate(...) {
      testViewModel = ViewModelProvider.of(this).get(TestViewModel::class.java)
  }
  ```

  하지만 ViewModel을 상속받은 클래스에 생성자에 매개변수가 존재한다면?(커스텀 생성자)

  `ViewModelProvider.Factory`를 반드시 사용해야한다.

  ### 사용방법?

  ```kotlin
  // TestViewModel에 커스텀 생성자에 매개변수가 String형이 있다고 가정.

  //팩토리 클래스를 하나 만들어 줌.
  class TestViewModelFactory(val test: String) : ViewModelProvider.Factory {
        override fun <T : ViewModel?> create(modelClass: Class<T>): T =
                modelClass.getConstructor(String::class.java).newInstance(test)

    }

    //MainActivity

    lateinit var testViewModel: TestViewModel

    fun onCreate(...) {
        private val testViewModelFactory = TestViewModelFactory("HelloWorld")

        testViewModel = ViewModelProvider.of(context, testViewModelFactory).get(TestViewModel::class.java)
    }
  ```

  ViewModelProvider.Factory는 ViewModel을 인스턴스(객체)화 하기위해 자체적 구현이 필요. 즉 ViewModel은 액티비티나 프래그먼트에서 따로 객체를 생성할 수 없기 때문에 뷰 모델의 constructor에서 인자값이 존재 할 시 객체를 만드려면 ViewModelProvider.Factory를 통해서 만들어 줘야한다.
  그럼 ViewModelProvider.Factory에서는 ViewModel의 객체를 만들어 줄 것이다.

## Koin의 viewModel(model의 확장함수.)

- 이제 그럼 본격적으로 koin의 viewModel에 들어가 보도록하자.


    koin의 viewModel을 들어가보면 모듈의 확장함수가 나오게 된다.

    ![koin-viewmodel함수](../../RxJava/img/koin_viewModel.png)

    함수를 보게되면 factory 모듈 함수가 하나 존재하는데 해당 함수는 inject 할 때마다  새로운 객체를 생성한다.

    주로 사용하게 될 것은 viewModel 함수안의 definition: Definition<T> 매개변수인데 해당 매개변수 클래스를 다시 보게 되면.

    ``` kotlin
    typealias Definition<T> = Scope.(DefinitionParameters) -> T
    ```

    이렇게 되어있다. T를 리턴하는 Scope클래스의 확장함수(매개변수는 DefinitionParameters) 이름을 Definition으로 사용중이다.

    T는 매개변수로 들어갈 ViewModel 클래스를 의미.

    또 해당 함수 안에를 보면

    ``` kotlin
    fun BeanDefinition<*>.setIsViewModel() {
        properties[ATTRIBUTE_VIEW_MODEL] = true
    }
    ```

    이라는 함수가 있는데 해당 함수는 properties의 키값으로 isViewModel를 집어넣고 해당 모듈은 뷰 모델이다 라는걸 캐싱해준다.

    이로인해 모듈로 지정된 ViewModel은 ViewModel 이란 것을 인지하게 된다.

## 마무리

- DI 없이 뷰 모델을 생성하기 위해선 커스텀 생성자가 아닌 뷰 모델은 Factory 생성없이 바로 사용 가능하지만 커스텀 생성자일 경우 ViewModelProvider.Factory를 상속받은 클래스를 만들어야한다.

- ViewModel은 `따로 인스턴스를 생성하지 못함!!!!`

- koin 으로 viewModel을 생성 할 경우 factory 형으로 생성이 되기 때문에 inject를 여러번 할 경우 새로운 인스턴스를 만든다.
