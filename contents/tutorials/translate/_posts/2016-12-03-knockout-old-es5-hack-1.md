---
layout : tutorials
title : 오래된 ES5 꼼수를 버려라 1
category : tutorials
subcategory : tips
summary : 이 문서는 https://rainsoft.io/make-your-javascript-code-shide-knockout-old-es5-hack/ 를 번역한 내용입니다.
permalink : /tutorials/translate/knockout-old-es5-hack-1
tags : javascript es5 es6
author : apple77y
---

ECMA Script 2015 이전의 자바스크립트 언어는 다루기 까다로웠다.
간단한 ES5 스크립트 정도는 괜찮았다.
하지만 어플리케이션의 복잡도가 커지게 되면서, 언어 구조의 부족함으로 인해 코드가 불편해지기 시작했다.

개발자는 어플리케이션 요구사항에 맞게 개발을 해야 하기 때문에, ES5 문법으로 개발하게 될 때 발생하는 골칫거리를 피해갈 수 없다.
그에 대한 해결책으로 합리적이진 않지만, 꼼수를 사용하게 된다.

꼼수와 차선책으로 개발된 여러 줄의 코드는 문제를 발생하게 된다.
쉽게 읽을 수 없게 되고, 결국에는 유지보수를 어렵게 만든다.

그리고 일반적인 말로

> 자바스크립트 꼼수를 적용하는 것은 얇은 카드로 집을 짓는 것과 같다.


![카드집](https://rainsoft.io/content/images/2016/08/House-of-cards-014-1.jpg)


차선책으로는 Underscore 혹은 lodash와 같은 유틸리티 라이브러리를 사용하는 것이다.
라이브러리는 어려운 것을 쉽게 해결할 수 있는 다양한 함수를 제공한다.
훨씬 나은 방법이지만, 이것 역시 여전히 차선책이다.
모듈 의존성 추가되고, 직접 해결하는 방법을 찾지 않게 될지도 모른다.

ECMA Script 2015, 2016, 그리고 곧 나올 2017은 이런 현상을 개선하기 위해 효과적인 것들을 만들고 있다.
이미 꼼수로 개발된 대부분을 안정적인 것들로 쉽게 대체될 수 있다.
또한, 당신의 프로젝트 프로세스를 개선할 수 있다.

어떻게 하면 자바스크립트 코드를 바위처럼 단단히, 빛나게 개발할 수 있을까?
당신의 코드에서 꼼수를 제거하고, 해결책을 적용하고 있는 이 가이드를 따라라.

## 1. 배열 내 요소가 존재하는지 증명하는 법

모든 어플리케이션이 배열을 다루기 때문에, 집합 내에 특정 요소가 존재하는지 찾아내는 작업은 평범한 일이다.

- ES5에서는 보통 Array.prototype.indexOf(element, [fromIndex]) 메소드를 사용했고, 반환되는 값과 -1을 비교해서 요소의 존재를 증명했다.

이 .indexOf() 메소드를 사용하는 증명법에는 부작용이 있는데, 배열 내 요소가 존재하면 요소의 인덱스를 반환하고, 존재하지 않다면 그냥 -1을 반환한다.

예제를 살펴보자.

``` javascript
var months = ['January', 'March', 'July'];
months.indexOf('March') !== -1;  // => true
months.indexOf('August') !== -1; // => false
```

months라는 배열에서 한 요소의 존재를 증명하기 위해서 months.indexOf()를 사용했고, 반환되는 값과 -1을 비교한다.
존재하는 요소인 months.indexOf('March')를 사용하게 되면 인덱스 1을 반환하게 되고, 1 !== -1 비교 값은 참이 된다.
'March'는 존재한다는 의미다.
months.indexOf('August')은 -1을 반환하는데, 이 의미는 요소가 없다는 것: -1 !== -1은 거짓이다.
'August' 요소는 존재하지 않는다는 의미다.

더욱 나쁜 방법은 비교를 하는 방법이 다양하다는 점이다.

- months.indexOf('March') != -1
- months.indexOf('Match') < 0
- ~months.indexOf('March')
- and so on...

이 방법으로 요소를 탐색하는 것은 간편하지 않을 뿐더러 꼼수를 사용하는 것 같은 느낌이다.

- 이 문제를 해결하기 위해, ECMAScript 2015는 새로운 메소드인 Array.prototype.includes(element, [fromIndex])를 소개한다. 이 메소드는 배열 내 요소가 존재하는지 참 / 거짓 값으로 반환해준다.


![includes](https://rainsoft.io/content/images/2016/08/1-4.jpg)


예제를 향상 시켜보자.

``` javascript
var months = ['January', 'March', 'July'];
months.includes('March');  // => true
months.includes('August'); // => false
```

존재하는 요소의 경우 months.includes('March') 결과 값으로 참, 존재 하지 않는 요소의 경우 months.includes('August') 결과 값으로 거짓을 반환한다.

간단하고 편리하다!
당연히 .indexOf(element) !== -1은 .includes(element)로 바뀌어야 한다.

## 2. 함수 인자 값에 접근하는 법

arguments는 함수 호출 부분에 전달되는 인자들을 포함하는 하나의 객체다.

내 생각에, arguments 객체는 하드코드 되어있는 제한적인 구조물이다.

- arguments는 유사 배열이다. 그래서 배열 메소드인 .forEach(), .reduce(), etc 등을 사용하는 것은 불가능하다.
- arguments 이름은 바꿀 수 없다. 예를 들어 args로 바꾸는 것도 안 된다.
- 모든 함수의 스코프는 자신의 arguments를 정의한다. 내부 함수에서 외부 함수의 arguments에 접근하기 위해서는 var outerArgs = arguments; 와 같은 임시 변수가 필요하다.
- 함수의 기본 형태인 function myFun() {}와 arguments의 실제 사용에는 모순이 있다. 함수 형태에서는 파라미터를 넘겨주지 않지만, 함수 내 코드에서는 갑자기 arguments 객체를 사용하게 되면 코드상에서 이질감이 생긴다.

예제를 살펴보자.

``` javascript
function sum() {
  return Array.prototype.reduce.call(arguments, function(sum, el) {
    return sum + el;
  });
}
sum(10, 5, 2); // => 17
```

sum() 함수는 arguments의 합계를 반환한다.
위에서 말한 것처럼, arguments는 유사 배열이다.
그래서 우회적인 방법으로 .reduce()를 사용해야 한다.
function sum() {} 이 함수 선언의 형태는 아무런 인자를 받지 않고 있다.
하지만 함수 내의 arguments는 넘어오지 않는 것처럼 보이는 함수의 인자에 접근하고 있다.
 이 부분은 혼란을 일으킨다.
그래서 함수의 선언 부분에서는 실행 부분을 보지 않고서라도, 어떤 인자가 넘어오는지 명확히 인지할 수 있어야 한다.

- 이 문제를 해결하기 위해서 es2015에서 제공하는 function funName(...restParam) 라는 나머지 매개변수를 적용한다.


![rest parameter](https://rainsoft.io/content/images/2016/08/2-2.jpg)


함수 선언식에 사용되는 3개의 마침표가 붙은 나머지 매개변수는 파라미터 인자들을 의미한다.
나머지 매개변수는 함수 실행문에 넘겨지는 인자들을 하나의 변수에 담는다.
그리고 다른 변수들과 마찬가지로 의미에 맞게 이름을 지을 수 있다.

위 예제에서 사용했던 arguments 유사배열을 제거하고, 나머지 매개변수라는 개념을 적용해보자.

``` javascript
function sum(...numbers) {
  return numbers.reduce(function(sum, el) {
    return sum + el;
  });
}
sum(10, 5, 2); // => 17
```

numbers라는 나머지 매개변수에는 arguments가 포함 되어있다.
그래서 function sum(10, 5, 2)로 실행하니 나머지 매개변수에 [10, 5, 2]가 전달 되었다.
이로써 arguments 유사배열을 사용했을 때의 문제점이 해결되었다.

- numbers는 배열이기 때문에 .reduce() 배열 메소드를 바로 사용할 수 있다.
- 변수의 의미에 따라 이름을 지을 수 있다. 이 경우 여러 개의 숫자가 담긴 배열이므로 numbers라는 이름으로 되어있다.
- 함수 선언식 function sum(...numbers)는 인자들이 numbers 라는 파라미터 변수에 담긴다는 것을 명백하게 나타내준다.

리팩토링하게 될 양이 많을지도 모르지만, 코드 가독성을 위해 arguments 유사배열은 나머지 매개변수로 대체되어야 한다.

## 3. this 바인드

this 혹은 함수 실행 문맥에 대한 개념은 자바스크립트에서 어려운 부분이다.
이 개념에 대해 이해를 하려면 [이 글](https://rainsoft.io/gentle-explanation-of-this-in-javascript/)을 읽는 것을 추천한다.

보통 자바스크립트 함수는 실행 시점에 따른 this를 가진다.
전역에 정의된 함수 myFunction()의 경우, this는 window 객체를 나타낸다.
혹은 strict mode에서 실행이 될 경우 undefined를 반환한다.

보통 함수라면 별도의 this를 정의하지 않아도 인접한 문맥으로 this를 참조하게 된다.
자바스크립트에서는 실행 문맥에 대해 수정하기 위해 특별한 메소드와 꼼수를 제공한다.

- call Function.prototype.call(context, arg1, ...) 혹은 Function.prototype.apply(context, [arg1, ...]) 메소드로 문맥을 간접적으로 호출하는 방법
- Function.prototype.bind(context, arg1, ...) 메소드로 문맥을 묶는 방법
- 인접 문맥을 var context = this와 같이 별도의 변수에 저장하고, 내부 함수에서 그 변수에 접근하는 방법

내부 함수에서도 같은 문맥을 유지하기 위해서 위와 같은 메소드를 사용하는 건 까다롭고 장황해 보인다.
나는 모든 함수마다 꼬리에 .bind(this)를 달거나 var self = this처럼 별도의 변수에 this를 저장하는 스파게티식 문맥 관리를 좋아하지 않는다.

보편적인 상황을 살펴보자.

``` javascript
function Numbers(array, operation) {
  this.array = array;
  this.operation = operation;
  this.calculate = function() {
    return this.array.reduce(function(accumulator, element) {
      if (this.operation === 'sum') {
        return accumulator + element;
      } else if (this.operation === 'multiply') {
        return accumulator * element;
      }
      throw new Error('Unknown operator');
    }.bind(this)); // <--- bind the function context
  }
}
var myNumbers = new Numbers([1, 5, 10], 'sum');
myNumbers.calculate(); // => 16
```

Numbers라는 생성자는 배열에 담긴 요소들의 합계 혹은 곱셈을 계산한다.
이 생성자에는 .calculate()라는 메소드가 있는데, 여기에서는 필요한 operation 변수로 받은 연산자를 적용하기 위해 this.array.reduce()라는 것을 사용하고 있다.
이 콜백 함수에서는 this.operation에 접근하기 위해 .bind(this)를 이용해 인접 문맥에 접근하고 있다.

화살표 함수에서는 this 문맥을 효율적으로 바인드 시켜준다.
이 함수는 인접 문맥을 사용하고, this를 새로 정의하지 않는다.


![binding this](https://rainsoft.io/content/images/2016/08/3-1.jpg)


화살표 함수를 적용시켜서 코드를 더 향상시켜보자.

``` javascript
function Numbers(array, operation) {
  this.array = array;
  this.operation = operation;
  this.calculate = function() {
    return this.array.reduce((accumulator, element) => {
      if (this.operation === 'sum') {
        return accumulator + element;
      } else if (this.operation === 'multiply') {
        return accumulator * element;
      }
      throw new Error('Unknown operator');
    });
  }
}
var myNumbers = new Numbers([1, 5, 10], 'sum');
myNumbers.calculate(); // => 16
```

this.array.reduce() 콜백 함수로 화살표 함수가 적용된 것을 볼 수 있다.
화살표 함수는 this.calculate() 메소드와 같은 문맥(Numbers 생성자의 인스턴스)을 가지기 때문에, this.operation에 접근할 수 있다.

보다시피, 화살표 함수는 간결한 문법과 문맥의 직관성을 제공한다.