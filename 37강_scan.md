# scan 

* 이 연산자는 기본값으로 연산을 시작한다. 
* **seed는 기본값, accumulator는 누산기를 뜻하는 것이 핵심이다.**

> accumulator가 + 일때 

```swift
let disposeBag = DisposeBag()

Observable.range(start: 1, count: 10)
  .scan(0, accumulator: +)
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

// 실행 결과 
// next(1)
// next(3)
// next(6)
// next(10)
// next(15)
// next(21)
// next(28)
// next(36)
// next(45)
// next(55)
// completed
```

> accumulator가 * 일때 

```swift
let disposeBag = DisposeBag()

Observable.range(start: 1, count: 10)
  .scan(1, accumulator: *)
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

// 실행 결과 
// next(1)
// next(2)
// next(6)
// next(24)
// next(120)
// next(720)
// next(5040)
// next(40320)
// next(362880)
// next(3628800)
// completed
```



