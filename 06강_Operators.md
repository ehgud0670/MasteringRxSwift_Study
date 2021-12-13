# Operators

`public protocol ObservableType: ObservableConvertibleType`

* 프로토콜 ObservableType에는 RxSwift 를 근간이 되는 여러 메소드가 있다. 
  * 새로운 Observable 을 생성하는 메소드도 있고,
  * 방출되는 요소를 filtering 하거나
  * 여러 Observable 을 하나로 합치는 메소드도 있다. 

* RxSwift 에서는 이러한 메소드를 **연산자(operator)** 라고 부른다.
* 대부분의 연산자는 Observable 상에서 동작하고, 새로운 Observable 을 리턴한다. Observable 을 리턴하기 때문에 2개 이상의 연산자를 연속으로 사용할 수 있다. 
* Operator들은 subsribe 메소드 앞에 위치해야 한다. 

### 예제

```swift
let disposeBag = DisposeBag()

Observable.from([1, 2, 3, 4, 5, 6, 7, 8, 9])
  .filter({ $0.isMultiple(of: 2)})
  .take(5)
  .subscribe { print($0) }
  .disposed(by: disposeBag)

// 실행 결과 
// next(2)
// next(4)
// next(6)
// next(8)
// completed
```

* take 연산자는 source Observable 이 방출하는 요소 중에서 파라미터로 지정한 수만큼만 순서대로 방출하는 새로운 옵저버블을 생성한다. 
* filter 연산자는 클로저 내용을 충족하는 요소들만 방출하는 새로운 옵저버블을 생성한다. 
* 호출 순서에 주의하자.
