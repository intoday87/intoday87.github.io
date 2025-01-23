```js
let promise = Promise.resolve()
promise.then(() => console.log('promise then'))
console.log('end of code')
```
then 함수로 호출되는 경우 micro task queue에 들어가게 되어 비동기로 처리되고 코드가 코드가 종료된 후 호출 된다.
```js
cosnt f = async () => {
	cosnt p = new Promise((resolve) => {
		console.log('in promise')
		resolve()
	})
	before
	
}
```