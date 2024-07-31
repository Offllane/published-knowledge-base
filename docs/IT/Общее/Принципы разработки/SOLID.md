**SOLID** - это принципы разработки программного обеспечения, следуя которым Вы получите хороший код, который в дальнейшем будет хорошо масштабироваться и поддерживаться в рабочем состоянии.

1. Single responsibility principle (принцип единственной ответственности);
2. Open closed principle (принцип открытости-закрытости);
3. Liskov substitution principle (принцип подстановки Барбары Лисков);
4. Interface Segregation principle (принцип разделения интерфейсов);
5. Dependency Inversion principle (принцип инверсии зависимостей).

## Single responsibility principle (Solid)
Один класс должен решать только одну задачу.
У одного класса единая зона ответственности.

У нас не должно быть классов, компонентов, которые и логируют, и что то записывают, и отправляют, и выводят и т.д.
### Какие проблемы решает принцип
1. Когда у нас одна сущность решает много задач, код разрастается и становится сильно связанным. Меняешь что то одно, ломается во многих местах.
2. Если класс содержит пару тысяч строк, то читать код такого класса становится очень сложно.
3. Очень сложно вносить изменения в большие классы.
4. Сложность работы в контроле версий (если много разработчиков будут править один файл, то это будет приводить к конфликтам и т.п.)

### Как работает принцип
#### Появление проблемы
У нас есть какой то класс, в котором есть пару полей и пару методов. Пока все в порядке.
```ts
interface Example {  
  field1: unknown;  
  field2: unknown;  
  
  getField1(): unknown;  
  getField2(): unknown;  
}
```

Приходит заказчик и просит добавить возможность сохранять сущность в базу данных и отправлять ее куда то (неважно куда). 
Мы добавляем эти методы в этот же класс. Все находится близко, все удобно. Пока проблемы вроде бы и нет.
```ts
interface Example {  
  field1: unknown;  
  field2: unknown;  
  
  getField1(): unknown;  
  getField2(): unknown;  
  save(): void;  
  send(): void;  
}
```

Потом опять приходит заказчик и говорит, что необходимо эту сущность уметь печатать и логировать данные в файл. Мы добавляем и эти методы прямо в класс.
```ts
interface Example {  
  field1: unknown;  
  field2: unknown;  
  
  getField1(): unknown;  
  getField2(): unknown;  
  save(): void;  
  send(): void;  
  print(): void;  
  log(): void;  
}
```

В итоге у нас есть объект, который может самоотправляться, самосохраняться, самопечататься, самологироваться и т.д. 
И чем больше будет требований, тем более запутанным будет становиться класс. 

#### Решение проблемы
Необходимо разделить класс на сущности, каждая из которых будет отвечать за свою область.
Сначала вынесем методы работы с базой данных.
```ts
interface Example {  
  field1: unknown;  
  field2: unknown;  
  
  getField1(): unknown;  
  getField2(): unknown;  
  // save(): void;  
  send(): void;  
  print(): void;  
  log(): void;  
}  
  
  
interface Repository {  
  get(obj: Example): Example;  // доп метод
  save(obj: Example): void;  
  remove(obj: Example): void;  // доп метод
}
```

Далее вынесем отправку в отдельную сущность Sender. И если заказчику будет необходимо добавить еще какие то методы отправки, то они будут реализованы уже именно в этой сущности. Изначальный класс нам трогать вообще не придется.
```ts
interface Example {  
  field1: unknown;  
  field2: unknown;  
  
  getField1(): unknown;  
  getField2(): unknown;  
  // send(): void;  
  print(): void;  
  log(): void;  
}  
  
  
interface Repository {  
  get(obj: Example): Example;  
  save(obj: Example): void;  
  remove(obj: Example): void;  
}  
  
interface Sender {  
  sendHttp(obj: Example): void;  
  sendWS(obj: Example): void;  // доп метод
}
```

Двигаясь дальше, мы создаем еще два класса: логгер и принтер:
```ts
interface Example {  
  field1: unknown;  
  field2: unknown;  
  getField1(): unknown;  
  getField2(): unknown;  
}  
  
  
interface Repository {  
  get(obj: Example): Example;  
  save(obj: Example): void;  
  remove(obj: Example): void;  
}  
  
interface Sender {  
  sendHttp(obj: Example): void;  
  sendWS(obj: Example): void;  
}  
  
interface Logger {  
  logToExcel(obj: Example): void;  
  logToTxt(obj: Example): void;  
}  
  
interface Printer {  
  printToPdf(obj: Example): void;  
  printToWord(obj: Example): void;  
}
```

И теперь если нам прилетает задача "Поправить логгирование", "Поправить печать", то нам не придется часами изучать огромный класс. Мы сразу пойдем в конкретную сущность логгер или принтер, который будет решать одну конкретную задачу. При этом эти задачи можно будет доверить двум разным разработчикам, не боясь конфликтов в VCS.

Также мы разделили модель данных, т.е. объект, который содержит состояние и поведение, где каждый класс отвечает за какую то конкретную операцию.

![[Pasted image 20240731121549.png]]

