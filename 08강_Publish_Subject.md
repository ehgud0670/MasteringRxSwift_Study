# Publish Subject

=> 가장 기본적인 형태의 Subject

* Publish Subject는 Subject 로 전달되는 이벤트를 옵저버에게 전달하는 Subject 이다.
* 생성자가 전달될때 파라미터 전달하지 않고 비어있는 채로 전달되는 Subject 이다. 바꾸어 말하면 Subject가 생성되는 시점에는 아무런 이벤트가 저장되어 있지 않다. 따라서 생성 직후에 옵저버가 구독을 시작하면 아무 이벤트도 전달받지 않는다. 
* Subject는 Observable이면서 Observer이다. 다른 Source로부터 이벤트를 전달 받을 수 있고(Observer), 다른 Destination으로 이벤트를 전달할 수 있다(Observable). 

```swift
let disposeBag = DisposeBag()

enum MyError: Error {
   case error
}

let subject = PublishSubject<String>()
subject.onNext("Hello") // subject로 next 이벤트가 전달된다. 현재 subscribe를 한 observer가 없어 이 Next 이벤트는 처리되지 않고 그냥 사라진다.

let firstObserver = subject.subscribe { print(">> 1", $0) }
firstObserver.disposed(by: disposeBag) // 콘솔에 아무것도 출력되지 않음. PublishSubject는 구독 이후에 전달되는 새로운 이벤트만 구독자로 전달한다.

subject.onNext("RxSwift") // "RxSwift"를 담은 이벤트를 subject로 전달되고(observer), 해당 subject를 구독한 observer들에게 해당 이벤트를 전달한다(observable).
let secondObserver = subject.subscribe { print(">> 2", $0) }
secondObserver.disposed(by: disposeBag)
// (현재까지) secondeObserver는 2개의 이벤트가 모두 전달되고 난 뒤에 subject를 구독했기 때문에 아무로 이벤트도 전달받지 못해 출력되지 않는다.

subject.onNext("Subject")
//subject.onCompleted() // 모든 구독자에게 completed 이벤트가 전달된다.
subject.onError(MyError.error)

// subject가 완료된 이후에 새로운 구독자가 나타난다면 어떻게 될까?
let thirdObserver = subject.subscribe { print(">> 3", $0) }
thirdObserver.disposed(by: disposeBag)
subject.onNext("Wow")
// subject가 그 전에 종료된 것이므로 thirdObserver는 completed 이벤트만 전달받는다. completed 이후의 next 이벤트는 모든 옵져버가 전달받지 못한다.
// 그럼 subject가 completed 이벤트를 전달하지 않고 error 이벤트를 전달하면 어떻게 될까? subject가 비정상 종료한 것이므로 이 역시도 completed 처럼
// thirdObserver는 error 이벤트만 전달받는다.
```