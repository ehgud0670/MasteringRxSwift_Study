# skip skip(while:) skip(until:)

## skip

```swift
public func skip(_ count: Int) -> Observable<Element> {
    SkipCount(source: self.asObservable(), count: count)
}
```

skip 연산자는 정수를 파라미터로 받는다. 옵져버블이 방출하는 요소 중에서 지정된 수만큼 무시한 다음에 
이후에 방출된 요소만 구독자로 전달한다.

```swift 
let disposeBag = DisposeBag()
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

Observable.from(numbers)
    .skip(3)
    .subscribe({ print($0) })
    .disposed(by: disposeBag)

// 실행 결과
// next(4)
// next(5)
// next(6)
// next(7)
// next(8)
// next(9)
// next(10)
// completed 
```

## skip(while:)

```swift
public func skip(while predicate: @escaping (Element) throws -> Bool) -> Observable<Element> {
    SkipWhile(source: self.asObservable(), predicate: predicate)
}
```

skipWhile 연산자는 클로저인 predicate 에서 true를 반환하는동안 그 이전까지의 방출되는 요소를 무시한다.
=> **즉 false 를 반환한 첫 요소부터 마지막까지 조건에 관계없이 방출하는 연산자, true인 동안엔 방출을 skip하는 연산자이다.**

```swift
let disposeBag = DisposeBag()
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

Observable.from(numbers)
    .skip(while: { element in
        return [1].contains(element)
    })
    .subscribe({ print($0) })
    .disposed(by: disposeBag)

// 실행 결과 
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

## skip(until:)

```swift
public func skip<Source: ObservableType>(until other: Source) -> Observable<Element> {
	SkipUntil(source: self.asObservable(), other: other.asObservable())
}
```

=> ObservableType을 파라미터로 받는다. 이 파라미터 Observable이 next 이벤트를 전달하기 전까지 원본 Observable 이 전달하는 이벤트를 무시(skip)한다.
<br>이런 특징 때문에 파라미터로 받는 Observable을 trigger 라고 부르기도 한다.

```swift
let disposeBag = DisposeBag()

let subject = PublishSubject<Int>()
let trigger = PublishSubject<Int>()

subject.skip(until: trigger)
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

subject.onNext(1)
subject.onNext(2)
subject.onNext(3)

trigger.onNext(1)
subject.onNext(4)
subject.onNext(5)

// 실행 결과 
// next(4)
// next(5)
```
=> trigger가 next 이벤트를 받기 전까지 subject 이 전달하는 이벤트는 무시된다. 
<br> trigger가 next 이벤트를 받은 후부터 subject가 전달하는 이벤트가 전달된다.  
