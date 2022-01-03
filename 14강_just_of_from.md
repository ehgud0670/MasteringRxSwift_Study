# just, of, from

## just

just 연산자는 파라미터로 받은 element를 그대로 방출한다.
<br> 파라미터로 element 단 한개를 받는다.

```swift
let disposeBag = DisposeBag()
let element = "😀"

Observable.just(element)
   .subscribe { event in print(event) }
   .disposed(by: disposeBag)

Observable.just([1, 2, 3])
   .subscribe { event in print(event) }
   .disposed(by: disposeBag)

// 실행 결과
// next(😀)
// completed
// next([1, 2, 3])
// completed
```

## of

of 연산자는 파라미터로 가변 Element 매개변수를 받기 때문에 just와 달리 여러개의 값을 방출할 수 있다. element들이 하나씩 순서대로 방출된다.

```swift
let disposeBag = DisposeBag()
let apple = "🍏"
let orange = "🍊"
let kiwi = "🥝"

Observable.of(apple, orange, kiwi)
   .subscribe { element in print(element) }
   .disposed(by: disposeBag)

Observable.of([1, 2], [3, 4], [5, 6])
   .subscribe { element in print(element) }
   .disposed(by: disposeBag)

// 실행 결과
// next(🍏)
// next(🍊)
// next(🥝)
// completed
// next([1, 2])
// next([3, 4])
// next([5, 6])
// completed
```

## from

from 연산자는 파라미터로 배열 타입을 받아 배열의 element를 순서대로 방출하는 연산자다. 

```swift
let disposeBag = DisposeBag()
let fruits = ["🍏", "🍎", "🍋", "🍓", "🍇"]

Observable.from(fruits)
   .subscribe { element in print(element) }
   .disposed(by: disposeBag)

// 실행 결과 
// next(🍏)
// next(🍎)
// next(🍋)
// next(🍓)
// next(🍇)
// completed
```

