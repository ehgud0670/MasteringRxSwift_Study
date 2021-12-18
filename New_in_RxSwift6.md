# What's new in RxSwift 6

1. Binder struct가 RxCocoa Library에서 RxSwift Library로 이동하였다. 
2. RxSwift6 는 Binder를 자동으로 합성한다. 
3. 새로운 Observable 타입이 추가되었다. 이름은 **Infalliable 이고 Error 이벤트를 방출하지 않는 Observable이다.** 
   이벤트의 종류가 명시적으로 제한되어 있다. 따라서 Error 가 방출되지 않는 다는 것이 컴파일 시점에 보장되어 있다.
4. Replay Relay가 새로 추가 되었다.
5. Driver 와 Signal 이 다중(multiple) binding 을 지원한다. 
6. DisposeBag에 Function Builder 를 받는 이니셜라이저가 추가되었다. 
7. withUnretained 와 decode 라는 새로운 연산자가 추가되었다.
  * withUnretained 를 사용하면 self 를 참조할때 클로러 캡쳐리스트(weak, unowned) 코드 작성할 수 있다. 작성해도 참조 cycle 문제가 나지 않는다. 
  * decode 연산자는 데이터를 방출하는 Observable 에서 사용할 수 있는 새로운 연산자다. 


