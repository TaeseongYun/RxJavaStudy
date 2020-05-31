## RxJava Error Custom

RxJava를 사용하며 핸들링을 잘 처리하지 못할 때 스트림 에러가 일어나면 항상 에러처리를 하지 않아 스트림 연결관계가 끊어져 더 이상 구독하지 못하는 상황이 종종 발생되곤 한다.

이럴 때 오류 핸들링을 할 수 있게하는 Operator 함수가 몇 가지 존재한다.

### Rxjava 에러처리 오퍼레이터 함수

- onError
- onErrorReturn
- onErrorResumeNext
- retry
- 에러 처리하지않고 완료하기.

## onError
