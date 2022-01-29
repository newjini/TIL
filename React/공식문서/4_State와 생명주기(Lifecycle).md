# 4. State와 생명주기(Lifecycle)

엘리먼트 렌더링에서 UI를 업데이트할 때, 렌더링 된 출력값을 변경하기 위해 `ReactDOM.render()`를 호출했었음.

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

## State를 올바르게 사용하기 [ 특징 ]

- `setState()`에 대해 알아야 할 세 가지 특징이 있다.

### 1. 직접 State를 수정하지 마세요

아래의 Wrong 코드는 컴포넌트를 다시 렌더링하지 않는다.

```jsx
// Wrong
this.state.comment = 'Hello';
```

대신, `setState()`를 사용한다. 

```jsx
// Correct
this.setState({comment: 'Hello'});
```

→ `this.state`를 지정할 수 있는 유일한 공간은 바로 `“constructor"`이다. 

### 2. State 업데이트는 비동기적일 수 있다.

- React는 성능을 위해 여러 setState() 호추을 단일 업데이트로 한꺼번에 처리할 수 있다.
- `this.props` 와 `this.state`가 비동기적으로 업데이트될 수 있기 때문에 다음 state를 계산할 때 해당 값에 의존해서는 안된다.

아래의 Wrong 코드는 카운터 업데이트에 실패할 수 있다.

```jsx
// Wrong
this.setState({
	counter: this.state.counter + this.props.increment,
});
```

이를 수정하기 위해 객체보다는 **“함수를 인자로 사용”**하는 다른 형태의 `setState()`를 사용한다. 그 함수는 이전 state를 첫 번째 인자로 받아들일 것이고, 업데이트가 적용된 시점의 props를 두 번째 인자로 받아들일 것이다.

```jsx
// Correct
this.setState((state, props) => ({
	counter: this.state.counter + props.increment
}));

// 위와 같은 화살표 외에 일반 함수에서도 정상적으로 작동함.
// Correct
this.setState(function(state, props) {
	return {
		counter: state.counter + props.increment
	};
});
```

### 3. State 업데이트는 병합된다.

- `setState()`를 호출할 때 React는 제공한 객체를 현재 state로 병합한다.

예를 들어, state는 다양한 독립적인 변수를 포함할 수 있다.

```jsx
constructor(props) {
	super(props);
	this.state = {
		posts: [],
		comments: []
	};
}
```

별도의 `setState()` 호출로 이러한 변수를 독립적으로 업데이트할 수 있다.

```jsx
componentDidMount() {
	fetchPosts().then(response => {
		this.setState({
			posts: response.posts
		});
	});
	
	fetchComments().then(response => {
		this.setState({
			comments: response.comments
		});
	});
}
```

→ 병합은 얕게 이루어지기 때문에 `this.setState({comments})`는 `this.state.posts`에 영향을 주진 않지만 `this.state.comments`는 완전히 대체된다.

### +) 추가적으로, 데이터는 아래로 흐른다.

- 부모 컴포넌트나 자식 컴포넌트 모두 특정 컴포넌트가 유상태인지 또는 무상태인지 알 수 없고, 그들이 함수나 클래스로 정의되었는 지에 대해 관심을 가질 필요가 없다.
- 이때문에 state는 종종 `로컬 또는 캡슐화`라고 불린다. state가 소유하고 설정한 컴포넌트 이외에는 어떠한 컴포넌트에도 접근할 수 없다.

컴포넌트는 자신의 state를 자식 컴포넌트에 props로 전달할 수 있다. ( 부모 state → 자식 props로 )

`<FormattedDate date={this.state.date} />` 

FormattedDate 컴포넌트는 date를 자신의 props로 받을 것이고 이것이 Clock의 state로부터 왔는지, Clock의 props에서 왔는지, 수동으로 입력한 것인지 알지 못한다.

```jsx
function FormattedDate(props) {
	return <h2>It is {props.date.toLocaleTimeString()}.</h2>;
}
```

일반적으로 이를 `"하향식(top-down)"` 또는 `"단향방식"` 데이터 흐름이라고 한다. 모든 state는 항상 특정한 컴포넌트가 소유하고 있으며 그 state로부터 파생된 UI 또는 데이터는 오직 트리구조에서 자신의 `“아래”` 에 있는 컴포넌트에만 영향을 미친다.

트리구조가 props들의 폭포라고 상상하면 각 컴포넌트의 state는 임의의 점에서 만나지만 동시에 아래로 흐르는 부가적인 수원(water source)이라고 할 수 있다.

모든 컴포넌트가 완전히 독립적이라는 것을 보여주기 위해 `App` 렌더링하는 세 개의 `<Clcok>`을 만들었다.

```jsx
function App(){
	return (
		<div>
			<Clock />
			<Clock />
			<Clock />
		</div>
	);
}

ReactDOM.render(
	<App />,
	document.getElementById('root')
);
```

→ 각 Clock은 자신만의 타이머를 설정하고 독립적으로 업데이트를 한다.

React 앱에서 컴포넌트가 유상태 또는 무상태에 대한 것은 시간이 지남에 따라 변경되 수 있는 구현 세부 사항으로 간주한다. 유상태 컴포넌트 안에서 무상태 컴포넌트를 사용할 수 있으며, 그 반대 경우도 마찬가지로 사용할 수 있다.