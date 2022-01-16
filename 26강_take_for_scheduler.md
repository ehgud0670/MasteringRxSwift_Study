# take(for:scheduler:)

```swift
public func take(for duration: RxTimeInterval, scheduler: SchedulerType) -> Observable<Element> {
    TakeTime(source: self.asObservable(), duration: duration, scheduler: scheduler)
}
```
=> duration 시간 동안만 요소를 방출하고 그 이후에는 전달하지 않고 completed 되는 operator 이다.

```swift
let disposeBag = DisposeBag()
let observable = Observable<Int>.interval(.seconds(1), scheduler: MainScheduler.instance)

observable.take(for: .seconds(3), scheduler: MainScheduler.instance)
  .subscribe { print($0) }
  .disposed(by: disposeBag)

// 실행 결과 
// next(0)
// next(1)
// completed
```

=> 1초 뒤에 0 방출, 2초 뒤에 1 방출 3초 지나고 끝. 요소 3부터는 3초 뒤에 방출되는 것이므로 방출되지 않고, completed 된다.
=> 이런 연산자를 사용할때는 시간적으로 항상 오차가 있음을 염두하고 사용해야 한다.