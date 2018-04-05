---
layout: post
title: RxSwift에 대해서 알아보기(Observable에 대해서) - 02
comments: true
tags: ios ReactiveX RxSwift RxCocoa Observable
---

안녕하세요 마기입니다.<br>
RxSwift 포스팅 두번째 시간 입니다.<br>
이번에는 Observable에 대해서 알아보도록 하겠습니다.

원래는 Observable, Subscribe, Dispose<br>
세가지에 대해서 알아보려고 하였는데<br>
Observable에 대한 내용이 아주 중요한 기본이고 내용도 많아서<br>
나눠서 포스팅 하려고 합니다.

하지만 너무 겁먹지 않으셔도 됩니다.<br>
차근차근 하다보면 뭐야.. 별거 아니네 라고 생각하실 겁니다. 😀 <br>
이제 같이 가보시죠!

<br>

#### 시작하기전에..

본론으로 들어가기전에 한가지 생각해 볼게 있습니다.<br>
이전 포스팅에서 알게된 reactive과 observer..<br>
즉 반응과 관찰에 대해서 한번 생각해보겠습니다.

어떠한 이벤트 혹은 상태 또는 값 등등..<br>
어떠한 스트림을 관찰하고 반응한다고 했었는데요.<br>
어떤식으로 관찰을 해야 할까요..

예를 들어서 정수형 프로퍼티를 관찰하겠다 라고 한다면...<br>
어떤 방법으로 관찰을 할수 있을까요?

스위프트에서라면 이렇게 만들어야 합니다.

{% gist magi82/9be2a494be8fe254b41824bd21140bba 1.swift %}

ReactiveX에서는 어떻게 관찰해야 할까요?<br>
이번시간에 알아보게 될 Observable이 바로 그것입니다.<br>
자 이제 시작해보도록 하겠습니다.

<br>

#### Observable이란 무엇인가?

언제나 그랬듯이 사전적 의미부터 한번 알아보겠습니다.

> Observable : 관찰 가능한, 관찰할수 있는

의미만 검색해봐도 잘 알수 있습니다. 아주 직관적이네요!<br>
관찰 가능한 녀석입니다.

이번에는 ReactiveX 공식 사이트에 가서 좀더 알아보겠습니다.<br>
친절하게도 한국어 번역도 되어 있습니다.

[http://reactivex.io/documentation/ko/observable.html](http://reactivex.io/documentation/ko/observable.html)

> ReactiveX에서 Observer는 Observable을 구독한다.<br>
> Obseravable이 배출하는 하나 또는 연속된 항목에<br>
> Observer는 반응한다. 

정리를 해보도록 하겠습니다.<br>
ReactiveX는 Observable이라는 객체를 이용해서<br>
값을 배출 할수 있고<br>
Observable에서 배출해주는 값을 관찰하고 반응합니다.<br>
좀더 자세히 알아봅시다.

<br>

#### next, error, complete

Observable은 행동 규칙이 있습니다.<br>
next, error, complete인데요.<br>
각각 행동에 대해서 알아보도록 하겠습니다.

위에서 말씀드렸듯이 Observable은 하나 또는 연속된<br>
항목을 배출합니다.<br>
바로 next인데요!<br>
next 스트림을 통해서 연속된 값들을 배출하고<br>
옵저버는 next 스트림을 관찰 및 구독해서<br>
원하는 행동을 하게 됩니다.

error 스트림은 키워드 그대로<br>
값을 배출하다가 에러가 생기면 error를 배출한다음<br>
해당 Observable은 스트림을 멈추게 됩니다.

complete는 Observable의 next가 더이상 배출하지 않으면..<br>
즉 모든 값을 다 배출하면 complete가 됩니다.

여기서 기억해야할 부분이 있습니다.<br>
바로 error와 complete인데요<br>
스트림 중간에 에러가 발생하게 되면<br>
complete가 되는게 아니라 그냥<br>
말그대로 멈춥니다.<br>
즉, erorr가 발생하면 complete은 발생하지 않습니다.

아래 스크린샷은 Observable을 설명하고 있는 마블 다이어그램입니다.

![1](https://magi82.github.io/images/2018-4-6-ios-rxswift-02/1.png)

[http://rxmarbles.com](http://rxmarbles.com) 라는 홈페이지에 가면<br>
각종 오퍼레이터들을 설명하는 다이어그램을 참고 할수 있습니다.

<br>

#### Observable 만들기

이론만 보고 있으니 이제 슬슬 지겨워지는군요.<br>
이제 한번 직접 Observable을 만들어 보도록 하겠습니다.

그전에 RxSwift 라이브러리를 설치하도록 하겠습니다.

[https://github.com/ReactiveX/RxSwift](https://github.com/ReactiveX/RxSwift)

해당 깃허브 주소를 가면 인스톨 정보를 알수 있는데요.<br>
저는 cocoapods를 사용해서 설치하도록 하겠습니다.

```
pod 'RxSwift', '~> 4.0'
pod 'RxCocoa', '~> 4.0’
```

Podfile에 위와 같이 등록하고 인스톨 하겠습니다.

설치가 끝났으면 이제 Observable을 만들어 보겠습니다.<br>
정수타입의 배열을 입력 받아서 각 엘리멘트를 체크 해보는<br>
Observable을 만들어 보겠습니다.

배열의 엘리멘트중에 0이 나오면 에러가 나는<br>
Observable을 만들어 봅시다.
사실 실제 개발할때 전혀 필요 없는 주제이지만,<br>
Observable에 대해서 이해하기에는 괜찮은 주제라고 생각합니다. 😀

rx에서 지원하는 오퍼레이터를 사용해도 되지만<br>
Observable의 특성을 공부하기 위해<br>
오퍼레이터 사용 없이 만들어 보겠습니다.

{% gist magi82/9be2a494be8fe254b41824bd21140bba 2.swift %}

생소한 코드지만 한줄 한줄 천천히 해석 해보도록 합시다.

먼저 items라는 정수 타입의 배열을 파라메터로 받고<br>
정수 제네릭 타입의 Observable 객체를 리턴하는<br>
checkArrayObservable 이라는 함수를 만들었습니다.

자 이제 리턴 해야할 Observable을 만들어 봅시다.

Observable은 create라는 메소드를 지원합니다.<br>
해당 메소드는 observer를 파라메터로 받고<br>
Disposable 객체를 리턴하는 클로저를 입력 받습니다.

{% gist magi82/9be2a494be8fe254b41824bd21140bba 3.swift %}

observer 파라메터는 몇가지 메소드를 지원하는데요.<br>
onNext, onError, onCompleted 입니다.

배열인 items를 순환하면서<br>
배열의 엘리멘트가 0이면 onError 메소드를 통해<br>
에러를 흘려보내주고, 0이 아니면 onNext 메소드를 이용해서<br>
각 엘리멘트를 next로 흘려줍니다.

로그를 편하게 확인하기 위해 sleep 함수를 사용하여<br>
1초마다 체크하게 만들었습니다.

{% gist magi82/9be2a494be8fe254b41824bd21140bba 4.swift %}

모든 순환이 끝나면 onCompleted 메소드를 호출하여<br>
completed 되었다는걸 알려줍니다.

{% gist magi82/9be2a494be8fe254b41824bd21140bba 5.swift %}

Disposable 객체에 대해서는 차후에 자세히 알아보도록 하고<br>
일단은 이렇게 해야한다고 넘어가도록 합시다.

자 이제 Observable을 만들었습니다.<br>
제대로 동작하는지 확인해보도록 합시다.

<br>

#### Observable 확인하기

Observable이 제대로 동작되는지 확인하기 위해서는<br>
구독을 해봐야 하겠죠?

{% gist magi82/9be2a494be8fe254b41824bd21140bba 6.swift %}

Observable은 subscribe라는 메소드를 지원합니다.

자 이제 너무 자주 나와서 외울거 같은 키워드들이 나오네요.<br>
next, error, completed를 구독 및 관찰하다가<br>
해당 값을 배출하게 되면 값을 print 합니다.

위 코드는 아래와 같은 결과를 보여줍니다.<br>
배열을 순환하는 도중에 0을 발견하고<br>
error 시그널을 흘려주면서 종료되었군요.

![2](https://magi82.github.io/images/2018-4-6-ios-rxswift-02/2.png)

그리고 배열을 [4, 3, 1, 5, 2] 라고 넣으면..

![3](https://magi82.github.io/images/2018-4-6-ios-rxswift-02/3.png)

이렇게 배열을 끝까지 순환하고 completed를 출력하였습니다.

위 코드중

{% gist magi82/9be2a494be8fe254b41824bd21140bba 7.swift %}

이 부분도 차후 자세히 알아보도록 하겠습니다.

아래 소스는 전체 소스 입니다.

{% gist magi82/9be2a494be8fe254b41824bd21140bba total.swift %}

<br>

#### 마치며..

자 이번시간에는 Observable에 대해 알아보고<br>
별로 쓸데가 많지 않은 주제이지만<br>
Observable의 이해를 돕기위해<br>
배열의 엘리멘트를 체크 하는 Observable을 만들어서<br>
확인을 해봤습니다.

이해가 가신분들도 계시고 <br>
아직도 햇갈리고 어려운 분들도 계실겁니다.<br>
하지만 차근차근 하다보면 당연하다는 듯이 사용할수 있을겁니다. 😆

다음 시간에는 차후 알아보기로 했던<br>
Subscribe, Dispose에 대해서 알아보도록 하겠습니다.

이상 마기였습니다.

![logo](https://magi82.github.io/images/magi.png)

<br>

---

#### 목차

- [RxSwift에 대해서 알아보기(ReactiveX에 대해서) - 01](https://magi82.github.io/ios-rxswift-01/)
- [RxSwift에 대해서 알아보기(Observable에 대해서) - 02](https://magi82.github.io/ios-rxswift-02/)