[advanced react](https://www.advanced-react.com/)에서 다루는 내용을 읽고 이해한 부분을 바탕으로 정리해 본다

저자는 주제에 앞서 다음과 같은 말을 한다

> And I'm not joking or exaggerating about half the time by the way. Doing memoization properly is hard, much harder than it seems. By the end of this chapter, hopefully, you'll agree with me. Here you'll learn:

Source: Makarevich, Nadia. Advanced React: Deep dives, investigations, performance patterns and techniques (p. 79). (Function). Kindle Edition. 

왜 그렇게 말을 하는지 살펴보자
다음과 같은 케이스는 memoizing props라는 anti pattern으로 소개하고 있는데 memoization의 효과가 있을까?

```jsx
export default function Component() {
	const handleCallback = useCallback(() => {
		// handle click...
	}, [/* dependencies... */])
	return <button type="button" onClick={handleClick}>click</button>
}
```

이 케이스는 많은 사람들의 믿음에 근거하고 있고 심지어 chatGPT도 효과가 있다고 설명한다는데 실제로는 효과가 없다.(확인해보니 현재 시점의 chatGPT(무료버전) 기준으로는 효과가 없다고 설명하고 있다)
그 이유는 모두가 알다시피 컴포넌트의 상태가 변경되면 해당 컴포넌트의 하위(down stream) 컴포넌트도 re-render 된다. 그 점에서 `Component`가 re-render 될 때 `button` 컴포넌트는 memoization된 props를 받게된다고 re-render를 멈출 수 있을까? 그렇지는 않다

또 다른 케이스를 보자

```jsx
```