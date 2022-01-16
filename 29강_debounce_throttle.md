# debounce, throttle

* 두 연산자는 짧은 시간동안 반복적으로 방출되는 이벤트를 제어하는 연산자라는 점에서 공통점이 있다.
* 연산자가 파라미터로 받는 값도 둘이 동일하다. (`dueTime: RxTimeInterval`)
* 하지만 연산의 결과는 두 연산자가 완전히 다르기 때문에 잘 구분해서 사용해야 한다.

## debounce

```swift
public func debounce(_ dueTime: RxTimeInterval, scheduler: SchedulerType) -> Observable<Element> {
    return Debounce(source: self.asObservable(), dueTime: dueTime, scheduler: scheduler) 
}
```
=> dueTime 은 연산자가 next 이벤트를 방출할지 결정하는 조건으로 사용된다. observer가 next 이벤트를 방출한 다음, 지정된 시간동안  다른 next 이벤트를 방출하지 않는다면 해당 시점에 가장 마지막으로 방출된 next 이벤트를 구독자에게 전달한다.  
<br>=> 반대로 지정된 시간이내에 또 다른 next 이벤트를 방출했다면 timer 를 초기화한다.  


: 아하! 해당 시간동안 단수개 방출은 허용해도 복수개 방출한다면 타이머를 초기화하는 operator 구나! 

```swift
let disposeBag = DisposeBag()

let buttonTap = Observable<String>.create { observer in
   DispatchQueue.global().async {
      for i in 1...10 {
         observer.onNext("Tap \(i)")
         Thread.sleep(forTimeInterval: 0.3)
      }

      Thread.sleep(forTimeInterval: 1)

      for i in 11...20 {
         observer.onNext("Tap \(i)")
         Thread.sleep(forTimeInterval: 0.5)
      }
      
      observer.onCompleted()
   }
   
   return Disposables.create {
      
   }
}

buttonTap.debounce(.milliseconds(<#T##Int#>), scheduler: MainScheduler.instance)
   .subscribe { print($0) }
   .disposed(by: disposeBag)
```

위 예제에서 due 타임이 0.2초, 0,4초, 0.6초인 경우 각각 결과는 아래와 같다.

> 0.2초 일때

```
// 실행 결과
// next(Tap 1)
// next(Tap 2)
// next(Tap 3)
// next(Tap 4)
// next(Tap 5)
// next(Tap 6)
// next(Tap 7)
// next(Tap 8)
// next(Tap 9)
// next(Tap 10)
// next(Tap 11)
// next(Tap 12)
// next(Tap 13)
// next(Tap 14)
// next(Tap 15)
// next(Tap 16)
// next(Tap 17)
// next(Tap 18)
// next(Tap 19)
// next(Tap 20)
// completed
```

> 0.4초 일때

```
// 실행 결과
// next(Tap 10)
// next(Tap 11)
// next(Tap 12)
// next(Tap 13)
// next(Tap 14)
// next(Tap 15)
// next(Tap 16)
// next(Tap 17)
// next(Tap 18)
// next(Tap 19)
// next(Tap 20)
// completed
```

> 0.6초일때 

```
next(Tap 10)
next(Tap 20)
completed
```

=> 미래의 나 자신이여, 정답을 빨리 찾으려 하지말고 결과를 유추해보라.

## throttle

throttle은 지정된 dueTime 동안 하나의 이벤트만 구독자에게 전달한다. 

```swift
public func throttle(_ dueTime: RxTimeInterval, latest: Bool = true, scheduler: SchedulerType) -> Observable<Element> {
    Throttle(source: self.asObservable(), dueTime: dueTime, latest: latest, scheduler: scheduler)
}
```

```swift
let disposeBag = DisposeBag()

let buttonTap = Observable<String>.create { observer in
   DispatchQueue.global().async {
      for i in 1...10 {
         observer.onNext("Tap \(i)")
         Thread.sleep(forTimeInterval: 0.3)
      }
      
      Thread.sleep(forTimeInterval: 1)
      
      for i in 11...20 {
         observer.onNext("Tap \(i)")
         Thread.sleep(forTimeInterval: 0.5)
      }
      
      observer.onCompleted()
   }
   
   return Disposables.create {
      
   }
}

buttonTap.throttle(.milliseconds(1000), scheduler: MainScheduler.instance)
   .subscribe { print($0) }
   .disposed(by: disposeBag)

// 실행 결과
// next(Tap 1)
// next(Tap 4)
// next(Tap 7)
// next(Tap 10)
// next(Tap 11)
// next(Tap 12)
// next(Tap 14)
// next(Tap 16)
// next(Tap 18)
// next(Tap 20)
// completed
```
=> 실행결과 dueTime마다 한번씩 이벤트가 방출됨을 알 수 있다. 


```swift
buttonTap.throttle(.milliseconds(1000), latest: false, scheduler: MainScheduler.instance)
   .subscribe { print($0) }
   .disposed(by: disposeBag)


// 실행 결과 
// next(Tap 1)
// next(Tap 5)
// next(Tap 9)
// next(Tap 11)
// next(Tap 13)
// next(Tap 15)
// next(Tap 17)
// next(Tap 19)
// completed

```
=> 맨 처음 값은 기다리지 않고 바로 방출된다. 
=> latest의 차이점은 무엇일까? true이면 dueTime 이 지날때 그 이전에 원본 observable에서 가장 최근에 방출된 값을 방출하고, false이면 dueTime 이 지나고 그 다음에 원본 observable에서 방출된 값을 방출한다. 