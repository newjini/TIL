# 3. Component와 Props

### 컴포넌트

컴포넌트를 통해 UI를 재사용 가능한 개별적인 여러 조각으로 나누고, 각 조각을 개별적으로 살펴볼 수 있다.

- 개념적으로 컴포넌트는 JavaScript 함수와 유사함. `"props"`라고 하는 임의의 입력을 받은 후, 화면에 어떻게 표시되는 지를 기술하는 `React 엘리먼트`를 반환함.

### 1. 함수 컴포넌트와 클래스 컴포넌트

컴포넌트 정의 방법 중 가장 간단한 방법은 JavScript 함수를 작성하는 것.

```jsx
// 함수형 컴포넌트
function Welcome(props) {
	return <h1>Hello, {props.name}</h1>;
}
```

이 함수는 데이터를 가진 하나의 `"props"(props: 속성을 나타내는 데이터)` 객체 인자를 받은 후 React 엘리먼트를 반환하므로 `유효한 React 컴포넌트`이다. 이러한 컴포넌트는 JavaScript 함수이기때문에 말 그대로 `“함수 컴포넌트"`라고 불림.

```jsx
// 클래스형 컴포넌트
class Welcome extends React.Component {
	render() {
		return <h1>Hello, {this.props.name}</h1>;
	}
}
```

또한 ES6 class를 사용해 컴포넌트 정의가 가능함.

→ React 관점으로는 위 2가지 유형의 컴포넌트는 동일함.

### 2. 컴포넌트 렌더링

`const element = <div />;`

- 이전까지는 DOM 태그만을 사용해 React 엘리먼트를 나타냄.

`const element = <Welcome name="Yujin" />;` 

- React 엘리먼트는 사용자 정의 컴포넌트(`=Welcome`)로도 나타낼 수 있음.

React가 사용자 정의 컴포넌트로 작성한 엘리먼트를 발견하면 JSX 어트리뷰트와 자식을 해당 컴포넌트에 단일 객체로 전달함. → 이 객체를 `"props"`라고 함.

```jsx
// 페이지에서 "Hello, Yujin"을 렌더링하는 예시.
function Welcome(props) { // Welcome : 사용자 정의 컴포넌트
	return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Yujin" />;
ReactDOM.render(
	element,
	document.getElementById('root')
);
```

1. `<Welcome name="Yujin"/>` 엘리먼트로 `ReactDOM.render()`를 호출함.
2. React는 `{name: 'Yujin'}`를 props로 하여 Welcome 컴포넌트를 호출함.
3. Welcome 컴포넌트는 결과적으로 `<h1>Hello, Yujin</h1>`엘리먼트를 반환함.
4. ReactDOM은 `<h1>Hello, Yujin</h1>`엘리먼트와 일치하도록 DOM을 효율적으로 업데이트함.

### 🤚주의

 컴포넌트의 이름은 항상 `대문자`로 시작함.

> React는소문자로 시작하는 컴포넌트를 DOM 태그로 처리함. 예를 들어 <div />는 HTML div 태그를 나타내지만, <**W**elcome />은 컴포넌트를 나타내며 범위 안에 Welcome이 있어야 함.
> 

### 3. 컴포넌트 합성

컴포넌트는 자신의 출력에 다른 컴포넌트 `참조가 가능`함. → 모든 세부단계에서 `동일한 추상 컴포넌트를 사용할 수 있음`을 뜻함. React 앱에서는 버튼, 폼, 다이얼로그, 화면 등 모든 것들이 흔히 컴포넌트로 표현됨.

```jsx
// Welcome을 여러 번 렌더링하는 App 컴포넌트 예시
function Welcome(props) {
	return <h1>Hello, {props.name}</h1>;
}

function App() {
	return (
		<div>
			<Welcome name="Yujin" />
			<Welcome name="BMO" />
			<Welcome name="Heeyoung"/>
		</div>
	);
}

ReactDOM.render(
	<App />,
	document.getElementById('root')
);
```

일반적으로 새 React 앱은 `최상위`에 단일 `App 컴포넌트`를 가짐. 하지만 기존 앱에 React를 통합하는 경우, `Button`과 같은 작은 컴포넌트부터 시작해서 뷰 계층의 상단으로 올라가면서 점진적으로 작업해야 할 수 있음.

### 4. 컴포넌트 추출

컴포넌트를 여러 개의 작은 컴포넌트로 나눌 수도 있음.

```jsx
function Comment(props) {
	return (
		<div className="Comment">
			<div className="UserInfo">
				<img className="Avatar"
					src={props.author.avatarUrl}
					alt={props.author.name}
				/>
				<div className="UserInfo-name">
					{props.author.name}
				</div>
			</div>
			<div className="Comment-text">
				{props.text}
			</div>
			<div className="Comment-date">
				{formatDate(props.date)}
			</div>
		</div>
	);
}
```

위 컴포넌트는 `author`(객체), `text`(문자열) 및 `date`(날짜)를 props로 받은 후 소셜 미디어 웹 사이트의 코멘트를 나타냄.

이 컴포넌트는 구성요소들이 모두 중첩 구조로 이루어져 있어서 변경하기 어려울 수 있으며, 각 구성요소를 개별적으로 재사용하기도 힘듦.

### → 추출해보기 !

```jsx
// 먼저, Avatar 추출
function Avatar(props) {
	return (
		<img className="Avatar"
					src={props.user.avatarUrl}
					alt={props.user.name}
		/>
	);
}
-> Avatar는 자신이 Comment 내에서 렌더링 된다는 것을 알 필요가 없음. 따라서 props의 이름을 author에서 더욱 일반화된 user로 변경함.

// UserInfo 추출 ( Avatar 추출한 후의 UserInfo 추출 )
function UserInfo(props){
	return (
		<div className="UserInfo">	
			<Avatar user={props.user}/>
			<div className="UserInfo-name">
			{props.user.name}
		</div>
	);
}
```

*** props의 이름은 사용될 context가 아닌 컴포넌트 자체의 관점에서 짓는 것을 권장함.

```jsx
// Avatar, UserInfo 추출 뒤 Comment 컴포넌트
function Comment(props) {
	return (
		<div className="Comment">
			<UserInfo user={props.author} />
			<div className="Comment-text">
				{props.text}
			</div>
			<div className="Comment-date">
				{formatDate(props.date)}
			</div>
		</div>
	);
}
```

### 5. props는 읽기 전용입니다.

`함수 컴포넌트나 클래스 컴포넌트 모두 컴포넌트의 자체 props를 수정해서는 안된다.`

```jsx
function sum(a, b) {
	return a + b;
}
```

`sum` 함수는 `순수 함수`라고 함. → 입력값을 바꾸려하지 않고 항상 동일한 입력값에 대해 동일한 결과를 반환하기 때문에

```jsx
function withdraw(account, amount) {
	account.total -= amount;
}
```

`withdraw`함수는 자신의 입력값을 변경하기 때문에 순수함수가 아님.

→ React는 한 가지 엄격한 규칙이 있는데, 

**모든 React 컴포넌트는 자신의 props를 다룰 때 반드시 순수 함수처럼 동작해야 함.**

물론 애플리케이션 UI는 동적이며 시간에 따라 변한다. React 컴포넌트는 `state`를 통해 위 규칙을 위반하지 않고 사용자 액션, 네트워크 응답 및 다른 요소에 대한 응답으로 시간에 따라 자신의 출력값을 변경할 수 있음.