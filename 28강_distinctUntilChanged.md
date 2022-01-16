# distinctUntilChanged

```swift
public func distinctUntilChanged() -> Observable<Element> {
    self.distinctUntilChanged({ $0 }, comparer: { ($0 == $1) })
}
```
=> Observable이 방출한 이벤트를 순서대로 비교한 다음에 이전 이벤트와 동일하다면 방출하지 않는다. 

```swift
public func distinctUntilChanged<Key: Equatable>(_ keySelector: @escaping (Element) throws -> Key) -> Observable<Element> {
    self.distinctUntilChanged(keySelector, comparer: { $0 == $1 })
}
```
=> keySelector는 요소중에 comparasion key가 되는 것을 반환하는 클로저이다. 아래 예시를 보면 쉽게 알 수 있다.

```swift
let tuples = [(1, "하나"), (1, "일"), (1, "one")]

Observable.from(tuples)
  .distinctUntilChanged({ element in
    element.0
  })
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

// 실행 결과
// next((1, "하나"))
// completed
```

```swift
public func distinctUntilChanged(_ comparer: @escaping (Element, Element) throws -> Bool) -> Observable<Element> {
    self.distinctUntilChanged({ $0 }, comparer: comparer)
}
```
=> 기본 `distinctUntilChanged()`은 단순하게 (`$0 == $1`)으로 비교해 그 이전과 같은 것은 방출하지 않았는데 이 메소드는 직접 comparer 클로저를 넘김으로써 comparer의 조건에 따라 값을 방출한다. 

```swift
Observable.from(numbers)
  .distinctUntilChanged({ $0 != $1 })
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

// 실행 결과
// next(1)
// next(1)
// next(1)
// completed
```
=> 일반적인 distinctUntilChanged의 조건(`$0 == $1`)과 반대로 하니(`$0 != $1`) 같은 값인 1만 방출하고 completed 가 되는 것을 알 수 있다. 

```swift
public func distinctUntilChanged<Property: Equatable>(at keyPath: KeyPath<Element, Property>) -> Observable<Element> {
    self.distinctUntilChanged { $0[keyPath: keyPath] == $1[keyPath: keyPath] }
}
```
=> 결국 keyPath는 keySelector와 같은 역할을 하는 파라미터이다.  

> NOTE: KeyPath는 swift의 문법 중 하나이다. \\. 이게 keyPath 이다. 
```swift
struct Article {
    let id: UUID
    let source: URL
    let title: String
    let body: String
}

let articleIDs = articles.map(\.id)
let articleSources = articles.map(\.source)
```

```swift
let persons = [
    Person(name: "Sam", age: 12),
    Person(name: "Paul", age: 12),
    Person(name: "Tim", age: 56)
]

Observable.from(persons)
  .distinctUntilChanged(at: \.age)
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

// 실행 결과
// next(Person(name: "Sam", age: 12))
// next(Person(name: "Tim", age: 56))
// completed
```