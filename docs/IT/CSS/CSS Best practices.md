## 1.1 Однонаправленнная верстка
Не должно быть одновременно верхних и нижних отступов в верстке. Точно также не должно быть одновременно правых и левых отступов. Это сделано из-за схлопывания маргинов (когда маргины накладываются друг на друга). Можно пользоваться таким принципом: блок не должен волноваться о том, какой отступ будет между ним и следующим блоком. Т.е. маргины должны быть левый и верхний.![[Pasted image 20230728214342.png]]

## 1.2 Инкапсулируй то, что может быть изменено. (Не повторяйся)
Обязательно необходимо использовать переменные для всего, что может быть изменено и впоследствии измениться.
```scss
.header {
	background: var(--ui-color-background);
	height: var(--header-size);

	// модификатор (s | m | lg | xlg) для разных размеров
	border-raduis: var(--ui-radius-m);
}
```

## 1.3 Стандартизация
Стандартизировать все. В идеале использовать ui-kit и не писать больше css код (кроме всяких маргинов и прочего. 
Особенно важно стандартизировать текст. Должно быть около 5-6 типов текстов, которые заданы через [[@mixin and @include|миксины]] или переменные.

## 1.4 Как переиспользовать стили
Пример не очень хорошего кода:
```css
.card {
	padding: 16px;
	border: 1px solid #ccc;
	background-color: pink;
}

.card_black {
	background-color: black;
	color: white;
}
```

Вот какая логика мыслей может быть на примере Angular приложения:
Возможно перед нами один и тот же компонент? 
![[Pasted image 20230728221313.png]]
Если это одна и та же сущность, то необходимо решать проблему темезации. В идеале компонент должен принимать тему и в зависимости от нее адаптировать компонент.
Пример кода с темезацией:
```scss
.card {
	padding: 16px;
	border: 1px solid #ccc;
	background-color: pink;
	
	// Название темы - отдельное искусство
	// Нужно соблюсти баланс между конкретикой и абстракцией
	// black - нельзя назвать, потому что дизайн может стать темно-серым
	// Придется переименовывать
	&_dark {
		background-color: black;
		color: white;
	}
}
```

Если компоненты логически очень схожи, но HTML - разметка разметка очень разная. 
![[Pasted image 20230728223539.png]]Тогда в идеале стоит создать абстрактный компонент, который будут имплементировать компоненты братья. 
```ts
export abstract class CardComponent {} // Абстрактная карточка
```
```ts
export class CoolCardComponent extends CardComponent{}

export class DumbCardComponent extends CardComponent{}
```

Важно выделить общие [[@mixin and @include|миксины]] и положить их на уровне абстрактного компонента.
```scss
@mixin card() {  
  padding: 16px;  
  border: 1px solid #ccc;  
  background-color: pink;  
}
```
Теперь компоненты дети смогут использовать миксины из абстракции.
```scss
// Стили компонента CoolCard  
.cool-card {  
  @include card;  
}
```
```scss
// Стили компонента DumbCard  
.dumb-card {  
  @include card;  
  
  // Переопределяем стили, которые отличаются от общих  
  background-color: black;  
  color: white;  
  
  &__image {  
    width: 72px;  
  }}
```

Чаще всего в проекте уже используется один компонент, а потом появляется другой. В идеале нужно сделать рефактор на вариант с абстрактным компонентом. Но если не получается, то можно считать один компонент базовым.
Нужно выделить в этом компоненте общие стили в миксины и уже в компонентах братьях использовать их.

Если компоненты совсем разные, но переиспользовать стили нужно, то необходимо нужные стили выделить в [[@mixin and @include|миксины]], сложить их на общий уровень и использовать дальше везде, где это необходимо.
```scss
// Общие стили  
@mixin ui-border {  
  border: 1px solid #ccc;  
}  
  
// ... //  
  
.card {  
  @include ui-border;  
  // Другие стили карточки  
}  
  
// ... //  
  
.table {  
  @include ui-border;  
  // Другие стили таблцы  
}
```

## 1.5. z-index
Необходимо соблюдать четкую систему z-indexов. Нужно вынести индексы в переменные и использовать только их. Необходимо договориться о том, какие индексы вообще необходимы. 
Например: 
	z-index: 0-2: для элементов которые по какой то причине наползают на другие элементы в общем потоке. 
	z-index: 3: для затемняющих и блюрящих масок.
	z-index:4: для сайдбаров.
	z-index:5: для модалок.
	z-index:6: для случаев исключений.

```scss
$ui-index-1: 1; // using for elements that should overlap elements in standard flow  
$ui-index-2: 2; // using for elements that should overlap elements in standard flow  
$ui-index-3: 3; // using for blurred masks  
$ui-index-4: 4; // using for sidebar and header  
$ui-index-5: 5; // using for modal  
$ui-index-6: 6; // overlaps everything
```

## 1.6 Медиа запросы
Конфигурация медиазапросов должна также храниться в переменных.
```scss
$ui-media-retina: '(-webkit-min-device-pixel-ratio: 2), (min-resolution: 192dpi)';

$ui-mobile: 'screen and (max-width: 47.9625em)';
$ui-mobile-min: 'screen and (min-width: 22.5em)';
$ui-mobile-interval: '(min-width: 22.5em) and (max-width: 47.9625em)';

$ui-tablet: 'screen and (max-width: 63.9625em)';
$ui-tablet-min: 'screen and (min-width: 48em)';
$ui-tablet-interval: '(min-width: 48em) and (max-width: 63.9625em)';

$ui-desktop: 'screen and (max-width: 79.9625em)';
$ui-desktop-min: 'screen and (min-width: 64em)';
$ui-desktop-interval: '(min-width: 64em) and (max-width: 79.9625em)';

$ui-desktop-lg-min: 'screen and (min-width: 80em)';

$ui-mouse-device: '(hover: hover) and (pointer: fine)';
$ui-touch-device: '(hover: none) and (pointer: coarse)';

@media #{$ui-mouse-device} {
  background: black;
}
```

## 1.7 Про !important
Лучше никогда им не пользоваться. Но если без этого никуда, то обязательно использовать комментарий-оправдание.
```scss
.card {  
  margin-top: 2rem !important; // HACK. Need to redefine fucking bootstrap  
}
```
