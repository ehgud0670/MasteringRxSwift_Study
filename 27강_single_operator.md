# single operator

```swift
public func single() -> Observable<Element> {
    SingleAsync(source: self.asObservable())
}
```

```swift
public func single(_ predicate: @escaping (Element) throws -> Bool) -> Observable<Element> {
    SingleAsync(source: self.asObservable(), predicate: predicate)
}
```

원본 observable에서 첫번째 요소만 방출(`single()`)하거나, 
<br>조건과 일치하는 첫번째 요소만 방출(`single(_ predicate: @escaping (Element) throws -> Bool)`)하는 operator 이다.

```swift
Observable.just(1)
  .single()
  .subscribe( { print($0) })
  .disposed(by: disposeBag)

// 실행 결과 
// next(1)
// completed
```

```swift
let subject = PublishSubject<Int>()
subject.single()
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

subject.onCompleted()

// 실행 결과 
// error(Sequence doesn't contain any elements.)
```

```swift
let disposeBag = DisposeBag()
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

Observable.from(numbers)
  .single()
  .subscribe( { print($0) })
  .disposed(by: disposeBag)

// 실행 결과
// next(1)
// error(Sequence contains more than one element.)
```

=> 이 경우에는 error(`Sequence contains more than one element.`)이벤트가 전달된다.
<br>=> 즉, single 연산자는 하나의 요소만 방출될때만 정상적으로 종료하고, 원본 Observable이 요소를 방출하지 않거나, 하나를 방출하고 그 이후에도 요소가 있는 경우에는 error를 전달하며 비정상 종료하는 것을 알 수 있다.

```swift
let disposeBag = DisposeBag()
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

Observable.from(numbers)
  .single({ $0 == 3 })
  .subscribe( { print($0) })
  .disposed(by: disposeBag)

// 실행 결과
// next(3)
// completed
```
=> 요소가 3인 경우가 하나만 있으므로 3을 방출하고 정상 종료(onCompleted)한다.

```swift
let disposeBag = DisposeBag()
let numbers = [3, 3]

Observable.from(numbers)
  .single({ $0 == 3 })
  .subscribe( { print($0) })
  .disposed(by: disposeBag)

// 실행 결과 
// next(3)
// error(Sequence contains more than one element.)
```
=> 3인 요소가 1개를 초과(2개)하므로 마지막에 error이벤트를 보내며 비정상 종료한다.

single 연산자가 이벤트를 전달하는 시점을 subject를 사용하며 생각해보자.
single 연산자는 하나의 요소가 방출되는 것을 보장한다. 

```swift
let subject = PublishSubject<Int>()
subject.single()
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

subject.onNext(100)

// 실행 결과
// next(100)
```
=> single()이 리턴하는 observable은 원본 observable이 completed 이벤트를 전달할 때까지 대기한다. 
=> 따라서 요소가 하나 방출했지만 subject는 더 요소를 전달받을 수 있는 객체이므로 onCompleted 가 전달되지는 않는다. subject가 completed 이벤트를 전달받아야지만 observable이 종료된다.
<br>=> 또 같은 논리로 요소를 하나 더 전달받으면 error이벤트를 전달하며 비정상 종료한다.

요약: single()은 하나의 요소 이벤트가 전달되는 것을 보장한다. 