# Window

* (Observable을 방출하는 Observable)을 리턴하는 operator이다.
* buffer 연산자처럼 timeSpan과 count를 파라미터로 갖는다. 

> Note: Observable을 방출하는 Observable을 InnerObservable 이라고 한다. (aka. AddRef)


```swift
let disposeBag = DisposeBag()

Observable<Int>.interval(.seconds(1), scheduler: MainScheduler.instance)
  .window(timeSpan: .seconds(2), count: 3, scheduler: MainScheduler.instance)
  .take(5)
  .subscribe({ event in
    if let observable = event.element {
      observable.subscribe({ print($0) })
        .disposed(by: disposeBag)
    }
  })
  .disposed(by: disposeBag)

// 실행 결과
// next(0)
// completed
// next(1)
// next(2)
// next(3)
// completed
// next(4)
// next(5)
// completed
// next(6)
// next(7)
// completed
// next(8)
// next(9)
// completed
```


```swift
let disposeBag = DisposeBag()

Observable<Int>.interval(.seconds(1), scheduler: MainScheduler.instance)
  .window(timeSpan: .seconds(5), count: 3, scheduler: MainScheduler.instance)
  .take(5)
  .subscribe({ event in
    if let observable = event.element {
      observable.subscribe({ print($0) })
        .disposed(by: disposeBag)
    }
  })
  .disposed(by: disposeBag)

// 실행 결과 
// next(0)
// next(1)
// next(2)
// completed
// next(3)
// next(4)
// next(5)
// completed
// next(6)
// next(7)
// next(8)
// completed
// next(9)
// next(10)
// next(11)
// completed
// next(12)
// next(13)
// next(14)
// completed
```