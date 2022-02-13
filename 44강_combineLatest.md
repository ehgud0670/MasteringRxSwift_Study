# combineLatest

```swift
public static func combineLatest<O1: ObservableType, O2: ObservableType> (_ source1: O1, _ source2: O2, resultSelector: @escaping (O1.Element, O2.Element) throws -> Element) -> Observable<Element> {
    return CombineLatest2(source1: source1.asObservable(), source2: source2.asObservable(),
            resultSelector: resultSelector
        )
}
```

* combineLatest 연산자는 source observable들을 combine(결합)한다. 
<br>source observables를 결합하고 parameter로 전달된 함수(클로저)를 실행해 결과를 방출하는 새로운 observable을 리턴한다.

* 가장 최신값끼리 결합해 방출한다. 

> 예제

```swift
let bag = DisposeBag()

enum MyError: Error {
   case error
}

let greetings = PublishSubject<String>()
let languages = PublishSubject<String>()

Observable.combineLatest(greetings, languages) { lhs, rhs in
  print(lhs, rhs)
}
.subscribe({ print($0) })
.disposed(by: bag)

greetings.onNext("1")
languages.onNext("2")

greetings.onNext("1")
languages.onNext("3")

// 실행 결과
// 1 2
// next(())
// 1 2
// next(())
// 1 3
// next(()) 
```

* combineLatest는 param의 모든 observable이 onCompleted 이벤트를 보내야 종료된다. 
* combineLatest는 어떤 observable이 completed 되었다면 해당 옵져버블의 마지막 최신값을 방출할 때마다 계속 사용한다.
* combineLatest도 param으로 받은 observable중에 하나라도 에러로 인한 비정상 종료를 하면 그 즉시 종료된다. 


```swift
let bag = DisposeBag()

enum MyError: Error {
   case error
}

let greetings = PublishSubject<String>()
let languages = PublishSubject<String>()

Observable.combineLatest(greetings, languages) { lhs, rhs in
  print(lhs, rhs)
}
.subscribe({ print($0) })
.disposed(by: bag)

greetings.onNext("1")
languages.onNext("2")

greetings.onNext("1")
languages.onNext("3")

greetings.onCompleted()
languages.onNext("4")

languages.onCompleted()
// 실행 결과 
// 1 2
// next(())
// 1 2
// next(())
// 1 3
// next(())
// 1 4
// next(())
// completed
```

link: https://reactivex.io/documentation/operators/combinelatest.html