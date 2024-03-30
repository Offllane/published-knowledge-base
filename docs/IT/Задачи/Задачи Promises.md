[[Promises]]
### Что такое Promise?
>[!faq]- Ответ
> Промис это объект обертка для работы с асинхронным кодом. Этот объект может находиться в одном из трех состояний: fullfilled, pending, rejected. Объект принимает в качестве параметра функцию-executor, которая в аргументах имеет два коллбэка resolve и reject.
### Для чего нужны промисы?
>[!faq]- Ответ
>Промисы придумали, чтобы организовывать асинхронный код последовательно. (Избавились от коллбэк хэлл)
### Назовите методы для работы с результатом выполнения промиса
>[!faq]- Ответ
>- `then()`
>-  `catch()`
>- `finally()`

###  Назовите методы промисов
>[!faq]- Ответ
>`Promise.all()`
>`Promise.allSettled()`
>`Promise.race()`
`Promise.any()`

### Что выведет код?
``` js
console.log('start');

const promise1 = new Promise((resolve, reject) => {  
  console.log(1)
})

console.log('end');
```
>[!faq]- Ответ
>start 1 end

```js
console.log('start');  
  
const promise1 = new Promise((resolve, reject) => {  
  console.log(1)  
  resolve(2)  
})  
  
promise1.then(res => {  
  console.log(res)  
})  
  
console.log('end');
```
>[!faq]- Ответ
>start 1 end 2

```js
console.log('start');  
  
const promise1 = new Promise((resolve, reject) => {  
  console.log(1)  
  resolve(2)  
  console.log(3)  
})  
  
promise1.then(res => {  
  console.log(res)  
})  
  
console.log('end');
```
>[!faq]- Ответ
>start 1 3 end 2

```js
console.log('start');  
  
const promise1 = new Promise((resolve, reject) => {  
  console.log(1)  
})  
  
promise1.then(res => {  
  console.log(2)  
})  
  
console.log('end');
```
>[!faq]- Ответ
>start 1 end

```js
console.log('start')  
  
const fn = () => (new Promise((resolve, reject) => {  
  console.log(1);  
  resolve('success')  
}))  
  
console.log('middle')  
  
fn().then(res => {  
  console.log(res)  
})  
  
console.log('end')
```
>[!faq]- Ответ
>start middle 1 end success

```js
console.log('start')  
  
Promise.resolve(1).then((res) => {  
  console.log(res)  
})  
  
Promise.resolve(2).then((res) => {  
  console.log(res)  
})  
  
console.log('end')
```
>[!faq]- Ответ
>start end 1 2


## Реализуйте полифилл для Promise.all
```js
function myPromiseAll(taskList: Array<Promise<unknown>>): Promise<Array<unknown>> {  
  const results: Array<unknown> = [];  
  let competedPromisesQuantity = 0;  
  
  return new Promise<Array<unknown>>((resolve, reject) => {  
    taskList.forEach((promise: Promise<unknown>, index: number) => {  
      promise.then((value: unknown) => {  
        results[index] = value;  
        competedPromisesQuantity++;  
  
        if (competedPromisesQuantity === taskList.length) {  
          resolve(results);  
        }      
      })        
        .catch(error => {  
          reject(error);  
        });    
    });  
  });
}

// tests
function task(time: number) {  
  return new Promise((resolve) => {  
    setTimeout(resolve, time, time);  
  })}  
  
function rejectedTask() {  
  return new Promise((resolve, reject) => {  
    setTimeout(reject, 3000, 'error');  
  })}

const taskList1 = [task(1000), task(5000), task(3000)];  
const taskList2 = [task(1000), task(5000), rejectedTask()];  

myPromiseAll(taskList1).then(result => console.log(result)); // [1000, 5000, 3000]
Promise.all(taskList1).then(result => console.log(result)); // [1000, 5000, 3000]
  
myPromiseAll(taskList2).then(result => console.log(result)).catch(err => console.log(err));  // error
Promise.all(taskList2).then(result => console.log(result)).catch(err => console.log(err)); // error
```

## Реализуйте полифилл для Promise.allSettled
### Первый вариант
```js
function myPromiseAllSettled(taskList: Array<Promise<unknown>>): Promise<Array<unknown>> {  
  const result: Array<unknown> = [];  
  let finishedPromisesQuantity = 0;  
  
  return new Promise<Array<unknown>>(resolve => {  
    taskList.forEach((promise: Promise<unknown>, index: number) => {  
      promise  
        .then((value: unknown) => {  
          result[index] = { status: 'fullfilled', value: value };  
        })        
        .catch(error => {  
          result[index] = { status: 'rejected', value: error };  
        })        
        .finally(() => {  
          finishedPromisesQuantity++;  
          if (finishedPromisesQuantity === taskList.length) {  
            resolve(result);  
          }        
        });   
      }); 
  });
}

// tests
const taskList1 = [task(1000), task(5000), task(3000)];  
const taskList2 = [task(1000), task(5000), rejectedTask()]; 

// [{status: fullfilled, value: 1000}, ...]
myPromiseAllSettled(taskList1).then(result => console.log(result));  
Promise.allSettled(taskList1).then(result => console.log(result));  
  
myPromiseAllSettled(taskList2).then(result => console.log(result)).catch(err => console.log(err));  
Promise.allSettled(taskList2).then(result => console.log(result)).catch(err => console.log(err));
```
### Второй вариант
```js
function myPromiseAllSettled(taskList: Array<Promise<unknown>>): Promise<Array<unknown>> {  
  let finishedPromises = taskList.map(promise => {  
    return promise  
      .then(value => {  
        return { status: 'fullfilled', value: value };  
      })      
      .catch(error => {  
        return { status: 'rejected', error: error }  
      });  
  });  
  return Promise.all(finishedPromises);  
}
```
## Реализуйте полифилл для Promise.any
```js
function myPromiseAny(taskList: Array<Promise<unknown>>): Promise<unknown | AggregateError>  {  
  let erroredPromisesQuantity = 0;  
  return new Promise((resolve, reject) => {  
    taskList.forEach(promise => {  
      promise  
        .then(value => {  
          resolve(value);  
        })        
        .catch(() => {  
          erroredPromisesQuantity++;  
          if (erroredPromisesQuantity === taskList.length) {  
            reject('AggregateError: All promises were rejected');  
          }        
        })    
	});  
  });
}

// tests
const taskList1 = [task(1000), task(5000), rejectedTask()];  
const taskList2 = [rejectedTask(), rejectedTask(), rejectedTask()];  
  
myPromiseAny(taskList1).then(result => console.log(result));  // 1000
Promise.any(taskList1).then(result => console.log(result));  // 1000
  
myPromiseAny(taskList2).then(result => console.log(result)).catch(err => console.log(err)); // AggregateError: All promises were rejected
Promise.any(taskList2).then(result => console.log(result)).catch(err => console.log(err)); // AggregateError: All promises were rejected
```