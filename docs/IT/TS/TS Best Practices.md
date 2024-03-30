# Никогда не дублируй типы
## Mapped Types
```ts
interface Book {  
  price: number;  
  pagesCount: number;  
  author?: string;  
}  
  
// Статья без страниц  
type Article = Omit<Book, 'pagesCount'>;  
  
// Вместо  
  
// interface Article {  
//   price: number;  
//   author?: string;  
// }
```

### Комбинированные типы
```ts
interface Book {  
  price: number;  
  pagesCount: number;  
  author?: string;  
}  
  
type NonAnonymousBook = Pick<Book, 'author'>;  
  
// Промежуточный результат  
// interface NonAnonymousBook {  
//   author?: string;  
// }  
  
type NonAnonymousBook = Required<Pick<Book, 'author'>>;  
// Промежуточный результат  
// interface NonAnonymousBook {  
//   author: string;  
// }  
  
type NonAnonymousBook = Required<Pick<Book, 'author'>> & Omit<Book, 'author'>  
// Финальный результат  
// interface NonAnonymousBook {  
//   price: number;  
//   pagesCount: number;  
//   author: string;  
// }
```


# Не нужно добавлять префикс I в интерфейсах
Префикс добавлять не нужно, т.к. он нарушает принцип инкапсуляции.
```ts
// какая разница, как реализован тип Car?  
function fixCar (car: Car) {  
  ...  
}
```

# Не использовать any и Function типы
Тип Function принимает в себя любое количество аргументов и всегда возвращает any.![[Pasted image 20231002005551.png]]
Всегда необходимо определять тип явно. Если не знаете, что должно вернуться в функции, то укажите в возвращаемом типе unknown.
![[Pasted image 20231002005617.png]]
# ReturnType
Этот тип необходимо использовать тогда, когда одна функция логически обязана использовать результат другой функции.
```ts
const addNumber = (firstNumber: number, secondNumber: number): number => firstNumber + secondNumber;  
  
// logResult принимает ТОЛЬКО результат addNumber, значит имеет смысл связать тип входного параметра  
// с возвращаемым типом addNumber  
const logResult = (result: ReturnType<typeof addNumber>) => console.log(result);  
  
logResult('2'); // ошибка  
logResult(2) // все хорошо  
logResult(addNumber(2, 2)) // все хорошо
```
# Перегрузка
```ts
// Перегружаем по полной  
interface Example {  
  foo(one: number): number;  
  foo(one: number, two: number): number;  
  foo(one: number, two: number, three: number): number;  
}  
  
// Лучше добавить опциональный параметров  
interface Example {  
  foo(one?: number, two?: number, three?: number): number  
}
```

```ts
// Вообще глупо  
interface Example {  
  foo(one: number): number;  
  foo(one: string): number;  
}  
  
// Лучше использовать union типы  
interface Example {  
  foo(one: number | string): number;  
}
```

Перегрузка имеет место быть, если типизация параметров совсем отличается
```ts
interface LayerFactory {  
  createLayer(userContext: UserContext, settings: Settings): Layer;  
  createLayer(layerName: string, style: string): Layer;  
}
```

# Использование readonly
```ts
interface Car {  
  readonly id: string;  
  readonly model: string;  
  readonly year: number;  
}
```
Можно создать закрытый массив с помощью утилиты ReadonlyArray
```ts
const array: ReadonlyArray<{x: number}> = [{x: 1}];  
  
array.push({x: 2}); // ошибка: Property push does not exist on type ReadonlyArray
```

```ts
interface Book {  
  price: number;  
  pagesCount: number;  
  author?: string;  
}  
  
type ReadonlyBook = Readonly<Book>;
```