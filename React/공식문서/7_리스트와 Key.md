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
### 2. 기본 리스트 컴포넌트

일반적으로 `컴포넌트`안에서 리스트를 렌더링한다.

이전 예시를 numbers 배열을 받아서 순서없는 엘리먼트 리스트를 출력하는 컴포넌트로 리팩토링할 수 있다.

```jsx
function NumberList(props) {
	const numbers = props.numbers;
	const listItems = numbers.map((number) =>
		<li>{number}</li>
	);
	return (
		<ul>{listItems}</ul>
	);
}
const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
	<NumberList numbers={numbers} />,
	document.getElementById('root')
);
```

위 코드를 실행하면 리스트의 각 항목에 key를 넣어야 한다는 경고가 표시된다. “key”는 엘리먼트 리스트를 만들 때 포함해야 하는 특수한 문자열 어트리뷰트다. 이제 `numbers.map()` 안에서 리스트의 각 항목에 `key`를 할당하여 키 누락 문제를 해결해보자.

```jsx
function NumberList(props){
	const numbers = props.numbers;
	const listItems = numbers.map((number) =>
		<li key={number.toString()}>
			{number}
		</li>
	);
	return (
		<ul>{listItems}</ul>
	);
}
const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
	<NumberList numbers={numbers} />,
	document.getElementById('root')
);
```
### 3. Key
Key는 React가 어떤 항목을 변경, 추가 또는 삭제할 지 식별하는 것을 돕는다. key는 엘리먼트에 안정적인 고유성을 부여하기 위해 배열 내부의 엘리먼트에 지정해야 한다.

```jsx
const numbers = [1,2,3,4,5];
const listItems = numbers.map((number) =>
	<li key={number.toString()}>
		{number}
	</li>
);
```

Key를 선택하는 가장 좋은 방법은 `리스트의 다른 항목들 사이에서 해당 항목을 고유하게 식별할 수 있는 문자열을 사용하는 것.` 대부분의 경우, **데이터의 ID**를 key로 사용함.

```jsx
const todoItems = todos.map((todo) =>
	<li key={todo.id}>
		{todo.text}
	</li>
);
```

렌더링 한 항목에 대한 안정적인 ID가 없다면 최후의 수단으로 `항목의 인덱스`를 key로 사용 가능.

```jsx
const todoItems = todos.map((todo, index) =>
	// Only do this if items have no stable IDs
	<li key={index}>
		{todo.text}
	</li>
);
```

항목의 순서가 바뀔 수 있는 경우, key에 인덱스를 사용하는 것은 권장 X.  
이로 인해 성능이 저하되거나 컴포넌트의 state와 관련된 문제가 발생할 수 있음.  
리스트 항목에 명시적으로 key를 지정하지 않으면 React는 기본적으로 **인덱스**를 key로 사용함.
### 4. Key로 컴포넌트 추출하기

키는 주변 배열의 context에서만 의미가 있다.  
예를 들어 `ListItem`컴포넌트를 추출한 경우 `ListItem`안에 있는 `<li>` 엘리먼트가 아니라 배열의 `<ListItem />`엘리먼트가 key를 가져야 한다.

[ 예시 : 잘못된 Key 사용법 ]

```jsx
function ListItem(props) {
	const value = props.value;
	return (
		// Wrong! 여기에는 key를 지정할 필요 X.
		<li key={value.toString()}>
			{value}
		</li>
	);
}
function NumberList(props) {
	const numbers = props.numbers;
	const listItems = numbers.map((number) =>
		// Wrong! 여기에 key를 지정해야 한다.
		<ListItem value={number} />
	);
	return (
		<ul>
			{listItems}
		</ul>
	);
}
const numbers = [1,2,3,4,5];
ReactDOM.render(
	<NumberList numbers={numbers} />,
	document.getElementById('root')
);
```

[ 예시 : 올바른 Key 사용법 ]

```jsx
function ListItem(props) {
	// Correct! 여기에는 key 지정할 필요 X
	return <li>{props.value}</li>;
}
function NumberList(props) {
	const numbers = props.numbers;
	const listItems = numbers.map((number) =>
		// Correct! 배열 안에 key를 지정해야 함.
		<ListItem key={number.toString()} value={number}/>
	);
	return (
		<ul>
			{listItems}
		</ul>
	);
}
const numbers = [1,2,3,4,5];
ReactDOM.render(
	<NumberList numbers={numbers} />,
	document.getElementById('root')
);
```

** 경험상 `map()` 함수 내부에 있는 엘리먼트에 key를 넣어 주는 게 좋다! **