# map

* 전달하는 요소를 map의 파라미터인 클로저에서 정의한대로 변환한 뒤 return 하는 operator

> 예제 

```swift
let disposeBag = DisposeBag()
let skills = ["Swift", "SwiftUI", "RxSwift"]

Observable.from(skills)
    .map({ return "Hello, \($0)" })
    .map({ return $0.count })
    .subscribe({ print($0) })
    .disposed(by: disposeBag)

// 실행 결과 
// next(12)
// next(14)
// next(14)
// completed
```