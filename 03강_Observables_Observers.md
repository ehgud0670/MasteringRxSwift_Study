# Observables and Observers

## 1. Observable & Observer

* Observable 
* Observable Sequence
* Sequence
<br>=> Observable을 Observable Sequence, Sequence라고도 부른다. 

* `Observable` 은 Event를 전달한다.
* `Observer` 는 `Observable` 를 감시하고 있다가 전달되는 Event를 처리한다. `Observable` 을 감시하고 있는 것을 `Subscribe` 라고 부른다.
  * 그래서 `Observer`을 `Subscriber(구독자)`라고 부르기도 한다.

## 2. Observable이 전달하는 세 가지 이벤트 

> 세 가지 이벤트: Next, Error, Completed

* Observable에서 발생한 새로운 Event 는 Next 이벤트를 통해 전달된다. Event에 값이 포함되어 있다면 Next 이벤트와 함께 전달된다. 
  * 값이 Next 이벤트와 함께 전달되는 것을 RxSwift 에서는 **Emission(방출)** 이라고 부른다. 
  * Observable의 Life Cycle 동안 Next 이벤트가 한번도 전달되지 않은 경우가 있고, 하나 이상 전달되는 경우도 있다. 

* Observable 에서 Error가 발생하면 Error 이벤트가 전달되고 종료된다.
* Observable이 정상적으로 종료되면 Completed 이벤트가 전달된다.
* Error, Completed 이벤트는 Observable 의 Life Cycle 에서 가장 마지막에 전달된다. 이후 Observable이 종료되고, 모든 리소스가 정리되기 때문에 다른 이벤트는 전달되지 않는다. 
* Error, Completed 이벤트는 Emission(Next) 라 부르지 않고 Notification 이라고 부른다. 

## 3. Observable을 생성하는 2가지 방법

* 1번째 방법은 `create` 연산자를 통해서 Observable의 동작을 직접 구현하는 방법이다. 

> Note: create 연산자는 ObservableType Protocol에 선언되어 있는 타입 메소드이다. RxSwift 에서는 이러한 메소드를 연산자(operator)라고 부른다. 

```swift
// #1
Observable<Int>.create { observer -> Disposable in
  observer.on(.next(0)) // observer 로 0 이 저장되어 있는 next 이벤트가 전달된다.
  observer.onNext(1) // observer 로 1 이 저장되어 있는 next 이벤트가 전달된다.
  observer.on(.completed) // observer 로 completed 이벤트가 전달되고 observable이 종료된다. 이후에 다른 이벤트를 전달할 순 없다.
  
  return Disposables.create() // Disposable 은 메모리 정리에 필요한 개체(Entity)다. Class 타입이 아닌 Struct 타입.
}
```

* 2번째 방법은 미리 정의된 규칙에 따라서 이벤트를 전달하는 방법이다. 

* from 연산자 사용

from 연산자는 파라미터로 전달된 배열 값을 순서대로 전달하고 completed 이벤트를 전달하는 Observable 을 생성한다.

```swift
// #2
Observable.from([0, 1]) // 0 -> 1 순으로 값 next 이벤트로 전달 
```

* 지금까지의 코드는 **Observable을 생성하는 것**까지의 코드이다.
다시 말해, 값이 Observer로 전달되지 않고 Observable은 Observer에게 어떤 순서대로 값이 전달되어야 할지 정의할 뿐이다. 

* 그런 값이 전달되는 시점은 언제인가. 바로 Observer가 Observable 을 subscribe 하는 순간이다. 이 시점에 0, 1이 next 이벤트를 통해 전달되고 이후 completed 이벤트가 전달되면 Observable은 종료된다. 
