```js
let promise = Promise.resolve()
promise.then(() => console.log('promise then'))
console.log('end of code')
// end of code
// promise then
```

then 함수로 호출되는 경우 micro task queue에 들어가게 되어 비동기로 처리되고 코드가 코드가 종료된 fifo로 처리된다

다음과 같은 await을 사용하는 syntax sugar도 같은것으로 생각된다
```js
const f = async () => {
	cosnt p = new Promise((resolve) => {
		console.log('in promise')
		resolve()
	})
	console.log('before waiting promise')
	await p
	console.log('after waiting promise')
}
// in promise
// before waiting promise
// after wating promise
```