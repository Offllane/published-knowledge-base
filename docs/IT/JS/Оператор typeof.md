```js
typeof 'str' // 'string'
typeof 0 // 'number'
typeof 12.345 // 'number'
typeof 1n // 'bigInt'
typeof true // 'boolean'
typeof false // 'boolean'
typeof Symbol() // 'symbol'
typeof {} // 'object'
typeof [] // 'object'
typeof null // 'object'
typeof undefined // 'undefined'
typeof function() // 'function'
typeof NaN // 'number'
```

>[!bug] Неправильный возвращаемый тип с помощью typeof
>
```js
typeof null // 'object'
typeof function() // 'function'
typeof NaN // 'number'
```

