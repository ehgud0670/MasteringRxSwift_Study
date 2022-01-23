# toArray

* 요소를 모두 받아 onCompleted 이벤트를 받았을때 한번에 배열로 전달하는 operator.

> 예제

```swift
let elements = [1, 2, 3, 4, 5]

let disposeBag = DisposeBag()
let subject = PublishSubject<Int>()

subject
    .toArray()
    .subscribe({ print($0) })
    .disposed(by: disposeBag)

elements.forEach { subject.onNext($0) }
subject.onCompleted()

// 실행 결과 
// success([1, 2, 3, 4, 5])
```

> NOTE 
<br>에러로 인한 비정상 종료일때는 error이벤트만 전달되고 종료한다.
 