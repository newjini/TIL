# 7. 리스트와 Key

먼저, JavaScript에서 리스트를 어떻게 변환하는 지 살펴보면,

```jsx
const numbers = [1,2,3,4,5];
const doubled = numbers.map((number) => number * 2);
console.log(doubled);
```

`map()`함수를 이용하여  numbers 배열의 값을 두배로 만든 후 `map()`에서 반환하는 새 배열을 doubled 변수에 할당하고 로그를 확인하는 코드인데,

이 코드는 콘솔에 `[ 2, 4, 6, 8, 10 ]` 을 출력한다.

React에서 배열을 엘리먼트 리스트로 만드는 방식은 이와 거의 동일함.
### 1. 여러 개의 컴포넌트 렌더링하기

엘리먼트 모음을 만들고 `중괄호 {}` 를 이용하여 `JSX에 포함`시킬 수 있다.

아래의 JavaScript `map()` 함수를 사용해 `numbers` 배열을 반복 실행한다. 각 항목에 대해 `<li>` 엘리먼트를 반환하고 엘리먼트 배열의 결과를 `listItems`에 저장한다.

```jsx
const numbers = [1,2,3,4,5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);
```

`listItems` 배열을 `<ul>` 엘리먼트 안에 포함하고 DOM에 렌더링한다. 

```jsx
ReactDOM.render(
	<ul>{listItems}</ul>,
	document.getElementById('root')
);
```

이 코드는 1부터 5까지의 숫자로 이루어진 리스트를 보여준다.