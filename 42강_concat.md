# concat 

* 이 연산자는 2개의 Observable을 연결할 때 사용한다. 
  * concat 연산자는 타입 메소드와 인스턴스 메소드가 각각 있다. 

### 타입 메소드 

```swift
public static func concat<Collection: Swift.Collection>(_ collection: Collection) -> Observable<Element> where Collection.Element == Observable<Element> {
    return Concat(sources: collection, count: Int64(collection.count))
}
```

=> 타입 메소드 concat은 파라미터로 전달된 Collection에 있는 모든 Observable을 순서대로 연결한 하나의 Observable을 리턴한다.

> 예제 

```swift
let bag = DisposeBag()
let fruits = Observable.from(["🍏", "🍎", "🥝", "🍑", "🍋", "🍉"])
let animals = Observable.from(["🐶", "🐱", "🐹", "🐼", "🐯", "🐵"])

Observable.concat(fruits, animals)
  .subscribe({ print($0) })
  .disposed(by: bag)

// 실행 결과 
// next(🍏)
// next(🍎)
// next(🥝)
// next(🍑)
// next(🍋)
// next(🍉)
// next(🐶)
// next(🐱)
// next(🐹)
// next(🐼)
// next(🐯)
// next(🐵)
// completed
```

### 인스턴스 메소드

```swift
public func concat<Source: ObservableConvertibleType>(_ second: Source) -> Observable<Element> where Source.Element == Element {
    Observable.concat([self.asObservable(), second.asObservable()])
}
```

=> 대상 Observable이 방출을 성공적으로 끝내면(completed) 매개변수로 넘겨진 Observable의 방출이 시작된다. 
<br>하지만 대상 Observable이 도중에 에러가 발생해 비정상 종료한다면, 매개변수로 넘어온 Observable은 방출되지 않는다. 이건 concat 타입메소드도 마찬가지다.

> 예제

```swift
let bag = DisposeBag()
let fruits = Observable.from(["🍏", "🍎", "🥝", "🍑", "🍋", "🍉"])
let animals = Observable.from(["🐶", "🐱", "🐹", "🐼", "🐯", "🐵"])
  
fruits.concat(animals)
  .subscribe({ print($0) })
  .disposed(by: bag)

// 실행 결과
// next(🍏)
// next(🍎)
// next(🥝)
// next(🍑)
// next(🍋)
// next(🍉)
// next(🐶)
// next(🐱)
// next(🐹)
// next(🐼)
// next(🐯)
// next(🐵)
// completed
```
