# flatMapLatest

* Latest 키워드를 생각해 해당 Operator가 어떻게 행동할지 유추하라.
* 최근 요소로 새롭게 InnerObservable을 ReturnObservable로 리턴하고 기존의 ReturnObservable은 제거된다. 
* 해당 Operator는 InnerObservable을 **재사용하지 않는다.**

```swift
let disposeBag = DisposeBag()

let redCircle = "🔴"
let greenCircle = "🟢"
let blueCircle = "🔵"

let redRectangle = "🟥"
let greenRectangle = "🟩"
let blueRectangle = "🟦"

let publishSubject = PublishSubject<String>()
publishSubject.flatMapLatest { circle -> Observable<String> in
        switch circle {
        case redCircle:
            return Observable<Int>.interval(.milliseconds(200), scheduler: MainScheduler.instance)
              .map { _ in return redRectangle }
              .take(8)
        case greenCircle:
            return Observable<Int>.interval(.milliseconds(200), scheduler: MainScheduler.instance)
              .map { _ in return greenRectangle }
              .take(8)
        case blueCircle:
            return Observable<Int>.interval(.milliseconds(200), scheduler: MainScheduler.instance)
              .map { _ in return blueRectangle }
              .take(8)
        default:
            return Observable.just("")
        }
    }.subscribe { print($0) }
    .disposed(by: disposeBag)


DispatchQueue.main.asyncAfter(deadline: .now() + 0.5) {
    Observable<String>.just(redCircle)
        .subscribe(onNext: { publishSubject.onNext($0) })
        .disposed(by: disposeBag)
}

DispatchQueue.main.asyncAfter(deadline: .now() + 1.5) {
  Observable<String>.just(greenCircle)
      .subscribe(onNext: { publishSubject.onNext($0) })
      .disposed(by: disposeBag)
}

DispatchQueue.main.asyncAfter(deadline: .now() + 3.5) {
  Observable<String>.just(blueCircle)
      .subscribe(onNext: { publishSubject.onNext($0) })
      .disposed(by: disposeBag)
}

DispatchQueue.main.asyncAfter(deadline: .now() + 4.5) {
  Observable<String>.just(redCircle)
      .subscribe(onNext: { publishSubject.onNext($0) })
      .disposed(by: disposeBag)
}

// 실행 결과
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟩)
// next(🟩)
// next(🟩)
// next(🟩)
// next(🟩)
// next(🟩)
// next(🟩)
// next(🟩)
// next(🟦)
// next(🟦)
// next(🟦)
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟥)
```
