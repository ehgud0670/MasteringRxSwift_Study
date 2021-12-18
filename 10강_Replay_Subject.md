# Replay Subject 

* Behavior Subject는 저장한 가장 최신의 next 이벤트 한개를 구독자에게 전달하는 subject이다. 
* 따라서 2개(버퍼 사이즈가 2 이상인 경우) 이상 저장했다가 구독자에게 전달하는 싶은 상황이 생기는데 이를 대비해 만든 것이 ReplaySubject이다. 
* ReplaySubject는 버퍼 사이즈 크기 이하의 next 이벤트를 버퍼에 저장하고 있다가 구독자가 구독하는 시점에 모든 next 이벤트를 전달한다.  observer에게 구독하기 이전의 저장한 next 이벤트를 모두(=버퍼 사이즈만큼) 전달하고 싶을때 replaySubject를 사용하자.

```swift
let disposeBag = DisposeBag()

enum MyError: Error {
   case error
}

let replaySubject = ReplaySubject<Int>.create(bufferSize: 3)

(1...10).forEach { replaySubject.onNext($0) }

replaySubject.subscribe { print("ReplaySubject >>", $0) }
             .disposed(by: disposeBag)
```

=> 10개의 이벤트를 보냈는데 그 직후에 구독한 observer가 3개만 전달받고 있다. 그 이유는 ReplaySubject가 가장 최신의 next 이벤트를 버퍼 사이즈 3만큼 저장하기 때문이다. 

* 버퍼는 메모리에 저장되기 때문에 항상 메모리 사용량에 신경써야 한다. 필요 이상으로 큰 버퍼를 사용하는 것은 피해야 한다. 

```swift
let disposeBag = DisposeBag()

enum MyError: Error {
   case error
}

let replaySubject = ReplaySubject<Int>.create(bufferSize: 3)

(1...10).forEach { replaySubject.onNext($0) }

replaySubject.subscribe { print("Observer 1 >>", $0) }
             .disposed(by: disposeBag)

replaySubject.subscribe { print("Observer 2 >>", $0) }
             .disposed(by: disposeBag)

replaySubject.subscribe { print("Observer 3 >>", $0) }
             .disposed(by: disposeBag)

replaySubject.onCompleted()

replaySubject.subscribe { print("Observer 4 >>", $0) }
             .disposed(by: disposeBag)

// 실행 결과
// Observer 1 >> next(8)
// Observer 1 >> next(9)
// Observer 1 >> next(10)
// Observer 2 >> next(8)
// Observer 2 >> next(9)
// Observer 2 >> next(10)
// Observer 3 >> next(8)
// Observer 3 >> next(9)
// Observer 3 >> next(10)
// Observer 1 >> completed
// Observer 2 >> completed
// Observer 3 >> completed
// Observer 4 >> next(8)
// Observer 4 >> next(9)
// Observer 4 >> next(10)
// Observer 4 >> completed
```
=>  ReplaySubject가 completed 이벤트를 전달받아 종료되어도 그 이후에 구독한 전달자에게도 버퍼에 저장된 Next 이벤트를 전달하고 Completed 이벤트를 전달한다. 에러도 마찬가지다. 즉 종료 여부의 관계없이 버퍼에 저장된 Next이벤트를 옵저버에게 전달한다.