# concatMap

* concatMap은 flatMap과 다르게 **interleaving이 없다.**
* interleaving이 없기 때문에 한번에 하나의 Observable만 event를 방출한다. 
* 현재의 innerObservable이 실행을 다하면, 다음 innerObservable이 실행된다. 
  * 따라서 원본 Observable이 방출하는 Next 이벤트의 순서와 InnerObservable이 방출하는 Next 이벤트의 순서가 동일한 것을 보장한다.

```swift
let disposeBag = DisposeBag()

let redCircle = "🔴"
let greenCircle = "🟢"
let blueCircle = "🔵"

let redRectangle = "🟥"
let greenRectangle = "🟩"
let blueRectangle = "🟦"

Observable.from([redCircle, greenCircle, blueCircle])
  .concatMap { circle -> Observable<String> in
    switch circle {
      case redCircle:
        return Observable<Int>.interval(.milliseconds(200), scheduler: MainScheduler.instance)
          .map { _ in return redRectangle }
          .take(5)
      case greenCircle:
        return Observable<Int>.interval(.milliseconds(200), scheduler: MainScheduler.instance)
          .map { _ in return greenRectangle }
          .take(5)
      case blueCircle:
        return Observable<Int>.interval(.milliseconds(200), scheduler: MainScheduler.instance)
          .map { _ in return blueRectangle }
          .take(5)
      default:
        return Observable.just("")
    }
  }
  .subscribe { print($0) }
  .disposed(by: disposeBag)

// 실행 결과 
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟩)
// next(🟩)
// next(🟩)
// next(🟩)
// next(🟩)
// next(🟦)
// next(🟦)
// next(🟦)
// next(🟦)
// next(🟦)
// completed
```