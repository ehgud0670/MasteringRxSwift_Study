# deferred

* deferred 연산자를 사용하면 특정 조건에 맞게 observable 을  생성할 수 있다.

* Bool 타입에 toggle 이란 메소드가 있는지 처음 알았네. 신기하구만.

```swift
let disposeBag = DisposeBag()
let animals = ["🐶", "🐱", "🐹", "🐰", "🦊", "🐻", "🐯"]
let fruits = ["🍎", "🍐", "🍋", "🍇", "🍈", "🍓", "🍑"]
var flag = true

let factory: Observable<[String]> = Observable<[String]>.deferred {
  flag.toggle()

  if flag {
    return Observable.just(animals)
  } else {
    return Observable.just(fruits)
  }
}

factory.subscribe({ print($0) })
  .disposed(by: disposeBag)

factory.subscribe({ print($0) })
  .disposed(by: disposeBag)

factory.subscribe({ print($0) })
  .disposed(by: disposeBag)

// 실행 결과
// next(["🍎", "🍐", "🍋", "🍇", "🍈", "🍓", "🍑"])
// completed
// next(["🐶", "🐱", "🐹", "🐰", "🦊", "🐻", "🐯"])
// completed
// next(["🍎", "🍐", "🍋", "🍇", "🍈", "🍓", "🍑"])
// completed
```