[Документация](https://sass-scss.ru/documentation/miksini/)

## Определение миксина
Миксины (часто используется название **примеси**) позволяют определить стили, которые могут быть использованы повторно в любом месте документа без необходимости прибегать к несемантическим классам вроде **.float-left**.

Миксины объявляются директивой `@mixin`. После неё должно стоять имя миксина и, опционально, его параметры, и блок, содержащий тело миксина. Например, можно определить миксин large-text следующим образом:
```scss
@mixin large-text {
  font: {
    family: Arial;
    size: 20px;
    weight: bold;
  }
  color: #ff0000;
}
```

Миксины могут также содержать селекторы, возможно со свойствами. Селекторы даже могут содержать [ссылки на родителя](https://sass-scss.ru/documentation/rasshirenie_css/ssilka_na_roditelya_selektora/). Например:
```scss
@mixin clearfix {
  display: inline-block;

  &:after {
    content: ".";
    display: block;
    height: 0;
    clear: both;
    visibility: hidden;
  }
  * html & { 
    height: 1px 
  }
}
```

## Использование миксина
Миксины вызываются в документ директивой @include. Она принимает имя миксина и, опционально, передаваемые в него аргументы, включает стили, определённые этим миксином, в текущее правило. Например:
```scss
page-title {
  @include large-text;
  padding: 4px;
  margin-top: 10px;
}
```

Миксины могут быть также вызваны вне какого-либо правила (то есть в корне документа), при условии, что они не определяют непосредственно правил и не используют ссылку на родителя. Например:
```scss
@mixin silly-links {
  a {
    color: blue;
    background-color: red;
  }
}

@include silly-links;
```
Скомпилируется в:
```css
a {
  color: blue;
  background-color: red; 
}
```

В миксины можно добавлять аргументы:
```scss
@mixin sexy-border($color, $width) {
  border: {
    color: $color;
    width: $width;
    style: dashed;
  }
}

p { 
  @include sexy-border(blue, 1in);
}
```
Скомпилируется в:
```css
p {
  border-color: blue;
  border-width: 1in;
  border-style: dashed; 
}
```

### Аргументы по умолчанию
```scss
@mixin sexy-border($color, $width: 1in) {
  border: {
    color: $color;
    width: $width;
    style: dashed;
  }
}
p { 
  @include sexy-border(blue); 
}
h1 { 
  @include sexy-border(blue, 2in); 
}
```
Скомпилируется в:
```css
p {
  border-color: blue;
  border-width: 1in;
  border-style: dashed; 
}

h1 {
  border-color: blue;
  border-width: 2in;
  border-style: dashed; 
}
```

Миксины также можно вызывать используя явно именованные аргументы. Например, вышеуказанный пример мог бы быть записан как:
```scss
p {
  @include sexy-border($color: blue); 
}

h1 {
  @include sexy-border($color: blue, $width: 2in); 
}
```
Данный способ записи делает код менее кратким, но более читабельным. Также он позволяет функциям представлять более гибкие интерфейсы: большое количество параметров у функции не усложняет её вызов.

#### Переменное количество переменных в аргументе миксина
```scss
@mixin box-shadow($shadows...) {
  -moz-box-shadow: $shadows;
  -webkit-box-shadow: $shadows;
  box-shadow: $shadows;
}

.shadows {
  @include box-shadow(0px 4px 5px #666, 2px 6px 10px #999);
}
```
Скомпилируется в:
```css
.shadows {
  -moz-box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
  -webkit-box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
  box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
}
```

