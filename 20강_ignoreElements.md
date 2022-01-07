# ignoreElements 

이 연산자는 Observable이 방출하는 next 이벤트는 필터링하고, completed 이벤트와 error 이벤트만 구독자에게 전달한다. 

> ignoreElements를 적용하지 않은 경우 

```swift
let disposeBag = DisposeBag()
let fruits = ["🍏", "🍎", "🍋", "🍓", "🍇"]

Observable.from(fruits)
    .subscribe({ print($0) })
    .disposed(by: disposeBag)

// 실행 결과
// next(🍏)
// next(🍎)
// next(🍋)
// next(🍓)
// next(🍇)
// completed
```

> ignoreElements를 적용한 경우 

=> 아래 결과처럼 ignoreElements를 사용하면 Observable이 방출하는 next 이벤트를 필터링한다. 

```swift
let disposeBag = DisposeBag()
let fruits = ["🍏", "🍎", "🍋", "🍓", "🍇"]

Observable.from(fruits)
    .ignoreElements()
    .subscribe({ print($0) })
    .disposed(by: disposeBag)

// 실행 결과
// completed
```

* ignoreElements 구현 내부 모습 

```swift
public func ignoreElements() -> Observable<Never> {
    self.flatMap { _ in Observable<Never>.empty() }
}
```
