## 1.1 Семантика
Семантика нужна не только для скринридеров, но и для лучшей читаемости кода.
Тег это первое, что видит человек при взгляде на html код. Если мы видим div, то нам нужно смотреть на класс, и надеяться, что класс написан верно и правильно. А вот если там будет понятный тег, то мы сразу поймем.

Пример плохого кода: 
```html
<div class="title">Title</div>  
<div class="text">Huge text</div>  
  
<div class="list">  
  <div class="some-sheet-element">  
    Item  
  </div>  
</div>  
  
<div class="date">2001-05-15</div>  
<div class="status">Status</div>
```

Пример хорошего кода:
```html
<h1 class="title">Title</h1> // для заголовков используем h  
<p class="text">Huge text</p> // для больших параграфов текста - p  
  
// для списков ul + li (для списков описаний, то dl + dt + dd)  
<ul class="list">  
  <li class="list-item">  
    Item  
  </li>  
</ul>  
  
// для времени и дат time с временной меткой для скрин-ридеров  
<time class="date" datetime="2001-05-15 19:00">2001-05-15</time>  
<span class="status">Status</span> // для коротких фраз span  
  
// тэги с начертаниями  
<strong class="status status_bold">Bold status</strong>
```
Это не сложно, но сильно упростит читабельность кода.

## 1.2 Гигантизм
Необходимо постоянно задавать себе вопрос: "Не нужно ли будет переиспользовать часть этой разметки где то еще?"
Необходимо держать в голове максимальную длину шаблона. Например 200 строк. Если ваш шаблон больше 200 строк, то с вероятностью 99% нужно декомпозировать компонент.

## 1.3 Бэмология
Добавить про БЭМ

## 1.4 Корректный inputmode
[[Атрибут inputmode]]

## 1.5 Лишний div
Не создавать div, без которого можно обойтись. Задавать себе вопрос " А не лишний ли тег я создал только что?"

Пример плохого кода:
```html
<div class="button-container">  
  <button class="button">Do something</button>  
</div>  
```
```css
.button-container {  
  margin-top: 1rem;  
}  
button {  
  background-color: yellow;  
}  
```
Тут прекрасно можно обойтись без button-container

Пример хорошего кода:
```html
<button class="button">Do something</button>  
```
```css
  button {  
    display: block;  
    margin-top: 1rem;  
    background-color: yellow;  
  }
```
## 1.6 Навождение классов
>[!note] 3 класса на элемент это максимум


Лучше использовать [[1. @mixin and @include|@mixin]].
Пример:
```scss
.reusable-styles {  
  @include ui-button(primary);  
  @include ui-space(top, 4);  
  @include ui-padding(all, 4);  
}
```

## 1.7 Не сокращай
Плохой код:
 ```html
<div *ngIf="condition; else tmp"> I love div</div>  
  
<ng-template #tmp>I hate div</ng-template>
```
Что значит tmp? Template? Temperature? Temporary?

Хороший код:
```html
// template - все еще не самое корректное название,  
// потому что слишком общее.  
// Нужно добавить частое: hateTemplate  
  
<div *ngIf="condition; else hateTemplate"> I love div</div>  
  
<ng-template #hateTemplate>I hate div</ng-template>
```

```html
<ul class="footer__btns"></ul>  
  
// vs  
  
<ul class="footer__buttons"></ul>
```


## 1.8 Порядочность
Всегда необходимо сортировать свойства по степени их важности.
1. Сначала всегда структурная условная директива. (Нам важно знать, при каких условиях элемент показывается)
2. Потом class. Важно знать, как элемент стилизуется.
3. Потом функциональные директивы.
4. События.
5. Связанные параметры.
6. Строковые параметры.
7. Прочие атрибуты

Для примера. Главное единый порядок.

Плохой код:
```html
<input (change)="onChange()"  
       #input  
       class="selection__input"  
       type="text"  
       placeholder="Choose"  
       [value]="value"  
       *ngIf="condition"  
       aria-label="String"  
       formInput  
       (focus)="onFocus()"  
       formControlName="choose"  
       [matAutoComplete]="auto">
```
Хороший код:
```html
<input *ngIf="condition"  
       class="selection__input"  
       formInput  
       #input  
       (change)="onChange()"  
       (focus)="onFocus()"  
       [value]="value"  
       [matAutoComplete]="auto"  
       formControlName="choose"  
       placeholder="Choose"  
       type="text"  
       aria-label="String">
```

## 1.9 Нет логике во view
Лучше вообще не размещать бизнес логику во view.
Причины: 
1. Логические блоки в html ухудшают читабельность. Особенно сложносоставные.
```html
<ul *ngIf="cards?.cardsItems?.lenght && showCardsLinks" class="cards__list"></ul>  
<p *ngIf="cards?.cardsItems?.lenght && showCardsLinks" class="cards__info"></p>

<p *ngIf="!(cards?.cardsItems?.lenght && showCardsLinks)" class="cards__error">
	Карточек нет =(
</p>
```
Как надо:
```html
<ul *ngIf="showCard" class="cards__list"></ul>  
<p *ngIf="showCard" class="cards__info"></p>

<p *ngIf="!showCard" class="cards__error">
	Карточек нет =(
</p>
```
```ts
get showCard(): boolean {
	return cards?.cardsItems?.lenght && showCardsLinks && this.someSheet;
}
```

2. Нелогично писать бизнес логику в шаблоне. Неочевидно, что логику нужно искать в шаблоне.
```html
<p class="count">{{(value / 100} * (37 + coefficient)}</p>
```

3. Логика в шаблоне часто дублируется
```html
<p class="count">{{(value / 100} * (37 + coefficient)}</p>  
<p class="count-2">{{(value / 50} * (37 + coefficient)}</p>  
<p class="count-3">{{(value / 10} * (37 + coefficient)}</p>  
<p class="count-4">{{(value / 1} * (37 + coefficient)}</p>
```

```html
// В каждой кнопке дублируется логика  
<a uiButton [appearance]="category.isActive ? 'primary' : 'secondary'">  
  Новости  
</a>  

<a uiButton [appearance]="category.isActive ? 'primary' : 'secondary'">  
  Материалы  
</a>  

<a uiButton [appearance]="category.isActive ? 'primary' : 'secondary'">  
  Премиум контент  
</a>
```

Как надо:
```html
<a uiButton [appearance]="appearance">Новости</a>  
<a uiButton [appearance]="appearance">Материалы</a>  
<a uiButton [appearance]="appearance">Премиум контент</a>
```
```ts
get appearance(): UiAppearance {  
  return this.category.isActive ? 'primary' : 'secondary'  
}
```
