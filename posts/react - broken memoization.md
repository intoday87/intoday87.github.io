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

두 번째 - children으로 전달되는 엘리먼트는 object일 뿐이다. jsx에서 엘리먼트는 react에서 렌더링할 컴포넌트와 그에 전달할 props를 담은 [object](https://github.com/facebook/react/blob/4db4b21c63ebc4edc508c5f7674f9df50d8f9744/packages/react/src/jsx/ReactJSXElement.js#L242)일 뿐이다

```jsx
// jsx
function Children() {
	return <div>i'm children</div>
}

function Parent({ children }) {
	return <div>{children}</div>
}

function App() {
	return <Parent><Children /></Parent>
}

// transfiled by babel
import { jsx as _jsx } from "react/jsx-runtime";

function Children() {
	return /*#__PURE__*/_jsx("div", {
		children: "i'm children"
	});
}

function Parent({ children }) {
	return /*#__PURE__*/_jsx("div", {
		children: children
	});
}

function App() {
	return /*#__PURE__*/_jsx(Parent, {
		children: /*#__PURE__*/_jsx(Children, {})
	});
}
```
[babel로 transfile된 결과 보기](https://babeljs.io/repl/#?browsers=defaults%2C%20not%20ie%2011%2C%20not%20ie_mob%2011&build=&builtIns=false&corejs=3.42&spec=false&loose=false&code_lz=GYVwdgxgLglg9mABAYQBYwDYBMBOBTMACgEpEBvAKEUXyhByQB4sYA3APhgHIBbRCdNnxhGAehYcKAXwqhIsBIgAKAQ2FRCZfoNwFEU0pWq16TCezIDMusFLHnps8NHhIAggAcPJclRp46BkRGVXV2RjRrYURRcNFQgih2aSA&forceAllTransforms=false&modules=false&shippedProposals=false&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=react&prettier=false&targets=&version=7.27.7&externalPlugins=&assumptions=%7B%7D)

children으로 전달되는 object의 참조가 다르기 때문에 기대했던 re-render를 멈추기 위해서는 엘리먼트(object)를 memoization 해줘야 한다

```jsx
// app.jsx
import Parent from "./components/Parent";
import A from "./components/A";
import { useState } from "react";

export default function App() {
	const [state, setState] = useState(false);
	const memoA = useMemo(() => <A />, []);
	
	console.log("App component render");
	
	return (
		<>
			<Parent>
				{memoA /* */}
			</Parent>
			<button onClick={() => setState((v) => !v)}>button</button>
		</>
	);
}
```

헷갈릴 수 있으니 transfile된 jsx로 다시 봐보자

```jsx
// before
import { useMemo } from 'react'

function Children() {
	return <div>i'm children</div>
}

function Parent({ children }) {
	return <div>{children}</div>
}

function App() {
	const memoChildren = useMemo(() => <Children />, [])
	return <Parent>{memoChildren}</Parent>
}

// transfiled
import { useMemo } from 'react';

import { jsx as _jsx } from "react/jsx-runtime";

function Children() {
	return /*#__PURE__*/_jsx("div", {
		children: "i'm children"
	});
}

function Parent({ children }) {
	return /*#__PURE__*/_jsx("div", {
		children: children
	});
}

function App() {
	const memoChildren = useMemo(() => /*#__PURE__*/_jsx(Children, {}), []);

	return /*#__PURE__*/_jsx(Parent, {
		children: memoChildren
	});
}
```

이번 케이스는 이전에 다루었던 케이스보다 단순하고 직관적이다. 흔히 하는 실수고 런타임 과정에서 사용자가 사용처의 사용 여부를 추적하지 않으면 발생할 수 있는 문제다. 예시를 보자

```jsx

function Component () {
	const { handleClick } = useHandleClick()

	return <Parent onClick={handleClick}><Children /></Parent>
}
```

별 문제가 없어 보인다 하지만 `Parent`의 내부를 보자

```jsx
import { useEffect } from 'react'

function Parent({ handleClick }) {
	const [toggled, setToggled] = useState(false)
	useEffect(() => {
		handleClick(toggled)
	}, [handleClick, toggled])

	// 나머지 코드..
}
```

여기 까지 봐도 별 문제가 없을수도 있겠다 싶어 보인다. 그럼 `useHandleClick`은 어떻게 되어 있을까?