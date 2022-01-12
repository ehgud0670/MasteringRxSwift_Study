# skip(duration: scheduler:)

duration동안 Observable 이 방출하는 요소들은 모두 무시(skip)된다.

```swift
let disposeBag = DisposeBag()

let observable = Observable<Int>.interval(.seconds(1), scheduler: MainScheduler.instance)

observable.take(10)
    .skip(.seconds(5), scheduler: MainScheduler.instance)
    .subscribe { print($0) }
    .disposed(by: disposeBag)

// 실행 결과
// next(4)
// next(5)
// next(6)
// next(7)
// next(8)
// next(9)
// completed
```

=> 5초가 지나고 4부터 방출이 된다. 이해가 안된다면 직접 해보시길.

* 또 추가로 시간에 오차가 있다는 것(예를 들어 초단위면 밀리세컨즈의 오차가 있다)을 인지하고 사용하자.