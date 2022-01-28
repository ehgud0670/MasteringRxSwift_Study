# groupBy

* key와 GroupedObservable이 핵심이다.

```swift
let disposeBag = DisposeBag()
let words = ["Apple", "Banana", "Orange", "Book", "City", "Axe"]

Observable.from(words)
  .groupBy(keySelector: { $0.count })
  .subscribe { event in
    if let observable = event.element {
      print(observable, )
      
      observable.subscribe({ print($0) })
        .disposed(by: disposeBag)
    }
  }
  .disposed(by: disposeBag)

// 실행 결과
// GroupedObservable<Int, String>(key: 5, source: RxSwift.(unknown context at $1114c7028).GroupedObservableImpl<Swift.String>) 5
// next(Apple)
// GroupedObservable<Int, String>(key: 6, source: RxSwift.(unknown context at $1114c7028).GroupedObservableImpl<Swift.String>) 6
// next(Banana)
// next(Orange)
// GroupedObservable<Int, String>(key: 4, source: RxSwift.(unknown context at $1114c7028).GroupedObservableImpl<Swift.String>) 4
// next(Book)
// next(City)
// GroupedObservable<Int, String>(key: 3, source: RxSwift.(unknown context at $1114c7028).GroupedObservableImpl<Swift.String>) 3
// next(Axe)
// completed
// completed
// completed
// completed
```

```swift
let disposeBag = DisposeBag()
let words = ["Apple", "Banana", "Book", "Orange", "City", "Axe"]

Observable.from(words)
  .groupBy(keySelector: { $0.count })
  .flatMap { $0.toArray() }
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

// 실행 결과
// next(["Axe"])
// next(["Book", "City"])
// next(["Banana", "Orange"])
// next(["Apple"])
// completed
```

```swift
let disposeBag = DisposeBag()
let words = ["Apple", "Banana", "Book", "Orange", "City", "Axe"]

Observable.from(words)
  .groupBy(keySelector: { $0.first ?? Character(" ") })
  .flatMap { $0.toArray() }
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

// 실행 결과
// next(["Orange"])
// next(["Apple", "Axe"])
// next(["City"])
// next(["Banana", "Book"])
// completed
```

```swift
Observable.range(start: 1, count: 10)
  .groupBy(keySelector: { return $0 % 2 == 0})
  .flatMap { $0.toArray() }
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

// 실행 결과
// next([2, 4, 6, 8, 10])
// next([1, 3, 5, 7, 9])
// completed
```