# Behavior Subject

## 특징 

* BehaviorSubject는 이니셜라이져에 파라미터로 value을 넣어야 한다. 

```swift
let publishSubject = PublishSubject<Int>() 
let behaviorSubject = BehaviorSubject<Int>(value: 1) // publishSubject와 달리 생성시 value을 집어넣어야 한다. 숫자인건 현재 제네릭 데이터 타입이 Int 이기 때문.
```

* BehaviorSubject는 생성시 파라미터로 넣어준 value를 저장해놓았다가 observer가 구독하면 observer에게 해당 value를 next 이벤트로 바로 전달한다. 

```swift 
let publishSubject = PublishSubject<Int>() 
let behaviorSubject = BehaviorSubject<Int>(value: 1)

publishSubject.subscribe { print("PublishSubject >>", $0) } // 노 출력
behaviorSubject.subscribe { print("BehaviorSubject >>", $0) } // BehaviorSubject >> next(1)
```
=> PublishSubject를 구독해도 Subject가 저장한 값이 없어 어떤 것도 출력하지 않는 반면(또 publish subject를 구독한 observer는 구독한 이후의 이벤트만 전달받는다), BehaivorSubject를 구독하면 저장한 value를 옵저버가 전달받아 해당 이벤트를 출력하고 있다. 

```swift
let publishSubject = PublishSubject<Int>()
let behaviorSubject = BehaviorSubject<Int>(value: 1)

publishSubject.subscribe { print("PublishSubject >>", $0) } // 노 출력
behaviorSubject.subscribe { print("BehaviorSubject >>", $0) } // BehaviorSubject >> next(1)

behaviorSubject.onNext(2)

behaviorSubject.subscribe { print("BehaviorSubject 2 >>", $0) }

// 실행 결과 
// BehaviorSubject >> next(1)
// BehaviorSubject >> next(2)
// BehaviorSubject 2 >> next(2)
```
=> 실행 결과를 보면 알 수 있듯 BehaviorSubject는 Observer가 구독한 시점에 가장 최신의 next 이벤트를 Observer로 전달한다. 



```swift
let publishSubject = PublishSubject<Int>()
let behaviorSubject = BehaviorSubject<Int>(value: 1)

publishSubject.subscribe { print("PublishSubject >>", $0) } // 노 출력
behaviorSubject.subscribe { print("BehaviorSubject >>", $0) } // BehaviorSubject >> next(1)

behaviorSubject.onNext(2)

behaviorSubject.subscribe { print("BehaviorSubject 2 >>", $0) }

behaviorSubject.onCompleted()

behaviorSubject.subscribe { print("BehaviorSubject 3 >>", $0) }

// 실행 결과
// BehaviorSubject >> next(1)
// BehaviorSubject >> next(2)
// BehaviorSubject 2 >> next(2)
// BehaviorSubject >> completed
// BehaviorSubject 2 >> completed
// BehaviorSubject 3 >> completed
```
=> BehaviorSubject가 completed 이벤트를 전달받아 종료되었으므로 그 이후에 구독한 Observer는 아무런 Next이벤트도 받지 않고 completed 이벤트만 전달받는다. Error로 비정상 종료를 한 경우에도 마찬가지다.