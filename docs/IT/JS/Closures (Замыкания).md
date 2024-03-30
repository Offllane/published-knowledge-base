Замыкание -- это способность функции в JS хранить в себе ссылку на [[Лексическое окружение (LexicalEnvironment)|лексическое окружение]], в котором она была создана. Это может быть глобальное или локальное [[Лексическое окружение (LexicalEnvironment)|лексическое окружение]].

Пример замыкания
```js
const x = 1;  
  
const logToConsole = function () {  
    console.log(x); // 1  
}  
  
logToConsole();
```

### Как это работает
1. Создается глобальное лексическое окружение:
```js
const x = 1;  
  
const logToConsole = function () {  
//    console.log(x); // 1  
}  
  
// logToConsole();
```
   
```json
// oбъект глобального лексического окружения
{
	link: null,
	variables: {
		x: 1,
		logToConole: 
	}
}
```

2. Создается локальное лексическое окружение (logToConsole):
   ```js
const x = 1;  
  
const logToConsole = function () {  
  console.log(x); // 1  
}  
  
logToConsole();
```

```json
// oбъект локального лексического окружения
{
	link: 'global lexical environment',
	variables: {
		empty
	}
}
```
т.к. в текущем лексическом окружении переменной x нет, то функция ищет эту переменную в родительском лексическом окружении. Там она ее находит. Это и есть замыкание.

>[!note] Функция помнит в каком окружении она была создана и может в любой момент к нему обратиться.

```js
let x = 1;  
  
const logToConsole = function () {  
    console.log(x); // 2  
}  
  
x = 2;  
  
logToConsole();
```
в глобальном лексическом окружении значение x на момент вызова функции равно 2. 
>[!note] Берется самое актуальное значение на момент вызова функции.

```js
let x = 1;  
  
const logToConsole = function () {  
    console.log(x); // 2  
}  
  
x = 2;  
  
logToConsole();  
  
x = 3;
```

```js
function makeCounter(count) {  //создается лекс окружение                                  с переменной count
    return function () {  // создается лекс окружение                                  (переменных нет)
        return count++;   // возвращает значение, а потом                              увеличивает его на 1. В                                   локальном лекс окружении нет                              переменной count, поэтому                                 находит эту                                               переменную в родительском
    }}  
  
let counter = makeCounter(0);  
let counter2 = makeCounter(0);  
  
alert(counter()); // 0
alert(counter()); // 1 
  
alert(counter2()); // 0 
alert(counter2()); // 1
```
![[Pasted image 20230922233320.png]]

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
log(); // Count is 0, т.к. переменная message была инициализирована в момент вызова createIncrement с аргументом 0 и потом не изменялась.
```
Чтобы log вывел 3 (как предполагалось), необходимо внести создание переменной в функцию log.