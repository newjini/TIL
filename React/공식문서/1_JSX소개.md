# 1.JSX 소개
### 1. JSX란?

`const element = <h1>Hello, world!</h1>;`

JavaScript를 확장한 문법으로, React 엘리먼트(element)를 생성함.

React는 별도의 파일에 `마크업과 로직을 넣어` 기술을 인위적으로 분리하는 대신, 둘 다 포함하는 `“컴포넌트”`라고 부르는 느슨하게 연결된 유닛으로 관심사를 분리함.

### 2. JSX에 표현식 포함하기

```jsx
const name = 'Yujin Kim';
const element = <h1>Hello, {name}</h1>;

ReactDOM.render(
	element,
	document.getElementById('root')
);
```

→ JSX의 중괄호 안에는 유효한 모든 JavaScript표현식을 넣을 수 있음.

ex) 2+2, user.fristName, formatName(user) 등은 모두 유효한 JavaScript 표현식

```jsx
function formatName(user){
	return user.firstName + ' ' + user.lastName;
}
const user = {
	firstName: 'Yujin',
	lastName: 'Kim'
};

const element = (
	<h1>
		Hello, {formatName(user)}!
	</h1>
);
```

→ JavaScript 함수 호출 결과인 formatName(user)을 <h1> 엘리먼트에 포함함.

자동 세미콜론 삽입을 피하고자 괄호로 묶는 것(`element = ();`)을 권장함.

### 3. JSX도 표현식입니다

컴파일이 끝나면, JSX 표현식이 정규 JavaScript 함수 호출이 되고 JavaScript 객체로 인식됨.

```jsx
function getGreeting(user) {
	if(user) {
		return <h1>Hello, {formatName(user)}!</h1>;
	}
	return <h1>Hello, Stranger.</h1>;
}
```

→ JSX를 if 구문 및 for loop 안에 사용하고, 변수에 할당하고, 인자로서 받아들이고, 함수로부터 반환할 수 있다

### 4. JSX 속성 정의

`const element = <div tabIndex="0"></div>;`

→ 어트리뷰트에 따옴표를 이용해 문자열 리터럴을 정의할 수 있음.

`const element = <img src={user.avatarUrl}></img>;`

→ **중괄호**를 사용해 어트리뷰트에 JavaScript 표현식을 삽입할 수 있음.

어트리뷰트에 JavaScript 표현식을 삽입할 때 중괄호 주변에 따옴표 입력금지.

- 따옴표(문자열 값에 사용) 또는 중괄호(표현식에 사용) 중 하나만 사용하고, 동일한 어트리뷰트에 두 가지를 동시에 사용하면 안된다.

### 5. JSX로 자식 정의

`const element = <img src={user.avatarUrl}`

→ 태그가 비어있다면 XML처럼 `/>` 을 이용해 바로 닫아주어야 함.

```jsx
const element = (
	<div>
		<h1>Hello!</h1>
		<h2>Good to see you here.</h2>
	</div>
);
```

→ JSX 태그는 자식을 포함할 수 있음.

### 6. JSX는 주입 공격을 방지합니다

```jsx
const title = response.potentiallyMaliciouInput;
// 이것은 안전함.
const element = <h1>{title}</h1>
```

→ JSX에 사용자 입력을 삽입하는 것은 안전함.  ⇒ JSX에 직접 태그써도 안 먹힘

기본적으로 React DOM은 JSX에 삽입된 모든 값을 렌더링하기 전에 `이스케이프` 하므로, 애플리케이션이 명시적으로 작성되지 않은 내용은 주입되지 않음. 

모든 항목은 렌더링 되기 전에 문자열로 변환됨. 이런 특성으로 인해 `XSS (cross-site-scripting)` 공격을 방지할 수 있음.

### 7. JSX는 객체를 표현합니다

- Babel은 JSX를 `React.createElement()` 호출로 컴파일함.

```jsx
const element = (
	<h1 className="greeting">
		Hello, world!
	</h1>
);

const element = React.createElement(
	'h1',
	{className: 'greeting'},
	'Hello, world!'
);
```

→ 두 예시는 동일함. `React.createElement()`는 버그가 없는 코드를 작성하는 데 도움이 되도록 몇 가지 검사를 수행한다.

```jsx
// 주의: 다음 구조는 단순화되었음.
// -> 보면 element 중괄호임.(=객체라는뜻)
const element = {
	type: 'h1',
	props: {
		className: 'greeting',
		children: 'Hello, world!'
	}
};
```

이러한 객체를 `“React 엘리먼트”`라고 하며, 화면에서 보고 싶은 것을 나타내는 표현이라 생각하면 된다. 

React는 이 객체를 읽어서, DOM을 구성하고 최신 상태로 유지하는 데 사용함.