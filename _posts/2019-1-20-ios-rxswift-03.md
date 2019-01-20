---
layout: post
title: RxSwift 알아보기(subscribe, dispose에 대해서) - 03
comments: true
tags: ios ReactiveX RxSwift RxCocoa Observable subscribe dispose disposeBag
---

안녕하세요. 당근마켓에서 iOS를 개발중인 마기입니다.

자주 포스팅 하겠다던 각오는<br> 
이직하게 되면서 새로운 환경에서 적응 한다고.. <br>
정신이 없어서 이제서야 올리게 되었네요 😭 <br>
좋은 동료들을 만나 빠르게 적응 되고 있어서 <br>
오랜만에 포스팅합니다.
새해부터 다시 각오를 다져 봅니다!!

이번 세번째 시간에는 <br>
두개의 주제에 대해서 알아보도록 하겠습니다. <br>
subscribe와 dispose입니다. <br>
먼저 subscribe에 대해 알아봅시다. 

<br>

#### subscribe

언제나 그랬듯이 사전적으로 어떤 뜻인지 먼저 알아보겠습니다. 

> subscribe : 구독하다

유튜브 보면 bj들이 항상 마지막에 하는 말이 있습니다.<br>
구독해주세요~! 구독하면 어떻게 되죠?<br>
그 bj가 영상을 올리면 제가 볼수있게 알림이 옵니다. 

네 바로 그 구독입니다! <br>
첫번째 observable을 설명하는 글에서 이미 잠깐 한줄 설명 한적이 있는데요. <br>
observable이 뭐라고 했었죠? stream을 관찰한다고 했죠? <br>
subscribe는 observable의 stream을 관찰하고 구독 해서 받는 역할을 합니다. 

아래 코드를 봅시다. 

{% gist magi82/a79bb41d3ce2cb6f668e0fa8880d6015 1.swift %}

observable 코드의 subscribe 부분을 보면 <br>
onNext, onError, onComplete 를 구독 받아서 <br>
print 해주고 있습니다.

자, 만약 여기까지 코딩을 해보셨다면..<br>
아마 xcode에서 warning이 뜰겁니다.<br>
왜냐하면 subscribe 메소드는 반환 객체이 있기 때문이죠.<br>
바로 disposable 이라는 객체인데요. <br>
뭐 하는 녀석인지 이제부터 알아봅시다! 

<br>

#### disposable

disposable 에 대해서 의미를 찾아 보았습니다.

> disposable : 처분할 수 있는, 사용후 버릴 수 있는

무엇을 처분 한다는거지..?<br>
살짝 어렵게 느껴질수도 있으니 바로 예시를 들어가며<br>
이해를 해보도록 합시다. :)

iOS 프로젝트 개발을 한다면, 보통 뷰 컨트롤러 단위로<br>
개발을 하게 됩니다.

CustomViewController 이라는 뷰컨트롤러를 개발한다고 가정 해보죠.<br>
기능 스펙이 하나 추가 되었습니다.<br>
내용은 뷰컨트롤러가 화면에 보여지면 10초동안 1초마다 카운트 다운을<br>
화면에 보여주는 기능입니다.

자 이제 RxSwift에 입문 하였으니..<br>
멋지게 RxSwift로 기능을 추가 해봅시다.

{% gist magi82/a79bb41d3ce2cb6f668e0fa8880d6015 2.swift %}

> interval, take 라는 새로운 메소드가 추가 되었습니다.<br>
> 간단하게 interval은 n초마다 정수 타입의 스트림이 emit 됩니다.<br>
> take는 parameter 만큼의 스트림을 허용 합니다.<br>
> 차후에 제대로 다룰 예정이니<br>
> 이정도로 이해하고 넘어가면 될거 같습니다.

![1](https://magi82.github.io/images/2019-1-20-ios-rxswift-03/1.png)

자! 콘솔창의 로그가 이렇게 출력 되었습니다.<br>
영상이 아니다보니 시간은 확인이 안되지만 1초마다 정수가 찍히다가<br>
10번이 찍힌다음 completed와 disposed가 차례대로 출력 되었습니다.

자기 할일을 다 하고 나서 completed 가 되면서 disposed<br>
즉, 할일이 끝났으니 버려지는 겁니다.

그런데 예외 상황이 발생 할수도 있습니다.<br>
카운팅이 끝나기 전에 뷰 컨트롤러를 해제해 버린다면<br>
어떻게 될까요?

{% gist magi82/a79bb41d3ce2cb6f668e0fa8880d6015 3.swift %}

결과를 확인하기 위해 DispatchQueue를 이용해서 3초 뒤에 뷰 컨트롤러를<br>
삭제 하였습니다.

![2](https://magi82.github.io/images/2019-1-20-ios-rxswift-03/2.png)

3초뒤에 뷰 컨트롤러를 해제 되면서 deinit이 되었다는 로그가 출력 되었음에도<br>
끝까지 카운트가 진행 됩니다.<br>
이런 결과를 원하는 개발자는 없을 겁니다. 어떻게 해야 할까요?

<br>

#### dispose, disposeBag

앞서 이야기 했듯이 observable을 subscribe를 하면<br>
Disposable 이라는 타입이 반환 됩니다.<br>
이 타입에 대해서 알아 보겠습니다.

![3](https://magi82.github.io/images/2019-1-20-ios-rxswift-03/3.png)

해당 프로토콜 타입은 dispose 라는 메소드를 가지고 있습니다.<br>
이 타입으로 dispose를 시킬수 있을거 같군요 :)<br>
자 테스트 해봅시다!

{% gist magi82/a79bb41d3ce2cb6f668e0fa8880d6015 4.swift %}

disposable이라는 프로퍼티에 subscribe 반환 객체를 가지고 있다가<br>
3초뒤에 dispose 메소드를 실행 했습니다.

![4](https://magi82.github.io/images/2019-1-20-ios-rxswift-03/4.png)

오오!! 3초뒤에 제대로 dispose 되는군요.<br>
반환된 disposable 객체를 가지고 있다 뷰 컨트롤러가 deinit 될때<br>
dispose를 실행 하면 될거 같습니다.

#### disposeBag

자 그런데.. 만약 구독 받는 observable들이 여러개라면<br>
좀 귀찮을수도 있을거 같네요.<br>
disposable 컬렉션을 만들고 다 집어 넣은 다음<br>
뷰 컨트롤러가 deinit 될때 dispose를 해주면 되지만<br>
귀찮습니다.

이때 사용하게 되는것이 바로 DisposeBag 입니다.

![5](https://magi82.github.io/images/2019-1-20-ios-rxswift-03/5.png)

Disposable에는 disposed(by:) 라는 메소드가 존재 합니다.<br>
파라메터에 DisposeBag 객체가 들어가고 그 bag에 자신을 insert 하는군요.

모든 disposable 객체에 disposed 를 해주면 해당 파라메터인<br>
disposeBag에 등록이 되고 disposeBag 객체가 해제 되면서<br>
등록된 모든 disposable이 다같이 dispose 되어 버립니다.<br>
확인 해보도록 할게요.

{% gist magi82/a79bb41d3ce2cb6f668e0fa8880d6015 5.swift %}

뷰 컨트롤러 로딩후 3초뒤에 뷰 컨트롤러를 해제 해보겠습니다.

![6](https://magi82.github.io/images/2019-1-20-ios-rxswift-03/6.png)

오~! 3초뒤에 뷰 컨트롤러가 해제 되면서 disposeBag 프로퍼티도 같이 해제 되고<br>
그에 따라 등록된 disposable도 dispose 되었군요.<br>
만족스러운 결과 입니다.

여기서 한가지 내용을 추가 하겠습니다.<br>
위에 코드를 보면 disposeBag 프로퍼티를 선언할때<br>
let이 아닌 var로 선언을 하였습니다. 왜 그럴까요?

disposeBag이 해제 되면 모든 disposable이 dispose 되는 원리를<br>
개발도중 사용할수 있습니다.

subscribe 중이던 disposable을 초기화 하고 싶으면<br>
disposeBag 프로퍼티에 새로운 DisposeBag 객체를 넣어주면 끝인거죠.

바로 이렇게요.

{% gist magi82/a79bb41d3ce2cb6f668e0fa8880d6015 6.swift %}

<br>

#### 마치며..

이번 시간에는 subscribe와 dispose 에 대해 알아 보았습니다.<br>
두가지 주제는 사실 간단하다면 아주 간단한 개념이라<br>
내용이 길지 않을거라 생각 했지만..<br>
이해하기 쉽게 자세히 작성하려다보니 생각보다 길어졌습니다.

다음 시간 부터는 RxSwift의 operator를 하나하나 알아보도록 하겠습니다.

이상 마기였습니다. 

![logo]( [https://magi82.github.io/images/magi.png](https://magi82.github.io/images/magi.png) ) 

<br> 

— 

#### 목차

- [RxSwift 알아보기(ReactiveX에 대해서) - 01](https://magi82.github.io/ios-rxswift-01/) 
- [RxSwift 알아보기(Observable에 대해서) - 02](https://magi82.github.io/ios-rxswift-02/) 
- [RxSwift 알아보기(subscribe, dispose에 대해서) - 03](https://magi82.github.io/ios-rxswift-03/) 
