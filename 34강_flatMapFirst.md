# flatMapFirst 

처음으로 도착한 요소에 대해서만 InnerObservable이 ReturnObservable이 되어 InnerObservable의 클로저 로직대로 값을 옵저버로 방출한다.

```swift
let disposeBag = DisposeBag()

let redCircle = "🔴"
let greenCircle = "🟢"
let blueCircle = "🔵"

let redRectangle = "🟥"
let greenRectangle = "🟩"
let blueRectangle = "🟦"

Observable.from([redCircle, greenCircle, blueCircle])
    .flatMapFirst { circle -> Observable<String> in
        switch circle {
        case redCircle:
            return Observable.repeatElement(redRectangle)
                .take(5)
        case greenCircle:
            return Observable.repeatElement(greenRectangle)
                .take(5)
        case blueCircle:
            return Observable.repeatElement(blueRectangle)
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
// completed
```


* 하지만 첫번째 요소에 대해서 방출이 모두 끝나면, flatMapFirst는 **또 새롭게 첫번째 요소를 기다리는 특징**이 있다.
<br>첫번째 요소를 받으면 또 다시 첫번째 요소에 대해서만 InnerObservable이 ReturnObservable이 되고, InnerObservable의 클로저 로직대로 값을 옵저버로 방출한다.

```swift 
let disposeBag = DisposeBag()

let redCircle = "🔴"
let greenCircle = "🟢"
let blueCircle = "🔵"

let redRectangle = "🟥"
let greenRectangle = "🟩"
let blueRectangle = "🟦"


let publishSubject = PublishSubject<String>()
publishSubject
  .flatMapFirst { circle -> Observable<String> in
    switch circle {
      case redCircle:
        return Observable<Int>.interval(.milliseconds(200), scheduler: MainScheduler.instance)
          .map { _ in return redRectangle }
          .take(10)
      case greenCircle:
        return Observable<Int>.interval(.milliseconds(200), scheduler: MainScheduler.instance)
          .map { _ in return greenRectangle }
          .take(10)
      case blueCircle:
        return Observable<Int>.interval(.milliseconds(200), scheduler: MainScheduler.instance)
          .map { _ in return blueRectangle }
          .take(10)
      default:
        return Observable.just("")
    }
  }
  .subscribe({ print($0) })
  .disposed(by: disposeBag)

// onNext 로 element 전달
publishSubject.onNext(redCircle)

DispatchQueue.main.asyncAfter(deadline: .now() + 0.5, execute: {
  publishSubject.onNext(greenCircle)
})

DispatchQueue.main.asyncAfter(deadline: .now() + 2.5, execute: {
  publishSubject.onNext(blueCircle)
})

// 실행 결과 
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟥)
// next(🟦)
// next(🟦)
// next(🟦)
// next(🟦)
// next(🟦)
// next(🟦)
// next(🟦)
// next(🟦)
// next(🟦)
// next(🟦)
```

=> 실행 결과를 보면 알 수 있듯이 첫번째 요소로 redCircle 를 전달받아 해당 redCircle에 대한 innerObservable의 클로저 로직에 의한 요소값들을 방출한다. 해당 Observable은 요소값들이 0.2초마다 총 10번 방출되면 종료되므로 약 2초(정확히 2초가 아니라서 `약 2초`라고 판단함) 뒤에 끝이 난다. 따라서 약 2.5초 뒤에 전달되는 blueCircle이 새로운 첫번째 요소가 되어 또 해당 요소에 대한 innerObservable 이 ReturnObservable이 되어 해당 클로저 로직에 의해 요소값들을 방출한다. 

* **즉 flatMapFirst는 주기성을 띈다.**

<img width="1282" alt="스크린샷 2022-01-28 오후 8 41 28" src="https://user-images.githubusercontent.com/38216027/151541195-910979fb-4b7e-43d7-8504-045fc07c36bd.png">

ps. ***이 예제를 보기전에 내가 혼자 궁리해서 예제 코드를 알아서 만들어냈으면 어땠을까. 정말 신나고 기쁘겠지.*** 하지만 이렇게 하려면 **기다릴 줄 알아야 한다.**