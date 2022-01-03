# repeatElement

repeatElement 는 element 를 무한정으로 방출하는 연산자이다. 
따라서 무한루프에 빠지기 때문에 사용할 때 take로 개수를 제한한다든가 조치를 취하자. 

```swift
let disposeBag = DisposeBag()
let element = "❤️"

Observable.repeatElement(element)
  .take(7)
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

// next(❤️)
// next(❤️)
// next(❤️)
// next(❤️)
// next(❤️)
// next(❤️)
// next(❤️)
// completed
```