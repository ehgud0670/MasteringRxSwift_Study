# empty, error

* 두 연산자는 요소를 방출하지 않는 특별한 연산자들이다. 즉 next 이벤트를 전달하지 않아, 어떠한 요소도 방출하지 않는 연산자들이다.
* 요소를 방출하지 않기 때문에 요소의 타입은 중요하지 않다. 보통은 Void로 선언한다. 

## empty

```swift
let disposeBag = DisposeBag()

Observable<Void>.empty()
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

// 출력 결과 
// completed
```

empty 연산자는 observer가 아무런 동작없이 종료해야 할때 자주 활용된다.

## error

* error 연산자는 에러 이벤트를 전달하고 종료하는 연산자다. 이 연산자는 주로 에러를 처리할 때 사용한다. 

```swift
let disposeBag = DisposeBag()

enum MyError: Error {
   case error
}

Observable<Void>.error(MyError.error)
  .subscribe({ print($0) })
  .disposed(by: disposeBag)
// 실행 결과 
// error(error)
```