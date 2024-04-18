### Что выведет код?
```js
const map = new Map();  
  
for (let i = 0; i < 5; i++) {  
  const key = {  
    index: i  
  }  
  map.set(key, i);  
}  
  
console.log(map.has({ index: 0 }));
```
>[!faq]- Ответ
>false, т.к. у объектов разные ссылки. Смотри [[Хранение переменных по ссылке и по значению]]
>

### Что выведет код?
```js
let map = new Map(); 

map.set("name", "John");
let keys = map.keys();

keys.push("more");

console.log(map.keys());
```
>[!faq]- Ответ
> Error: keys.push is not a function
> Т.к. map.keys() возвращает не массив, а итериуемый объект. Чтобы все заработало можно использовать Array.from();

