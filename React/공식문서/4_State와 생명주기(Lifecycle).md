# 4. State와 생명주기(Lifecycle)

엘리먼트 렌더링에서 UI를 업데이트할 때, 렌더링 된 추력값을 변경하기 위해 `ReactDOM.render()`를 호출했었음.

```jsx
function tick() {
	const element = (
		<div>
			<h1>Hello, world!</h1>
			<h2>It is {new Date().toLocaleTimeString()}.</h2>
		</div>
	);
	ReactDOM.render( // 기존에 ReactDOM.redner()를 호출
		element,
		document.getElementById('root')
	);
}

setInterval(tick, 1000);
```

### 이번 섹션에서는 `Clock` 컴포넌트를 완전히 재사용하고 캡슐화하는 방법을 배울 것.

( 컴포넌트는 스스로 타이머를 설정하고, 매초 스스로 업데이트하도록! )

```jsx
// 변경 전 코드

function Clock(props) {
	return (
	<div>
		<h1>Hello, world!</h1>
		<h2>It is {props.date.toLocaleTimeString()}.</h2>
	</div>
	);
}

function tick() {
	ReactDOM.render(
		<Clock date={new Date()} />,
		document.getElementById('root')
	);
}

setInterval(tick, 1000);
```

`Clock`이 타이머를 설정하고 매초 UI를 업데이트하는 것이 `Clock`의 구현 세부사항이 되어야 함.

`Clock`이 스스로 업데이트하도록 만들려면, Clock 컴포넌트에 `"state"`를  추가해야 함.

State는 props와 유사하지만, 비공개이며 컴포넌트에 의해 완전히 제어됨.

### 1. 클래스에 로컬 State 추가하기

3 단계에 걸쳐서 `date`를  props → state로 이동해 보기

1) `render()` 메소드 안에 있는 `this.props.date`를  `this.state.date`로  변경한다.

```jsx
class Clock extends React.Component {
	render() {
		return (
			<div>
				<h1>Hello, world!</h1>
				<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
			</div>
		);
	}
}
```

2) 초기 `this.state`를 지정하는 class constructor를 추가한다.

```jsx
class Clock extends React.Component {
	constructor(props) {        // class constructor
		super(props);
		this.state = {date: new Date()};
	}
	render() {
		return (
			<div>
				<h1>Hello, world!</h1>
				<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
			</div>
		);
	}
} 
```

어떻게 props를 기본 constructor에 전달하는 지 살펴보기

→ 클래스 컴포넌트는 항상 `props` 로 기본 constructor를 호출해야 함.

3) `<Clock />`요소 에서 date prop을 삭제함.

```jsx
ReactDOM.render(
	<Clock />,
	document.getElementById('root')
);
```

### 결과

```jsx
class Clock extends React.Component {
	constructor(props) {
			super(props);
			this.state = {date: new Date()};
		}
	render() {
		return (
			<div>
```