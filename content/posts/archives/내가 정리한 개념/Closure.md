---
title: "클로저(Closure) 발표 자료"
date: "2021-07-01"
description: "클로저 발표 내용"
tags: ["클로저"]
categories: "내가 정리한 개념"
ShowToc: false
ShowBreadCrumbs: false
---

클로저는 함수형 프로그래밍 언어에서 등장하는 보편적인 특성이다. JS 고유의 개념이 아니라 ECMAScript 명세서에도 클로저 정의를 다루지 않고 있다.

MDN에서는 클로저를 이렇게 정의하고 있다.

> A closure is the combination of a function and the lexical environment within which that function was declared

```jsx
'클로저는 함수와 그 함수가 선언될 당시의 Lexical environment와의 상호관계에 따른 현상'
```

함수 내부에서 함수 외부에 있는 변수에 접근할 수 있다는 사실을 우리는 알고 있습니다.

위에서 언급하고 있는 "**현상**"의 의미를 알아보자

```jsx
function A() {
	function B() {
		//.....
	}
}
```

내부함수 B가 A의 LexicalEnvironment를 언제나 사용하는 것은 아니다. 내부함수에서 외부 변수를 참조하지 않는 경우라면 combination이라고 할 수 없겠죠. 내부함수에서 외부 변수를 참주하는 경우에 한해서만 combination, 즉 '선언될 당시의 LexicalEnvironment와의 상호관계'가 의미 있을 것입니다.

지금까지 파악한 내용으로는 클로저란 "**어떤 함수에서 선언한 변수를 참조하는 내부 함수에서만 발생하는 현상**"이라고 볼 수 있다.

<br />

예시를 통해 알아보자

외부 함수의 변수를 참조하는 내부함수(1)

```jsx
var outer = function() {
	var a = 1;

	var inner = function() {
		console.log(++a); //2
	};

	inner();

}
```

- inner 함수 내부에서는 a를 선언하지 않았기 때문에 EnvironmentRecord에서 값을 찾지 못하므로 outerEnvironmentReference에 지정된 상위 컨텍스트인 outer의 LexicalEnvironment에 접근해서 다시 a를 찾는다.

<br />

외부 함수의 변수를 참조하는 내부함수(2)

```jsx
var outer = function() {
	var a = 1;

	var inner = function() {
		return ++a;
	};

	return inner; ✅

}

var outer2 = outer();
console.log(outer2); //2
```

- 이번에도 똑같이 inner함수 내부에서 외부 변수인 a를 사용했다. 그런데 ✅  줄에는 inner 함수를 실행한 결과를 리턴하고 있으므로 결과적으로 outer 함수의 실행 컨텍스트가 종료된 시점에는 a 변수를 참조하는 대상이 없어진다.

<br />

예제 1과 2 둘다 outer 함수의 실행 컨텍스트가 종료되기 이전에 inner 함수의 실행 컨텍스트가 종료돼 있으며, 이후 별도로 inner 함수를 호출할 수 없다는 공통점이 있습니다. 그렇다면 **outer의 실행 컨텍스트가 종료된 후에도 inner 함수를 호출할 수 있게 만들면 어떨까?**

<br />

외부 함수의 변수를 참조하는 내부함수(3)
```jsx
const outer = () => {
    let a = 1;
    const inner = () => {
        return ++a;
    };
    return inner; ✅  
};

let outer2 = outer(); 🔥

console.log(outer2()); //2 🍓
console.log(outer2()); //3
```

이번 ✅  줄에서는 inner 함수의 실행 결과가 아닌 inner 함수 자체를 반환했습니다.

그러면 outer 함수의 실행 컨텍스트가 종료될 때(🔥) **outer2 변수는 outer의 실행 결과인 inner 함수를 참조하게 될 것 입니다.**

<img src="../../../../data/images/스크린샷%202021-07-02%20오전%2012.00.11.png" />

이후 🍓 줄에서 outer2를 호출하면서 앞서 반환된 inner 함수가 가 실행되겠죠.

outer2() 함수를 실행할 때, **inner 함수의 실행 컨텍스트의 environmentRecord(함수 내부에 있는 값들)에는 수집할 정보가 없다.** 

outer-EnvironmentReference(outer())에는 inner함수가 선언된 위치의 LexicalEnvironment가 참조 복사된다.

inner 함수는 outer 함수 내부에서 선언됐으므로, outer 함수의 LexicalEnvironment가 담길 것이다.

----------------------inner() 전역 컨텍스트----------------------
- environmentRecord: { 텅 텅 }
- outerEnvironmentReference: outer() { a: 1 → 2 → 3 }

<br />

이제 스코프 체이닝에 따라 outer에서 선언한 변수 a에 접근해 1만큼 증가시킨 후 그 값인 2를 반환하고 inner 함수의 실행 컨텍스트가 종료된다.

### 이상한 점! 🤔
inner함수 실행 시점에는 outer 함수는 이미 실행이 종료된 상태인데 outer 함수의 LexicalEnvironment에 어떻게 접근할 수 있을까? 이는 **가비지 컬렉터의 동작 방식** 때문이다.

***가비지 컬렉터는 어떤 값을 참조하는 변수가 하나라도 있다면 그 값은 수집 대상에 포함시키지 않는다.***

외부함수인 outer의 실행이 종료되더라도 내부 함수인 inner 함수는 언젠가 outer2를 실행함으로써 호출될 가능성이 열린것이다. 언젠가 inner 함수의 실행 컨텍스트가 활성화되면 outerEnvironmentReference가 outer 함수의 LexicalEnvironment를 필요로 할 것 이므로 수집대상에서 제외된다. 그 덕에 inner 함수가 이 변수에 접근할 수 있다.

클로저는 어떤 함수에서 선언한 변수를 참조하는 내부함수에서만 발생하는 현상이라고 했다. 함수의 실행 컨텍스트가 종료된 후에도 LexicalEnvironment가 가비지 컬렉터의 수집 대상에서 제외되는 경우는 위의 예제와 같이 지역변수를 참조하는 내부함수가 외부로 전달된 경우가 유일하다.

즉, "어떤 함수에서 선언한 변수를 참조하는 내부함수에서만 발생하는 현상"이란 **외부 함수의 LexicalEnvironment가 가비지 컬렉팅 되지 않는 현상**을 말하는 것이다.

이를 바탕으로 정의를 다시 고쳐보면 클로저란 어떤 함수 A에서 선언한 변수 a를 참조하는 내부함수 B를 외부로 전달할 경우 A의 실행 컨텍스트가 종료된 이후에도 변수 a가 사라지지 않는 현상을 말한다.

주의할 점은 '외부로 전달'이 곧 return만을 의미하는 것은 아니