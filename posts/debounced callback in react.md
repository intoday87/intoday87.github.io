source: https://www.developerway.com/posts/debouncing-in-react 

`debounce`함수를 사용하는 일은 잦다. 대표적으로 자동완성 요청이 떠오를 수 있다.  키보드 타이핑 스킬이 좋은 사람이면 1분에 70여개의 영단어를 타이핑할 수 있다고 한다. 그것은 대략 1초에 6키를 누른다는 것인데 이런 상황에 매 키 스트로크마다 자동완성 api를 요청한다거나 필터링 처리 로직으로 re-render을 발생시킨다면 사용자 경험측면에서도 성능 측면에서도 매우 좋지 않을 것이다.
이런 상황에서 우리가 주로 사용하는 것은 두 가지 정도가 있다 `debounce`와 `throttle`이다.  두 가지는 구현적 측면에서 비슷하다고 느껴지지만 처리하는 결과에서는 차이가 있다. 둘 다 실행할 함수와 ms동안 지연시킬 `delay` 파라미터를 받고 각각의 기능에 맞게 처리된 함수를 리턴한다. 이 함수를 앞서 언급한 타이핑하는 input의 `onChange`이벤트 핸들러로 사용한다고 생각해보자.

`debounce`: 지연시킬 ms 파라미터를 받아서 지연시간 동안 재호출이 들어오면 함수 호출을 무시하고 타이머를 다시 ms로 리셋한다. ms 동안 추가 호출 없이 지연이 무사히 끝나면 비로소 파라미터로 받았던 함수를 호출한다

`throttle`: 앞서 본 `debounce`는 요약하자면 우다다다 사용자 타이핑이 끝나고 나서 진정 되면 함수를 호출해 주는 반면 그 우다다다 타이핑이 길어지면 결국 그 동안에는 api호출을 하지 못하는 단점이 있어서 지연시킬 ms동안 함수 호출을 주기적으로 호출해준다. `debounce`는 `setTimeout`이라면 `throttle`은 `setInterval`같은 느낌

여기서는 중요한 것은 re-render에 대응하는 stale closure 문제를 react 환경에서 어떻게 다루느냐가 중점으로 다룰것이므로  `debounce`와  `throttle`  두 가지 케이스를 모두 다루지는 않고  `debounce`로 `useDebounce` 훅이 만들어지는 과정을만을 다루어본다.

머리로는 이해했어도 실제로 output을 내보며 한 단계 한 단계 단계를 밟아보면 늘 중간에 턱하고 막힐때가 있다. 이 부분이 바로 익숙함과 익숙하지 않음의 영역이라고 보고있고 각 단계를 밟아보자

## debounce in react component

react + ts 환경에서 컴포넌트에 debounce를 직접 써보자

```ts
import debounce from '@naverpay/hidash/debounce'

export default function App() {
	const { debounce: debounced } = debounce((e) => {
		// 최신 입력 정보를 키 타이핑이 멈춘 500ms 지연 후에 출력한다
		console.log(e.target.value);
	}, 500);

	return (
		<input onChange={debounced} />
	)
}
```

아무 문제 없이 동작한다. 정확히 키 타이핑이 멈추고 500ms 지연이 끝나면 비로소 input에 입력한 키 타이핑 결과를 출력한다.
하지만 우리는 state라는것을 사용해 컴포넌트가 각 state에 맞는 closure 내(re-rencer)에서 state를 활용해 컴포넌트에 정보를 표시하거나 사용한다

```ts
import debounce from '@naverpay/hidash/debounce'

export default function App() {
	const [state, setState] = useState('')
	const { debounce: debounced } = debounce((e) => {
		console.log(e.target.value);
	}, 500);

	return (
		<input onChange={(e) => {
			debounced(e)
			setState(e)
		}} />
	)
}
```

실행해보면  console.log에 찍히는 값은 500ms의 지연 후에 찍히는게 아니라 매번 `setState`로 input의 변경을 re-render를 발생시는 횟수만큼 찍히게 된다

왜 그럴까?