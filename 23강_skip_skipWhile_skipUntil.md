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
=> **즉 false 를 반환한 첫 요소부터 마지막까지 조건에 관계없이 방출하는 연산자이다.**