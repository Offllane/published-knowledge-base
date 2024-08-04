**SOLID** - это принципы разработки программного обеспечения, следуя которым Вы получите хороший код, который в дальнейшем будет хорошо масштабироваться и поддерживаться в рабочем состоянии.

1. Single responsibility principle (принцип единственной ответственности);
2. Open closed principle (принцип открытости-закрытости);
3. Liskov substitution principle (принцип подстановки Барбары Лисков);
4. Interface Segregation principle (принцип разделения интерфейсов);
5. Dependency Inversion principle (принцип инверсии зависимостей).

## Single responsibility principle (Принцип единственной ответственности)
### Определение
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

## Open-closed principle (Принцип открытости/закрытости)
### Определение
Программные сущности (классы, компоненты, модули, функции) должны быть открыты для расширения, но закрыты для изменения.

### Какие проблемы решает этот принцип
Изменять существующий программный код плохо, т.к. он уже протестирован, работает и у нас с ним проблем нет. Если мы изменяем существующий код, то мы должны проводить регрессионное тестирование и проверять, что мы не сломали существующий функционал.

Нужно стараться добавлять новый функционал не за счет изменения новых сущностей, а за счет добавления новой сущности и реализовывать новый функционал уже там.

Естественно этим правилом не получится пользоваться в 100 процентах случаев, т.к. есть багфиксы, исправления логики и т.п. и нам в любом случае придется изменять старый код.

### Как работает принцип
#### Появление проблемы
У нас есть класс Оружие. У него есть три поля: тип, урон, дистанция. 
```ts
class Weapon {  
  type: string;  
  damage: number;  
  range: number;  
  
  constructor(type: string, damage: number, range: number) {  
    this.type = type;  
    this.damage = damage;  
    this.range = range;  
  }}
```

И есть класс Персонаж, который это Оружие может принимать.
```ts
class Character {  
  name: string;  
  weapon: Weapon;  
  
  constructor(name: string, weapon: Weapon) {  
    this.name = name;  
    this.weapon = weapon;  
  }  
  changeWeapon(newWeapon: Weapon): void {  
    this.weapon = newWeapon;  
  }}
```

Создаем оружие Меч с конкретными параметрами дистанции и урона. Создаем персонажа и даем ему Меч.
```ts
const sword = new Weapon('sword', 15, 2);  
  
const character = new Character('Warrior', sword);
```

После чего в классе оружия создаем метод Атака.
```ts
class Weapon {  
  /.../  
  attack(): void {  
    console.log('Удар мечом с уроном ' + this.damage);  
  }}
```

Персонажу мы делегируем метод Атака из класса Оружие, после чего этот метод вызываем уже у персонажа.
```ts
class Character {  
 /.../  
 attack() {  
   this.weapon.attack();  
 }}

 /.../ 
 character.attack();
```

Получаем ожидаемый результат
![[Pasted image 20240804222235.png]]

Теперь нужно добавить новый функционал -- арбалет.
Создадим новое Оружие с типом Арбалет и поменяем оружие Меч на оружие Арбалет. После этого атакуем. 
```ts
/.../ 
const crossbow = new Weapon('crossbow', 40, 70);  

character.changeWeapon(crossbow);
character.attack();
```

Получаем тоже вполне ожидаемый результат: два раза удар мечом с разным уроном.
![[Pasted image 20240804222637.png]]

И вот на данном этапе мы идем в уже протестированный и рабочий код, и изменяем его.
```ts
class Weapon {  
   /.../ 
  attack(): void {  
    switch (this.type) {  
      case 'sword': {  
        console.log('Удар мечом с уроном ' + this.damage);  
        return;  
      }     
      case 'crossbow': {  
        console.log('Выстрел из арбалета с уроном ' + this.damage);  
        return;  
      }    
    }  
  }
}
```

Да, результат мы получаем, но не за счет добавления новой сущности, а за счет изменения уже существующей.

#### Решение проблемы
Вынесем метод attack в интерфейс Attacker, чтобы мы могли его переиспользовать в других классах.
```ts
interface Attacker {  
  attack: () => void;  
}  
  
class Weapon implements Attacker {  
  /.../ 
  attack(): void {}  
}
```
 
Затем мы создаем класс Меч, который наследуется от класса Оружие.
Затем создаем класс Арбалет таким же образом, переопределяя конкретный класс.
```ts
class Sword extends Weapon {  
  override attack() {  
    console.log('Удар мечом с уроном ' + this.damage);  
  }
}  
  
class Crossbow extends Weapon {  
  override attack() {  
    console.log('Выстрел из арбалета с уроном ' + this.damage);  
  }
}
```

Теперь мы можем избавиться от свойства "тип" в классе Оружие.
```ts
class Weapon implements Attacker {  
  // type: string;  
  damage: number;  
  range: number;  
  
  constructor(// type: string,
			   damage: number, range: number) {  
    // this.type = type;  
    this.damage = damage;  
    this.range = range;  
  }  
  attack(): void {}  
}
```

Теперь, чтобы создать новый тип оружия, нам достаточно создать новый класс, унаследовать его от базового класса Оружие, и новый функционал у нас добавлен.
```ts
const sword = new Sword(15, 2);  
const character = new Character('Warrior', sword);  
character.attack();  
  
const crossbow = new Crossbow(40, 70);  
character.changeWeapon(crossbow);  
character.attack();
```

Теперь все работает как надо, на нарушая принцип открытости-закрытости.
![[Pasted image 20240804231900.png]]

Т.е. когда вы продумываете схему ваших сущностей стоит подумать заранее о том, что вам придется добавить новый функционал. И что этот функционал лучше будет добавлять не путем накостыливания дополнительный условий, а путем расширения.

Теперь если мы захотим добавить в приложение Нож, то нам не нужно будет дополнительно тестировать Меч и Арбалет, т.к. мы не вносим никаких изменений в существующий код. Мы добавим Нож и протестируем только его.
```ts
class Knife extends Weapon {  
  override attack() {  
    console.log('Удар ножом с уроном ' + this.damage);  
  }}
```
