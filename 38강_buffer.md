# buffer

* 이 연산자는 특정 주기동안 Observable 이 방출하는 요소를 수집하고, 하나의 배열로 리턴한다.
<br>RxSwift 에서는 이러한 연산자를 `Controlled Buffering`이라고 한다.

* 해당 연산자는 timeSpan이 끝나면 지금까지 수집한 요소를 배열로 방출하고, 또 새롭게(이전 에 수집한 요소들은 제거하고)수집을 시작한다. 그리고 onCompleted 이벤트가 전달되기 전까지 무한정 진행된다.

```swift
let disposeBag = DisposeBag()

Observable<Int>.interval(.seconds(1), scheduler: MainScheduler.instance)
  .buffer(timeSpan: .seconds(2), count: 3, scheduler: MainScheduler.instance)
  .take(5)
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

// 실행 결과 
// next([0])
// next([1, 2, 3])
// next([4, 5])
// next([6, 7])
// next([8, 9])
// completed
```

```swift
let disposeBag = DisposeBag()

Observable<Int>.interval(.seconds(1), scheduler: MainScheduler.instance)
  .buffer(timeSpan: .seconds(5), count: 3, scheduler: MainScheduler.instance)
  .take(5)
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

// 실행 결과
// next([0, 1, 2])
// next([3, 4, 5])
// next([6, 7, 8])
// next([9, 10, 11])
// next([12, 13, 14])
// completed
```
=> 결과가 왜 둘이 다른지 유추해보라 (hint: timeSpan means `Maximum time length of a buffer`, count means `count: Maximum element count of a buffer`)
