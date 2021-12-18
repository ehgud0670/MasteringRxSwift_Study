# Async Subject

* async subject는 completed 이벤트를 전달받기 전까지 어떠한 이벤트도 observer에 전달하지 않는다. completed 이벤트가 전달되면 가장 최근에 전달되었던 next 이벤트 하나를 observer에게 전달한다. 

```swift
let bag = DisposeBag()

enum MyError: Error {
   case error
}

let subject = AsyncSubject<Int>()
subject.subscribe { print($0) }
       .disposed(by: bag)


subject.onNext(1)
subject.onNext(2)
subject.onNext(3)
subject.onCompleted()

// 실행 결과 
// next(3)
// completed
```
=> completed 이벤트가 전달되면 가장 최근에 전달되었던 next 이벤트 하나를 observer에게 전달한다. 


```swift
let bag = DisposeBag()

enum MyError: Error {
   case error
}

let subject = AsyncSubject<Int>()
subject.subscribe { print($0) }
       .disposed(by: bag)

subject.onNext(1)
subject.onNext(2)
subject.onNext(3)
subject.onError(MyError.error)

// 실행 결과
// error(error)
```
=> 반면 Error로 인한 비정상 종료인 경우에는 completed 이벤트와 달리 next 이벤트를 전달하지 않는다. 

