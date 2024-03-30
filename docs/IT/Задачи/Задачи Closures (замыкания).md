Определение [[Closures (Замыкания)|замыканий]]?
Что такое [[Лексическое окружение (LexicalEnvironment)|лексическое окружение]]?
Какие бывают лексические окружения?
Особенности лексических окружений.

Есть ли здесь замыкание?
```js
const x = 1;  
  
const logToConsole = function () {  
  console.log(x); // 1  
}  
  
logToConsole();
```
>[!faq]- Ответ
> Да, есть, т.к. функция logToConsole использует переменную x из внешнего лексического окружения (а это и есть замыкание)

Что выведет код?
```js
let x = 1;  
  
const logToConsole = function () {  
    console.log(x); // что выведет код? 
}  
  
x = 2;  
  
logToConsole();
```
>[!faq]- Ответ
> 2, т.к. на момент вызова функции в глобальном лекс окружении перменная х хранит значение 2. Берется самое актуальное значение на момент вызова функции

Что выведет код?
```js
function makeCounter(count) {
    return function () {
        return count++;
    }}  
  
let counter = makeCounter(0);  
let counter2 = makeCounter(0);  
  
alert(counter());  
alert(counter());  
  
alert(counter2());  
alert(counter2());
```
>[!faq]- Ответ
> 0, 1, 0, 1 (смотри статью)

Что выведет код?
```js
function createIncrement() {  
    let count = 0;  
    function increment() {  
        count++;  
    }
        
    let message = `Count is ${count}`;  
    function log() {  
        console.log(message);  
    }  
    return [increment, log];  
}  
  
const [increment, log] = createIncrement();  
increment();  
increment();  
increment();  
log(); // 
```
>[!faq]- Ответ
> Count is 0, т.к. переменная message была инициализирована в момент вызова createIncrement с аргументом 0 и потом не изменялась.

