# 1강: Hello, RxSwift 

* 인상 깊은 한마디: 모든 내용의 공통적인 결론은 RxSwift 를 사용하면 **단순하고 직관적인 코드**를 작성할 수 있다는 것이다.


## 강의 코드 

```swift
import UIKit
import RxSwift

let disposeBag = DisposeBag()

Observable.just("Hello, RxSwift")
    .subscribe { print($0) }
    .disposed(by: disposeBag)
```
