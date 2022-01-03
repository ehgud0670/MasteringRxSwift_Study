# range, generate

## range

range 연산자는 start의 정수 값부터 count만큼 차례대로 정수를 방출한다. 아래의 예시를 들면 1부터 10까지 방출된다.

```swift
let disposeBag = DisposeBag()

Observable.range(start: 1, count: 10)
   .subscribe { print($0) }
   .disposed(by: disposeBag)

// 실행 결과 
// next(1)
// next(2)
// next(3)
// next(4)
// next(5)
// next(6)
// next(7)
// next(8)
// next(9)
// next(10)
// completed
```

## generate 

> 증가되는 크기를 바꾸거나 감소하는 Sequence 를 보이고 싶다면 generate 연산자를 사용하자.

```swift
public static func generate(
    initialState: Element, 
    condition: @escaping (Element) throws -> Bool, scheduler: ImmediateSchedulerType = CurrentThreadScheduler.instance, 
    iterate: @escaping (Element) throws -> Element
    )
```
* initialState: 시작값, 가장 먼저 방출되는 값.
* condition: true를 리턴하는 경우에만 요소를 방출한다. 반대로 false를 리턴하면 completed 이벤트를 방출하고 바로 종료한다.  
* iterate: 그 다음 값을 바꾸는 로직이 담겨있는 클로저 파라미터이다. 보통 로직은 값을 증가시키거나 감소시킨다. 

```swift
Observable.generate(
  initialState: 0,
  condition: { $0 <= 10 },
  iterate: { $0 + 2 }
).subscribe(onNext: {
  print($0)
}).disposed(by: disposeBag)

// 출력 결과 
// 0
// 2
// 4
// 6
// 8
// 10
```

> 또 generate 는 element 가 정수로 국한되어 있지않는 것도 특징이다.

```swift
let disposeBag = DisposeBag()
let red = "🔴"
let blue = "🔵"

Observable.generate(
  initialState: red,
  condition: { $0.count < 15 },
  iterate: { $0.count.isMultiple(of: 2) ? $0 + red : $0 + blue }
).subscribe(onNext: {
  print($0)
}).disposed(by: disposeBag)

// 출력 결과 
// 🔴
// 🔴🔵
// 🔴🔵🔴
// 🔴🔵🔴🔵
// 🔴🔵🔴🔵🔴
// 🔴🔵🔴🔵🔴🔵
// 🔴🔵🔴🔵🔴🔵🔴
// 🔴🔵🔴🔵🔴🔵🔴🔵
// 🔴🔵🔴🔵🔴🔵🔴🔵🔴
// 🔴🔵🔴🔵🔴🔵🔴🔵🔴🔵
// 🔴🔵🔴🔵🔴🔵🔴🔵🔴🔵🔴
// 🔴🔵🔴🔵🔴🔵🔴🔵🔴🔵🔴🔵
// 🔴🔵🔴🔵🔴🔵🔴🔵🔴🔵🔴🔵🔴
// 🔴🔵🔴🔵🔴🔵🔴🔵🔴🔵🔴🔵🔴🔵
```