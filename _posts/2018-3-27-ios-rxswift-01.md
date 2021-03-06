---
layout: post
title: RxSwift 알아보기(ReactiveX 에 대해서) - 01
comments: true
tags: ios ReactiveX RxSwift RxCocoa
---

안녕하세요 마기입니다.<br>
오랜만에 포스팅을 합니다.<br>
그동안 여러 일로 정신이 없었습니다. 😭<br>
다시 힘내서 자주 포스팅 할 예정입니다.<br>

이번 시간에는 핫한 시기를 넘어서<br>
반 필수적으로 알아야 하게끔 흘러가고 있는..<br>
ReactiveX!!<br>
그중에서도 iOS에서 사용하는 RxSwift에 대해서 알아보겠습니다.

<br>

#### ReactiveX

언제나 그랬듯이 먼저 사전적 의미를 찾아보도록 하겠습니다.

> reactive : 반응하는, 반응을 보이는

이거만으로는 감이 안오는군요<br>
이번에는 관련사이트([reactivex.io](http://reactivex.io))에 직접 가보도록 하겠습니다.<br>

> An API for asynchronous programming with observable streams

대문짝만하게 이렇게 설명하고 있네요..<br>
관찰 가능한 흐름과 함께 비동기 프로그래밍을 위한 api?<br>
이것도 애매모호 합니다.

조금만 더 살펴보죠

> ReactiveX is a combination of the best ideas from the Observer pattern, the Iterator pattern, and functional programming

자 필요한 키워드들이 다 나온것 같네요.<br>
이제 이 내용들을 조합해 보도록 합시다.<br>

옵저버패턴과 이터레이터패턴,<br>
그리고 함수형 프로그래밍을 이용한<br>
반응형 프로그램이다.<br>
그리고 그것은 비동기에 좋다.<br>

뭐 이런뜻입니다. 아직 어렵죠? ㅎㅎ<br>
하나씩 하나씩 진행하다보면 자연스레 느낌이 올겁니다.<br>
천천히 함께 진행해보시죠!<br>

일단 다른건 다 치우고 반응형이라는 부분만<br>
생각해보도록 합시다.

<br>

#### 반응형? reactive?

위에서 의미를 검색했었죠?<br>
반응을 하는, 반응하는<br>
반응을 한다는건 일단 수동적이라는 이야기 입니다.

저는 이렇게 이해하고 있습니다.<br>
> ~한다면, ~라면

만약 버튼이 눌린다면..<br>
텍스트필드에 값이 들어간다면..<br>
서버통신을 했다면..

어떠한 일이 발생한다면..<br>
그것에 반응해서 뭔가를 해주면 됩니다.

```
myButton.rx.tap
    .subscribe(onNext: { print(“tapped”) })
    .disposeBag(self.disposeBag)
```

이 코드는 버튼이 탭 되었을때를 관찰하고 있다가<br>
실제로 버튼이 눌러지면 반응해서<br>
**“tapped”** 라는 스트링을 출력해주는 코드입니다.

이 코드는 RxCocoa 라는 라이브러리에서 지원합니다.<br>
이 라이브러리는 iOS의 UI들을 Rx방식으로<br>
사용할수 있도록 해주는 라이브러리 입니다.

일단 이런식으로 구현된다는것만 확인하시고<br>
자세한 설명은 다음에 하도록 하겠습니다.

또 하나의 좋은 예가 있습니다.<br>
바로 스프레드시트 인데요.<br>
스프레드시트에서도 반응형 프로그래밍을 할수 있습니다.

![excel](https://magi82.github.io/images/2018-3-27-ios-rxswift-01/excel.gif)

A3, A4의 내용이 바뀐다면,<br>
A1이 반응해서 결과값이 바뀐다는것을 알수 있습니다.

<br>

#### 관찰가능한?

위 설명에서 관찰 가능한 비동기를 위한 프로그래밍 이라는 내용은<br>
반응형과 연계해서 생각하면 이해가 쉬워 집니다.

반응을 하려면 뭘 해야할까요…<br>
버튼이 눌리는지 관찰을 해야겠죠?

혹은 텍스트 필드에 값이 변경되는지.. 서버 통신을 했는지..<br>
관찰을 해야 합니다.<br>
관찰을 한다는건 옵저버패턴 그 자체 입니다.

ReactiveX는 Observable이라는<br>
(역시나 추후에 자세히 설명 들어갑니다.)<br>
제네릭 타입을 통해서 스트림(값)을 보내주고<br>
그것을 관찰해서 무언가를 합니다.

<br>

#### 비동기를 위한?

비동기를 위한 프로그래밍 역시 반응형, 관찰가능한 이라는<br>
키워드를 이해하면 쉽게 이해가 가능합니다.

일반적으로 비동기의 예를 들어보면<br>
서버와의 통신이 적절한데요.<br>
통신을 시도 한다고 바로 결과가 나오질 않죠?<br>
결과는 언제 나올지 모릅니다.<br>
혹은 결과가 오지 않거나 실패가 올수도 있죠.

관찰을 하고 있으면 언젠지는 모르지만 결과가 올테고<br>
그것에 대해 반응하면 됩니다.<br>
이것을 쉽게 해주는것이 바로 ReactiveX 입니다.

<br>

#### 마치며..

자 이제 다시 정리를 해보도록 할게요.<br>
ReactiveX는 비동기에 대한 스트림(결과)를 관찰하고,<br>
그것에 반응해서 무언가를 할수 있는 프로그래밍 패러다임입니다.

혹시 이제 이 정리한 문장이 이해가 가시나요?<br>
어려우신가요?<br>
네.. 제가 설명을 제대로 못해서 어려우실수도 있습니다.<br>
(설명하려니 너무 어렵네요 ㅠㅠ 저와 같이 개념을 잡으셨으면 좋겠습니다.)

하지만 괜찮습니다. 하나하나 하다보면 어느샌가 이해하고 계실 겁니다.

다음 시간에는 ReactiveX의 기본인<br>
Observable, Subscribe, Dispose에 대해서<br>
간략하게 알아보도록 하겠습니다.

이상 마기였습니다.

![logo](https://magi82.github.io/images/magi.png)

<br>

---

#### 목차

- [RxSwift 알아보기(ReactiveX에 대해서) - 01](https://magi82.github.io/ios-rxswift-01/) 
- [RxSwift 알아보기(Observable에 대해서) - 02](https://magi82.github.io/ios-rxswift-02/) 
- [RxSwift 알아보기(subscribe, dispose에 대해서) - 03](https://magi82.github.io/ios-rxswift-03/) 
