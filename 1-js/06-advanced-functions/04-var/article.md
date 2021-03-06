
# 오래된 'var'

[변수](info:variables)를 다룬 첫 번째 장에서 변수 선언 방법 세 가지를 배운 바 있습니다.

1. `let`
2. `const`
3. `var`

`let`과 `const`는 렉시컬 환경 측면에서 정확히 같은 방식으로 동작합니다.

하지만 `var`는 초기 자바스크립트 구현 방식 때문에 `let`과 `const`로 선언한 변수와는 전혀 다른 방식으로 동작합니다. 근래엔 `var`를 쓰지 않아서 이를 만나는 건 흔치 않은 일이지만, `var`는 오래된 스크립트에서 당신을 기다리고 있는 괴물 같은 존재입니다.

구식 스크립트를 다룰 계획이 없는 개발자라면 이 챕터를 건너뛰거나 학습을 미루려고 할 겁니다. 하지만 그랬다간 이 괴물에게 물릴 수 있습니다.

처음 `var`를 접했을 때는 `let`과 비슷하게 변수를 선언하는 것처럼 보일 겁니다.

```js run
function sayHi() {
  var phrase = "Hello"; // "let" 대신 "var"를 사용해 지역 변수를 선언 

  alert(phrase); // Hello
}

sayHi();

alert(phrase); // Error, phrase is not defined
```

하지만 `var`와 `let`엔 차이가 있습니다.

## "var"는 블록 스코프가 없습니다.

`var`로 선언한 변수의 스코프는 함수 스코프이거나 전역 스코프입니다. 블록 기준으로 스코프가 생기지 않기 때문에 블록 밖에서 접근 가능합니다.

예시:

```js run
if (true) {
  var test = true; // "let" 대신 "var"를 사용했습니다.
}

*!*
alert(test); // true(if 문이 끝났어도 변수에 여전히 접근할 수 있음)
*/!*
```

`var`는 코드 블록을 무시하기 때문에 `test`는 전역 변수가 됩니다. 전역 스코프에서 이 변수에 접근할 수 있죠.

두 번째 행에서 `var test`가 아닌 `let test`를 사용했다면, 변수 `test`는 `if`문 안에서만 접근할 수 있습니다.

```js run
if (true) {
  let test = true; // "let"으로 변수를 선언함
}

*!*
alert(test); // Error: test is not defined
*/!*
```

반복문에서도 유사한 일이 일어납니다. `var`는 블록이나 루프 수준의 스코프를 형성하지 않기 때문이죠.

```js
for (var i = 0; i < 10; i++) {
  // ...
}

*!*
alert(i); // 10, 반복문이 종료되었지만 "i"는 전역 변수이므로 여전히 접근 가능합니다.
*/!*
```

코드 블록이 함수 안에 있다면, `var`는 함수 레벨 변수가 됩니다.

```js run
function sayHi() {
  if (true) {
    var phrase = "Hello";
  }

  alert(phrase); // 제대로 출력됩니다.
}

sayHi();
alert(phrase); // Error: phrase is not defined
```

위에서 살펴본 바와 같이, `var`는 `if`, `for` 등의 코드 블록을 관통합니다. 아주 오래전의 자바스크립트에선 블록 수준 렉시컬 환경이 만들어 지지 않았기 때문입니다. `var`는 구식 자바스크립트의 잔재이죠.

## "var" 선언은 함수 시작 시 처리됩니다.

`var` 선언은 함수가 시작될 때 처리됩니다. 전역에서 선언한 변수라면 스크립트가 시작될 때 처리되죠. 

함수 본문에 있는 `var`로 선언한 변수는 선언 위치와 상관없이 함수 본문이 시작되는 지점에서 정의됩니다(단, 변수가 중첩 함수 내에서 정의되지 않아야 이 규칙이 적용됩니다).

따라서 아래 두 예제는 동일하게 동작합니다.

```js run
function sayHi() {
  phrase = "Hello";

  alert(phrase);

*!*
  var phrase;
*/!*
}
sayHi();
```

`var phrase`가 위로 이동한 것처럼 말이죠.

```js run
function sayHi() {
*!*
  var phrase;
*/!*

  phrase = "Hello";

  alert(phrase);
}
sayHi();
```

코드 블록은 무시되기 때문에, 아래 코드 역시 동일하게 동작합니다.

```js run
function sayHi() {
  phrase = "Hello"; // (*)

  *!*
  if (false) {
    var phrase;
  }
  */!*

  alert(phrase);
}
sayHi();
```

이렇게 변수가 끌어올려 지는 현상을 "호이스팅(hoisting)"이라고 부릅니다. `var`로 선언한 모든 변수는 함수의 최상위로 "끌어 올려지기(hoisted)" 때문입니다(옮긴이 -- *hoist*는 끌어올리다 라는 뜻이 있습니다).

바로 위 예제에서 `if (false)` 블록 안 코드는 절대 실행되지 않지만, 이는 호이스팅에 전혀 영향을 주지 않습니다. `if` 내부의 `var` 는 함수 `sayHi`의 시작 부분에서 처리되므로, `(*)`로 표시한 줄에서 `phrase`는 이미 정의가 된 상태인 것이죠.

**선언은 호이스팅 되지만 할당은 호이스팅 되지 않습니다.**

예제를 통해 이에 대해 알아보도록 합시다.

```js run
function sayHi() {
  alert(phrase);  

*!*
  var phrase = "Hello";
*/!*
}

sayHi();
```

`var phrase = "Hello"`행에선 두 가지 일이 일어납니다.

1. 변수 선언(`var`) 
2. 변수에 값을 할당(`=`)

변수 선언은 함수 실행이 시작될 때 처리되지만(호이스팅) 할당은 호이스팅 되지 않기 때문에 할당을 한 코드에서 처리됩니다. 따라서 위 예제는 아래 코드처럼 동작하게 됩니다.

```js run
function sayHi() {
*!*
  var phrase; // 선언은 함수 시작 시 처리됩니다.
*/!*

  alert(phrase); // undefined

*!*
  phrase = "Hello"; // 할당은 실행 흐름이 해당 코드에 도달했을 때 처리됩니다.
*/!*
}

sayHi();
```

이처럼 모든 `var` 선언은 함수 시작 시 처리되기 때문에, `var`로 선언한 변수는 어디서든 참조할 수 있습니다. 하지만 변수에 무언갈 할당하기 전까진 값이 undefined이죠.

바로 위의 두 예시에서 `alert`를 호출하기 전에 변수 `phrase`는 선언이 끝난 상태이기 때문에 에러 없이 얼럿 창이 뜹니다. 그러나 값이 할당되기 전이기 때문에 얼럿 창엔 `undefined`가 출력되죠.

## 요약

`var`로 선언한 변수는 `let`이나 `const`로 선언한 변수와 다른 두 가지 주요한 특성을 보입니다.

1. `var`로 선언한 변수는 블록 스코프가 아닌 함수 수준 스코프를 갖습니다.
2. `var` 선언은 함수가 시작되는 시점(전역 공간에선 스크립트가 시작되는 시점)에서 처리됩니다.

이 외에도 전역 객체와 관련된 특성 하나가 더 있는데, 이에 대해선 다음 챕터에서 다루도록 하겠습니다.

`var`만의 특성은 대부분의 상황에서 좋지 않은 부작용을 만들어냅니다. `let`이 표준에 도입된 이유가 바로 이런 부작용을 없애기 위해서입니다. 변수는 블록 레벨 스코프를 갖는 게 좋으므로 이제는 `let`과 `const`를 이용해 변수를 선언하는 게 대세가 되었습니다.
