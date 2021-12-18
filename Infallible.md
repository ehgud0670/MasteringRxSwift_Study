# Infallible

사전적 의미

* 절대 보증할 수 있는 
* 결코 틀리지 않은 
* 절대 옳은

새로운 형태의 Observable이고, Error 이벤트는 방출하지 않고 Next와 Completed 이벤트만 방출하는 옵저버블이다.
Error 가 방출되지 않는다는 것이 컴파일 타임 시점에 보장되어 있다.

```swift
let infallible = Infallible<String>.create { (observer) -> Disposable in
//  observer.on(.next("1"))  infallible은 해당 메소드 없음. 컴파일 에러
//  observer.onNext("1")  infallible은 해당 메소드 없음. 컴파일 에러
  observer(.next("1"))
  observer(.next("2"))
  observer(.completed)
  
  return Disposables.create()
}
```