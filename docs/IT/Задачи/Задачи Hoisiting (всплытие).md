
Что такое Hoisting одним словом?
Что такое [[Hositing (всплытие, поднятие)]]?
Что делает компилятор при всплытии?

Что выведет код?
```js
console.log (a); // 1.
  
a = 1;  
  
if (a === 1) {  
    console.log(a); // 2.  
}  
  
var a;
```
>[!faq]- Ответ
> 1. undefined, т.к. только всплывает только объявление переменной, а не ее инициализация
> 2. 1

Что выведет код?
```js
console.log (a); // 1.
  
a = 1;  
  
if (a === 1) {  
    console.log(a); // 2.
}  
  
let a;
```
>[!faq]- Ответ
> 1. Uncaught ReferenceError: Cannot access 'a' before initialization
> 2. Сюда не дойдет, т.к. необработанное исключение

Что выведет код?
```js
letsGo();   
function letsGo() {  
    console.log('go!'); // 1.
    console.log(add(5, 3)); // 2.
}  
  
function add(a, b) {  
    return a + b;  
}
```
>[!faq]- Ответ
> 1. go!
> 2. 8

Что выведет код?
```js
console.log(`sumFunc(1, 2) => ${sumFunc(1, 2)}`); // 1. 
console.log(`sumFunc(1, 2) => ${sumConst(3, 4)}`); // 2.
  
const sumConst = (a, b) => a + b;  
  
function sumFunc(a, b) {  
    return a + b;  
}
```
>[!faq]- Ответ
> 1. 3
> 2. Cannot access 'sumConst' before initialization  (т.к. всплыло только объявление функции

