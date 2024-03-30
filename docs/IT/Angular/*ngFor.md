[[Angular директивы|Директива]] ngFor позволяет перебрать в шаблоне элементы массива (на самом деле можем передать любые итерируемые объекты). Например:
Класс компонента содержит следующий код 
```ts
public customers = [  
  {name: 'Egor', age: 22},  
  {name: 'Pasha', age: 21},  
  {name: 'Vitaliy', age: 23},  
  {name: 'Sasha', age: 35},  
];
```
Чтобы вывести этот массив в шаблоне, можно написать следующий код:
```html
<div *ngFor="let customer of customers">  
  {{customer.name}} - {{customer.age}}  
</div>
```
Результат:
![[Pasted image 20230809234038.png]]
Переменная customer видна только внутри ngFor.

### Как отрабатывает ngFor при изменении данных, по которым директива итерируется?
При изменении массива Angular перерисовывает дерево DOM полностью. Но если использовать функцию `trackBy`, то Angular будет понимать, какой элемент изменился, а затем внесёт изменения в DOM только для этого конкретного элемента.

## Локальные переменные для ngFor
### Index
index -- если нам нужно получить index элементов
```html
<div *ngFor="let customer of customers; let i = index">  
  {{i}} {{customer.name}} - {{customer.age}}  
</div>
```
![[Pasted image 20230809234703.png]]
### Even and odd (четные и нечетные)
Если нам нужно по разному форматировать данные из массива по четным нечетным, то необходимо использовать переменные even  или odd.
```html
<div *ngFor="let customer of customers; let isEven = even"  
     [ngClass]="{ 'even': isEven }"  
>  
  {{customer.name}} - {{customer.age}}  
</div>
```
![[Pasted image 20230809235223.png]]

### First and last
Для первого и последнего элементов используются переменные first и last.
```html
<div *ngFor="let customer of customers; let isFirst = first"  
>  
  <span *ngIf="isFirst">First customer is: </span>  
  {{customer.name}} - {{customer.age}}  
</div>
```
![[Pasted image 20230809235935.png]]

## trackBy
Чтобы избежать этой дорогостоящей операции (переисовки DOM), можно настроить алгоритм отслеживания по умолчанию. предоставив параметр trackBy для ngFor. Тогда будут перерисовываться только новые 
```html
// в шаблоне
<li *ngFor="let item of items; trackBy: trackByFn">{{ item }}</li> 

```
```ts
// в компоненте 
trackByFn(index, item) { return item.id; // unique id corresponding to the item }
```

Использование trackBy — это оптимизация производительности, и обычно она не требуется по умолчанию, в принципе она нужна только при возникновении проблем с производительностью.


