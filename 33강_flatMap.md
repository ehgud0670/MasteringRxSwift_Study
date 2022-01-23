# flapMap

* flatMap은 innerObservable 이다.
최종적으로 event를 구독자에게 전달할 때는 모든 Observable를 하나로 합친 새로운 Observable을 사용한다. 이 과정을 `flattening(평탄화)` 라고 하고 새로운 Observable을 `result Observable`이라고 한다.. 


```swift


```
=> result Observable이 값을 방출할 때는 **지연없이 방출**하기 때문에 출력 결과가 redHeart 5개가 먼저 출력되는 게 아니라 
중간에 `"hello, baby"`, 나 blueHeart 가 출력됨을 알 수 있다.  
이러한 동작 방식을 `interleaving` 이라고 한다. 