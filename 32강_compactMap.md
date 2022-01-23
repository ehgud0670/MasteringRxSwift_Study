# compactMap 

* compactMap을 적용한 operator. 따라서 반환된 요소들은 모두 non-optional로 변환된 값들이고 당연히 nil인 값은 필러링되어 존재하지 않는다.

```swift
let disposeBag = DisposeBag()
let optionals: [Int?] = [nil, 1, nil, 2, nil, 3, nil, 4]
Observable.from(optionals)
    .compactMap( {$0} )
    .subscribe({ print($0) })
    .disposed(by: disposeBag)

// 실행결과
// next(1)
// next(2)
// next(3)
// next(4)
// completed
```