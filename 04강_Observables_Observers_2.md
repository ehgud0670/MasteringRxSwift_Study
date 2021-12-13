# Observables and Observers 2

* 실제로 Observable에서 이벤트가 전달되는 시점은 최초로 Observer에서 해당 Observable 을 구독(subscribe)한 시점이다.
* Observer는 Observable에서 전달된 이벤트를 처리한다. 이를 구독(subsribe)라고 한다. Observer == Subscriber 

## Subscribe 연산자 

* Observer가 Observable 을 구독하는 방법은 **Observable이 subscribe 메소드(연산자)를 호출하는 것**이다.
  * Subscribe 메소드는 **Observable 과 Observer를 연결**한다.
  * Observable과 Observer를 연결해야 이벤트가 전달되므로 subscribe 메소드 호출은 RxSwift 에서 가장 기초적이고 필수적인 작업이다. 

> 첫번째 방법

```swift
firstObservable.subscribe({
  print($0)
  if let element = $0.element {
    print(element)
  }

  // 실행 결과
  // next(0)
  // 0
  // next(1)
  // 1
  // completed
}) 
```

* 이 클로저로 이벤트가 전달되고 이 클로저에서 이벤트를 처리한다. 바로 이 클로저가 Observer 이다.

> 두번째 방법

```swift
public func subscribe(
    onNext: ((Element) -> Void)? = nil, 
    onError: ((Swift.Error) -> Void)? = nil, 
    onCompleted: (() -> Void)? = nil, onDisposed
    : (() -> Void)? = nil
)
```

* 이 subscribe 메소드를 보면 이벤트 별로 파라미터로 받는 것을 알 수 있다. 
* 개별 이벤트들을 개별 클로저로 처리하고 싶을때 사용한다. 

```swift
firstObservable.subscribe(onNext: { element in
  print("== Start ==")
  print(element)
  print("== End ==")
})

// 실행 결과 
// == Start ==
// 0
// == End ==
// == Start ==
// 1
// == End ==
```

### Observer는 동시에 2개 이상의 이벤트를 처리하지 않는다. 바꾸어 말하면, Observable은 Observer가 하나의 이벤트를 처리한 후에 이어지는 이벤트를 전달한다. 여러 이벤트를 동시에 전달하지 않는다.
