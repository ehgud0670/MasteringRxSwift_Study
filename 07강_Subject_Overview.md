# Subject Overview

* Subject 탄생 배경
  * Observable 은 Observer 처럼 다른 Observable을 구독하지 못한다. 
  * Observer 는 Observable 처럼 다른 Observer에거 이벤트를 전달할 수 없다. 
  * Observer와 Observable의 역할을 동시에 하는 녀석이 있으면 좋을텐데 그런 녀석을 만들어볼까 하는 차원에서 생긴게 바로 Subject 이다. 

## Subject가 할 수 있는 것

<img width="1280" alt="스크린샷 2021-12-18 오후 8 18 27" src="https://user-images.githubusercontent.com/38216027/146639134-d2e5f8dd-3d9e-48af-8983-872e0c1ad532.png">

* 다른 Observable로부터 event를 받아서(Observer의 역할) 구독자에게 전달(Observable의 역할)할 수 있다. 
 * 다시 말해 Subject는 **Observable인 동시에 Observer 이다.**

## Subject 종류

RxSwift는 4가지 종류의 subject 를 제공한다. 

1. Publish Subject
  * Subject로 전달되는 **새로운 Event**를 구독자로 전달한다.
2. Behavior Subject
  * 생성 시점에 시작 이벤트를 지정한다. 
  * Subject 로 전달되는 이벤트 중에서 가장 마지막에 전달된 최신 이벤트를 저장해두었다가 새로운 구독자에게 최신 이벤트를 전달한다.
3. Replay Subject
  * 하나 이상의 최신 이벤트를 버퍼에 저장한다. 
  * Observer가 구독을 시작하면 버퍼에 있는 모든 이벤트를 전달한다. 
4. Async Subject
  * Subject로 Completed Event가 전달되는 시점에 마지막으로 전달된 Next 이벤트를 구독자로 전달한다. 

# Relay

RxSwift 는 Subject를 Wrapping 하고 있는 두가지 Relay를 제공한다. 

1. Publish Relay: Publish Subject를 래핑한 것
2. Behavior Relay: Behavior Subject를 래핑한 것

Relay는 일반적인 Subject와 달리 Next 이벤트만 받고, **나머지 Completed 이벤트와 Error 이벤트는 받지 않는다.**
<br>**주로 종료없이 계속 전달되는 이벤트 시퀀스를 처리할때 이용한다.** 