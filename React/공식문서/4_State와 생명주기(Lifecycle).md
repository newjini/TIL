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

### [ 결과 ]

```jsx
class Clock extends React.Component {
	constructor(props) {
			super(props);
			this.state = {date: new Date()};
		}
	render() {
		return (
			<div>
				<h1>Hello, world!</h1>
				<h2>It is
					{this.state.date.toLocaleTimeString()}.</h2>
			</div>
		);
	}
}

ReactDOM.render(
	<Clock />,
	document.getElementById('root')
);
```

### 2. 생명주기 메소드를 클래스에 추가하기

많은 컴포넌트가 있는 애플리케이션에서 컴포넌트가 삭제될 때 해당 컴포넌트가 사용 중이던 리소스를 확보하는 것이 중요하다.

`Clock`이 처음 DOM에 렌더링 될 때마다 타이머를 설정하려고 함. → React에서 `“마운팅"`이라고 함.

또, `Clock`에 의해 생성된 DOM이 삭제될 때마다 타이머를 해제하려고 함. → React에서 `“언마운팅”`이라고 함.

컴포넌트 클래스에서 특별한 메소드를 선언하여 컴포넌트가 마운트 혹은 언마운트될 때 일부 코드를 작동할 수 있음.

```jsx
class Clock extends React.Component {
	constructor(props) {
		super(props);
		this.state = {date: new Date()};
	}
	componentDidMount() {
	}
	componentWillUnmount() {
	}
	render() {
		return (
			<div>
				<h1>Hello, world!</h1>
				<h2>It is
					{this.state.date.toLocaleTimeString()}.</h2>
			</div>
		);
	}
}
```

이러한 메소드(componentDidMount(), componentWillUnmount())들을 `“생명주기 메소드”`라고 함.

- `componentDidMount()` : 컴포넌트 출력물이 DOM에 렌더링된 후에 실행됨. 타이머를 설정하기 좋은 장소다.

```jsx
componentDidMount() {
	this.timerID = setInterval(
		() => this.tick(),
		1000
	);
}
```

`this(this.timerID)`에서 어떻게 타이머ID를 제대로 저장하는 지 주의하기.

→ `this.props`가 React에 의해 스스로 설정되고 `this.state`가 특수한 의미가 있지만, 타이머ID와 같이 데이터 흐름 안에 포함되지 않는 어떤 항목을 보관할 필요가 있다면 자유롭게 클래스에 수동으로 부가적인 필드를 추가해도 된다.

```jsx
componentWillUnmount() {
	clearInterval(this.timerID);
}
```

마지막으로, Clock 컴포넌트가 매초 작동하도록 하는 `tick()`이라는 메소드를 구현한다.

tick() 메소드는 컴포넌트 로컬 state를 업데이트하기 위해 `this.setState()`를 사용한다. 

### [ 결과 ]

```jsx
class Clock extends React.Component {
	constructor(props) { // 1번
		super(props);
		this.state = {date: new Date()};
}
componentDidMount() {  // 3번
	this.timerID = setInterval(
		() => this.tick(),
		1000
	);
}
componentWillUnmount() {  // 5번
	clearInterval(this.timerID);
}
tick() {  // 4번
	this.setState({
		date: new Date()
	});
}
render() {  // 2번, 4번
	return (
		<div>
			<h1>Hello, world!</h1>
			<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
		</div>
	);
}
ReactDOM.render(
	<Clock />,
	document.getElementById('root')
);
```

이제 시계는 매초 째깍거린닷!

### 정리를 해보자면,

1. `<Clock />`가 `ReactDOM.render()`로 전달되었을 때 React는 Clock 컴포넌트의 constructor로 호출된다. Clock이 현재 시각을 표시해야 하기 때문에 현재 시각이 포함된 객체로 `this.state`를 초기화한다. 나중에 이 `"state"`를 업데이트할 것이다
2. React는 Clock 컴포넌트의 `render()` 메소드를 호출한다. 이를 통해 React는 화면에 표시되어야 할 내용을 알게된다. 그 다음 React는 Clock의 렌더링 출력값을 일치시키기 위해 DOM을 업데이트한다.
3. Clock 출력값이 DOM에 삽입되면, React는 `componentDidMount()` 생명주기 메소드를 호출한다. 그 안에서 Clock 컴포넌트는 매초 컴포넌트의 `tick()` 메소드를 호출하기 위한 타이머를 설정하도록 브라우저에 요청한다.
4. 매초 브라우저가 `tick()`메소드를 호출한다. 그 안에서 Clock 컴포넌트는 `setState()`에 현재 시각을 포함하는 객체를 호출하면서 UI 업데이트를 진행한다. `setState()` 호출 덕분에 React는 state가 변경된 것을 인지하고 화면에 표시될 내용을 알아내기 위해 `render()`메소드를 다시 호출한다. 이 때 `render()`메소드 안의 `this.state.date`가 달라지고 렌더링 출력값은 업데이트된 시각을 포함한다. React는 이에 따라 DOM을 업데이트한다. 
5. Clock 컴포넌트가 DOM으로부터 한 번이라도 삭제된 적이 있다면 React는 타이머를 멈추기 위해 `componentWillUnmount()`생명주기 메소드를 호출한다.