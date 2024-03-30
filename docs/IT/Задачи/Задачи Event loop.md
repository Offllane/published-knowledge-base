[[Event  Loop (Цикл событий)]]
### Что выведет код?
```js
console.log(1);

setTimeout(() => {
  console.log(2);
}, 0);

Promise.resolve().then(() => {
  console.log(3);
});

console.log(4);
```
>[!faq]- Ответ
>1 4 3 2

```js
log(1);

setTimeout(() => {
  log(2);
  Promise.resolve().then(() => {
    log(3);
  });
}, 0);

setTimeout(() => {
  log(4);
}, 0);

Promise.resolve().then(() => {
  log(5);
});

Promise.resolve().then(() => {
  log(6);
});

log(7);
```
>[!faq]- Ответ
> 1 7 5 6 2 3 4

```js
log(1);

setTimeout(() => {
  log(2);
  Promise.resolve().then(() => {
    log(3);
  });
}, 0);

setTimeout(() => {
  log(4);
}, 0);

Promise.resolve().then(() => {
  log(5);
});

queueMicrotask(() => {
  log(6);
});

log(7);

setTimeout(() => {
  log(8);
  queueMicrotask(() => {
    log(9);
  });
}, 0);
```
>[!faq]- Ответ
> 1 7 5 6 2 3 4 8 9

```js
console.log('start')  
  
setTimeout(() => {  
  console.log('setTimeout')  
})  
  
Promise.resolve().then(() => {  
  console.log('resolve')  
})  
  
console.log('end')
```
>[!faq]- Ответ
>  start end resolve setTimeout

```js
const promise = new Promise((resolve, reject) => {  
  console.log(1);  
  setTimeout(() => {  
    console.log("timerStart");  
    resolve("success");  
    console.log("timerEnd");  
  }, 0);  
  console.log(2);  
});  
  
promise.then((res) => {  
  console.log(res);  
});  
  
console.log(4);
```
>[!faq]- Ответ
>  1 2 4 timerStart timerEnd success

```js
const timer1 = setTimeout(() => {  
  console.log('timer1');  
  
  const promise1 = Promise.resolve().then(() => {  
    console.log('promise1');  
  })
}, 0)  
  
const timer2 = setTimeout(() => {  
  console.log('timer2');  
}, 0)
```
>[!faq]- Ответ
>  timer1 promise1 timer2

```js
console.log('start');  
  
const promise1 = Promise.resolve().then(() => {  
  console.log('promise1');  
  const timer2 = setTimeout(() => {  
    console.log('timer2')  
  }, 0)  
});  
  
const timer1 = setTimeout(() => {  
  console.log('timer1')  
  const promise2 = Promise.resolve().then(() => {  
    console.log('promise2')  
  })}, 0)  
  
console.log('end');
```
>[!faq]- Ответ
>  start end promise1 timer1 promise2 timer2