# filter 

filter 연산자는 클로저를 파라미터로 받고, 이 클로저는 predicate 로 사용된다. 클로저에서 true를 반환하는 요소가 Observable이 방출하는 요소에 포함된다.
**즉 filter 클로저 조건에 부합하는 요소들만 방출된다는 뜻이다.**

```swift
Observable.from(numbers)
    .filter({ return $0 % 2 == 0 })
    .subscribe({ print($0) })
    .disposed(by: disposeBag)

// 실행 결과
// next(2)
// next(4)
// next(6)
// next(8)
// next(10)
// completed
```
