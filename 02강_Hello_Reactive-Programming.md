# 2강  Hello Reactive Programming 

RxSwift 는 값이나 상태의 변화에 따라서 새로운 결과를 도출하는 코드를 비교적 쉽게 구현할 수 있다. 
이러한 프로그래밍 패러다임을 Reactive Programming, 즉 반응형 프로그래밍이라고 한다. 

> 명령형 패러다임

```swift
var a = 1
var b = 2
a + b // 3

a = 12 // 12
```

> 반응형 패러다임

```swift 
let a = BehaviorSubject(value: 1)
let b = BehaviorSubject(value: 2)

Observable.combineLatest(a,b) { $0 + $1 }
.subscribe(onNext: { print($0) } )
.disposed(by: disposeBag)

a.onNext(12) // 3, 14 => 값의 변화를 감지하여 a + b 의 값이 지속적으로 출력되고 있다. 
```