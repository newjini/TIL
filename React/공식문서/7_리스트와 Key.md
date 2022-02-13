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