# element(at:)

## element(at:) 내부 구현 모습

```swift
public func element(at index: Int) -> Observable<Element> {
    ElementAt(source: self.asObservable(), index: index, throwOnEmpty: true)
}
```

=> element(at:) 연산자는 정수 index를 파라미터로 받아서 Observable을 리턴한다. 
연산자가 리턴하는 옵져버블은 해당 index에 있는 하나의 element를 방출하고, 
이어서 completed 이벤트를 전달한다. 
<br>결과적으로 구독자에겐 하나의 요소만 전달되고, **원본 옵져버블이 방출하는 나머지 모든 요소는 무시된다.**

```swift
let disposeBag = DisposeBag()
let fruits = ["🍏", "🍎", "🍋", "🍓", "🍇"]

Observable.from(fruits)
    .elementAt(1)
    .subscribe({ print($0) })
    .disposed(by: disposeBag)

// 실행 결과 
// next(🍎)
// completed
```