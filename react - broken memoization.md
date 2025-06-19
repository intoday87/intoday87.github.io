[advanced react](https://www.advanced-react.com/)에서 다루는 내용을 읽고 이해한 부분을 바탕으로 정리해 본다

저자는 주제에 앞서 다음과 같은 말을 했던것으로 기억한다.(정확한 워딩은 아니다. 기억나는대로 적을 뿐)

> react에서 memoization을 실제로 의도한대로 하는 것이 절대 쉽지 않다. 당장은 이 말에 공감하기는 힘들수 있을것같다. 하지만 이 주제를 다루고 나서 독자들도 공감하게 될 것이라 생각한다.

왜 그렇게 말을 하는지 살펴보자.
다음과 같은 케이스는 memoization의 효과가 있을까?

```jsx
export default function Component() {
	const handleCallback = useCallback(() => {
		// handle click...
	})
	return <button type="button" onClick={handleClick}>click</button>
}
```