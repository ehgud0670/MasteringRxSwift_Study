# merge 

* 이 연산자는 여러 Observable이 방출하는 항목들을 하나의 옵져버블에서 방출하도록 병합한다. 
* 이 연산자는 concat 연산자와 자주 헷갈리게 되는데, concat 연산자와 동작방식이 다르다.
* 이 연산자는 2개 이상의 observable을 병합하고, 모든 옵져버블에서 방출하는 요소들을 순서대로 방출하는 옵져버블을 리턴한다.
  * concat은 단순히 뒤에 연결하는 것이지만 merge는 Observable들을 하나의 Observable로 합치는 것이다. 


```swift
let bag = DisposeBag()

enum MyError: Error {
   case error
}

let oddNumbers = BehaviorSubject(value: 1)
let evenNumbers = BehaviorSubject(value: 2)
let negativeNumbers = BehaviorSubject(value: -1)

let source = Observable.of(oddNumbers, evenNumbers)
source
  .merge()
  .subscribe({ print($0) })
  .disposed(by: bag)

evenNumbers.onNext(4)
oddNumbers.onNext(3)

oddNumbers.onNext(5)
evenNumbers.onNext(6)

// 실행 결과
// next(1)
// next(2)
// next(4)
// next(3)
// next(5)
// next(6)
```

=> subject가 방출한 항목이 전달된다.

* 그럼 metge가 종료되는 시점은 언제인가? 합친 두개의 subject로부터 completed 이벤트를 모두 받았을때이다. 

```swift
let oddNumbers = BehaviorSubject(value: 1)
let evenNumbers = BehaviorSubject(value: 2)
let negativeNumbers = BehaviorSubject(value: -1)

let source = Observable.of(oddNumbers, evenNumbers)
source
  .merge()
  .subscribe({ print($0) })
  .disposed(by: bag)

evenNumbers.onCompleted()
oddNumbers.onNext(7)
oddNumbers.onCompleted()

// 실행 결과 
// next(1)
// next(2)
// next(7)
// completed
```

* 반면에 어느 한 subject라도 에러 이벤트를 받으면 merge 연산자는 바로 종료된다. 

```swift
let oddNumbers = BehaviorSubject(value: 1)
let evenNumbers = BehaviorSubject(value: 2)
let negativeNumbers = BehaviorSubject(value: -1)

let source = Observable.of(oddNumbers, evenNumbers)
source
  .merge()
  .subscribe({ print($0) })
  .disposed(by: bag)

evenNumbers.onError(MyError.error)
oddNumbers.onNext(7)
oddNumbers.onCompleted()

// 실행 결과 
// next(1)
// next(2)
// error(error)
```

* maxConcurrent param을 사용하면 merge 가능한 Observable의 개수를 제한할 수 있다.

```swift
.merge(maxConcurrent: 2)
```
=> 병합 가능한 옵져버블의 수를 2개로 제한.

```swift
let oddNumbers = BehaviorSubject(value: 1)
let evenNumbers = BehaviorSubject(value: 2)
let negativeNumbers = BehaviorSubject(value: -1)

let source = Observable.of(oddNumbers, evenNumbers, negativeNumbers)
source
  .merge(maxConcurrent: 2)
  .subscribe({ print($0) })
  .disposed(by: bag)

negativeNumbers.onNext(-10)
oddNumbers.onNext(7)
evenNumbers.onNext(4)

// 실행 결과 
// next(1)
// next(2)
// next(7)
// next(4)
```

=> merge로 병합가능한 옵져버블 수가 max 로 2개이므로 마지막 param으로 들어온 negativeNumbers subject 는 병합되지 않았다. 
<br>따라서 전달한 -1 과 -10 요소는 무시되어 방출되지 않는다.
<br>merge는 병합되지 않은 옵져버블을 queue에 저장해 두었다가 병합 대상이 completed 이벤트를 전달하면 순서대로 대기중인 observable을 병합 대상에 추가한다.

> oddNumbers가 completed 된 이후에는 negativeNumbers로 들어온 요소가 방출된다.

```swift
let oddNumbers = BehaviorSubject(value: 1)
let evenNumbers = BehaviorSubject(value: 2)
let negativeNumbers = BehaviorSubject(value: -1)

let source = Observable.of(oddNumbers, evenNumbers, negativeNumbers)
source
  .merge(maxConcurrent: 2)
  .subscribe({ print($0) })
  .disposed(by: bag)

negativeNumbers.onNext(-10)
oddNumbers.onCompleted()
negativeNumbers.onNext(-5)

// 실행 결과 
// next(1)
// next(2)
// next(-10)
// next(-5)
```