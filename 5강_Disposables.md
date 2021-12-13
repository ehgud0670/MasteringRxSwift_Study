# Disposables 

```swift 
Observable.from([1, 2, 3])
  .subscribe(onNext: { element in
    print("Next ", element)
  }, onError: { error in
    print("Error ", error)
  }, onCompleted: {
    print("Completed")
  }, onDisposed: {
    print("Disposed")
  })

// 실행 결과 
// Next  1
// Next  2
// Next  3
// Completed
// Disposed
```

* disposed 는 `Observable` 이 전달하는 이벤트는 아니다. 
  * onDisposed 파라미터로 클로저를 전달하면 Observable와 관련된 모든 리소스가 제거된 후에 호출된다.
  * disposed 이벤트는 Observable과 관련된 리소스들이 제거되는 시점에 자동으로 전달되는 이벤트로 리소스들이 모두 해제되는 시점에 어떤 코드를 호출하고 싶다면 onDisposed 파라미터에 넣으면 된다. 

> Disposable 

리소스 정리에 사용되는 개체이다. 
<br>리소스 정리하는 데에는 2가지 방법이 있다.

```swift 
// #1
subscription.dispose()
// #2
subscription.disposed(by: disposeBag)
```

* dispose: 이 메소드가 호출되는 시점에 Disposable 개체는 바로 해지된다. 
* disposed(by: disposebag): disposbag이 해지(deallocate)되는 시점에 같이 해지된다. 즉 리소스 정리가 된다. 

### 그럼 내가 원하는 시점에 Disposable 을 해지하는 방법은?

```swift
// #1.
disposeBag = DisposeBag()
// #2.
disposeBag = nil
```

=> disposeBag을 새로 할당하거나 nil처리 해서 이전에 있던 disposBag 객체를 해지 시켜 기존의 disposable들을 같이 해지시킨다.

### 값이 끊임없이 생성되는 경우 

```swift
let subscription2 = Observable<Int>.interval(.seconds(1), scheduler: MainScheduler.instance)
  .subscribe(onNext: { element in
    print("Next ", element)
  }, onError: { error in
    print("Error ", error)
  }, onCompleted: {
    print("Completed")
  }, onDisposed: {
    print("Disposed")
})

DispatchQueue.main.asyncAfter(deadline: .now() + 3.0, execute: {
  subscription2.dispose()
})
// 실행 결과 
// Next  0
// Next  1
// Next  2
// Disposed
```
<br>=> from 연산자는 컴파일 시점에 전달되는 값들이 정해져있기 때문에 Observable이 모든 값을 보내고 completed 이벤트를 보낸다. 
그 이후에 Obseravable 과 관련된 모든 리소스가 해지되고, dispose 이벤트가 자동으로 전달된다. 

=> 이에 반해 위 코드의 interval 연산자는 1초마다 0부터 1씩 증가시키며 값(Next 이벤트에 넣어서)을 보낸다. 런타임에 계속 값을 보내는 연산자로 따로 completed 를 보내지 않아 계속적으로 끝이 없이 값을 방출할 것이다. 따라서 이러한 경우에는 위 코드처럼 dispose 메소드를 사용하여 종료시키거나 disposeBag을 nil 처리 or 새로 객체 할당시켜(`disposed(by:)`를 사용한 경우) Disposable을 해지시켜야 한다.

