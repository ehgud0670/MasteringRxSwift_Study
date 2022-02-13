# startWith 

```swift
public func startWith(_ elements: Element ...) -> Observable<Element> {
  return StartWith(source: self.asObservable(), elements: elements)
}
```

* 이 연산자는 Observable이 요소를 방출하기 전에 다른 항목들을 앞부분에 추가해 먼저 방출한다. 주로 기본값이나 시작값을 지정할 때 활용한다. 

```swift
let bag = DisposeBag()
let numbers = [1, 2, 3, 4, 5]

Observable.from(numbers)
  .startWith(-3)
  .startWith(-1, -2)
  .startWith(0)
  .subscribe({ print($0) })
  .disposed(by: bag)

// 실행결과
// next(0)
// next(-1)
// next(-2)
// next(-3)
// next(1)
// next(2)
// next(3)
// next(4)
// next(5)
// completed
```

* startWith은 lastIn-firstOut이다. 가장 마지막에 들어간 값이 가장 먼저 호출된다(위 예시로는 -3,-1,-2,0 순으로 들어갔으므로 0이 먼저 방출된다).

