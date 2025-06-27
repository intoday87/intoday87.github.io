[advanced react](https://www.advanced-react.com/)에서 다루는 내용을 읽고 이해한 부분을 바탕으로 정리해 본다. 이 주제를 읽으면서 생각해보니 나도 모르게 memo가 깨지는 상황에 대해 잘 인지하지 않고 개발하는 경우가 있었던 것 같다

저자는 주제에 앞서 다음과 같은 말을 한다

> And I'm not joking or exaggerating about half the time by the way. **Doing memoization properly is hard, much harder than it seems.** By the end of this chapter, hopefully, you'll agree with me. Here you'll learn:

Source: Makarevich, Nadia. Advanced React: Deep dives, investigations, performance patterns and techniques (p. 79). (Function). Kindle Edition. 

왜 그렇게 말을 하는지 살펴보자
첫 번째로 다음과 같은 케이스는 memoizing props라는 anti pattern으로 소개하고 있는데 memoization의 효과가 있을까?

```jsx
export default function Component() {
	const handleCallback = useCallback(() => {
		// handle click...
	}, [/* dependencies... */])
	return <button type="button" onClick={handleClick}>click</button>
}
```

이 케이스는 많은 사람들의 믿음에 근거하고 있고 심지어 chatGPT도 효과가 있다고 설명한다는데 실제로는 효과가 없다.(확인해보니 현재 시점의 chatGPT(무료버전) 기준으로는 효과가 없다고 잘 설명하고 있다✨)
그 이유는 모두가 알다시피 컴포넌트의 상태가 변경되면 해당 컴포넌트의 하위(down stream) 컴포넌트도 re-render 된다. 그 점에서 `Component`가 re-render 될 때 `button` 컴포넌트는 memoization된 props를 받게된다고 re-render를 멈출 수 있을까? 그렇지는 않다. (memo로 감싸져 있는 경우가 아니라면)

또 다른 케이스를 보자

```jsx
// components/A.jsx
export default function A() {
	console.log("A component render");
	
	return <div>A</div>;
}

// components/Parent.jsx
import { memo } from "react";
  
export default memo(function Parent({ children }) {
	console.log("Parent render");
	return <div>{children}</div>;
});

// app.jsx
import Parent from "./components/Parent";
import A from "./components/A";
import { useState } from "react";

export default function App() {
	const [state, setState] = useState(false);
	
	console.log("App component render");
	
	return (
		<>
			<Parent>
				<A />
			</Parent>
			<button onClick={() => setState((v) => !v)}>button</button>
		</>
	);
}
```

얼핏 보기에는 처음에는 App -> Parent -> A가 렌더링 되고 button 클릭시 App만 렌더링 될 것 같아 보인다.
왜 그럴까?

그 이유는 두 가지로 정리를 할 수 있을것 같다

첫 번째 - children as prop: 말 그대로 children은 prop이고 nesting된 엘리먼트의 문법은 jsx에서 syntax sugar에 지나지 않는다

```jsx
<Parent>
	<A />
</Parent>

//는 다음과 같다
<Parent children={<A />} />
```

두 번째 - children으로 전달되는 엘리먼트는 함수내 생성되는 object일 뿐이다. jsx에서 엘리먼트는 react에서 렌더링할 컴포넌트와 그에 전달할 props object를 

```jsx
```