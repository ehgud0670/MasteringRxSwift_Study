# create

앞에서 배운 연산자들은 파라미터로 전달된 요소를 방출하는 옵져버블을 생성하는 연산자들이었다. 이렇게 생성된 옵져버블은 모든 요소를 방출하고 completed 이벤트를 전달한 다음 종료된다.
이게 옵져버블의 기본 동작이기 때문에 앞에 있는 연산자들로는 옵져버블의 동작을 바꿀 순 없다.

옵져버블이 동작하는 방식을 직접 구현하고 싶다면 create 연산자를 사용하자. 

```swift
let disposeBag = DisposeBag()

enum MyError: Error {
   case urlError
   case stringError
}

let observable = Observable<String>.create({ (observer) -> Disposable in
  guard let url = URL(string: "https://www.apple3249832u4.com") else {
    observer.onError(MyError.urlError)
    return Disposables.create()
  }
  
  guard let html = try? String(contentsOf: url, encoding: .utf8) else {
    observer.onError(MyError.stringError)
    return Disposables.create()
  }
  
  observer.onNext(html)
  observer.onCompleted()
  
  observer.onNext("After completed")
  
  return Disposables.create()
})

observable.subscribe({ print($0) })
  .disposed(by: disposeBag)

// 출력 결과
// error(stringError)
```

* create 으로 observable 을 생성할때 반드시 onCompleted 나 onError로 종료시켜주어야 한다.
 * observable 중에는 영원히 종료되지 않는 경우도 있는데 이런 경우가 아니라면 반드시 onCompleted나 onError로 종료 시켜주어야 한다.
* onCompleted 나 onError로 종료시킨 이후에는 element가 방출되지 않으므로 observable을 종료시키기 전에 element를 방출해야 한다.  