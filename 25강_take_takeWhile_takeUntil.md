# take, take(while:), take(until:), takeLast

## take

정수를 파라미터로 받아서 해당 숫자 count까지만 요소를 처음부터 방출한다. 이후부터는 방출하지 않는다. 단 completed, error 이벤트는 막지 않는다.

```swift
let disposeBag = DisposeBag()
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

Observable.from(numbers)
  .take(5)
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

// 실행 결과 
// next(1)
// next(2)
// next(3)
// next(4)
// next(5)
// completed
```

## take(while:)

predicate 에서 true를 반환할 때까지만 요소를 방출한다. false를 반환한 순간부터 true이든 false 이든 요소를 방출하지 않는다. 

* 참고: skipWhile은 어떨까? predicate 에서 true를 반환할때 까지만 요소를 무시한다. false를 반환한 순간부터 true이든 false 이든 요소를 방출한다
  * skipWhile, takeWhile 둘 다 이름 참 잘 지었다.

```swift
let disposeBag = DisposeBag()
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

Observable.from(numbers)
  .take(while: { [1, 2, 4, 6, 7].contains($0) })
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

// 실행 결과 
// next(1)
// next(2)
// completed
```

## take(until:)

파라미터로 넣는 ObservableType인 trigger가 next 이벤트를 전달하기 전까지 원본 Observable은 next 이벤트를 전달할 수 있지만, trigger가 next 이벤트를 전달한 후 부터는 next 이벤트를 전달할 수 없고 무시된다. 따라서 operator가 파라미터로 받는 Observable로 인해 동작하기 때문에 해당 파라미터를 trigger라고 부르는 것이다.

```swift
let disposeBag = DisposeBag()
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

let subject = PublishSubject<Int>()
let trigger = PublishSubject<Int>()

subject.take(until: trigger)
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

subject.onNext(1)
subject.onNext(2)
subject.onNext(3)

trigger.onNext(1)

subject.onNext(4)
subject.onNext(5)

// 실행 결과
// next(1)
// next(2)
// next(3)
// completed
```

## takeLast

마지막으로 방출되는 n개의 요소를 completed 이벤트가 전달되기 전에 방출된다. 즉 onCompleted() 메소드가 호출되어야 하지만 방출된다. error 이벤트가 전달되면 아무 값도 방출되지 않는다.

```swift
let disposeBag = DisposeBag()
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
let subject = PublishSubject<Int>()

subject.takeLast(4)
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

numbers.forEach { subject.onNext($0) }
subject.onNext(11)
subject.onNext(12)

subject.onCompleted()

// 실행 결과 
// next(9)
// next(10)
// next(11)
// next(12)
// completed
```