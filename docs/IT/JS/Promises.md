## Что такое промис?
Промис -- это объект обертка для асинхронного кода. Промис может находиться в одном из трёх состояний: вначале pending, затем fullfilled (выполнено успешно) или rejected (выполнено с ошибкой).

Пример промиса
```js
const promise = new Promise(function (resolve, reject) {
  const data = getData() // делаем асинхронную операцию: запрос в БД, API, etc.
  resolve(data) // переводим промис в состояние fulfilled. Результатом выполнения будет объект data
})

const errorPromise = new Promise(function (resolve, reject) {
  reject(new Error('ошибка')) // переводим промис в состояние rejected. Результатом выполнения будет объект Error
})
```
- первый параметр (в примере кода назван `resolve`) — колбэк для перевода промиса в состояние `fulfilled`, при его вызове аргументом передаётся результат операции;
- второй параметр (в примере кода назван `reject`) — колбэк для перевода промиса в состояние `rejected`, при его вызове аргументом передаётся информация об ошибке.
## Для чего промисы нужны?
Промисы придумали, чтобы организовывать асинхронный код последовательно.

Пример когда без промисов
```js
function request(url, onSuccess) {
  /*...*/
}

request('/api/users/1', function (user) {
  request(`/api/photos/${user.id}/`, function (photo) {
    request(`/api/crop/${photo.id}/`, function (response) {
      console.log(response)
    })
  })
})
```

Пример кода с промисами
```js
function request(url) {
  return new Promise(function (resolve, reject) {
    let responseFromServer
    /*...*/
    resolve(responseFromServer)
  })
}

request('/api/users/1')
  .then((user) => request(`/api/photos/${user.id}/`))
  .then((photo) => request(`/api/crop/${photo.id}/`))
  .then((response) => console.log(response))

```


Дополнительным плюсом стала возможность обрабатывать ошибки от цепочки промисов в одном месте — последним `catch`:
```js
request('/api/users/1')
  .then((user) => request(`/api/photos/${user.id}/`))
  .then((photo) => request(`/api/crop/${photo.id}/`))
  .then((response) => console.log(response))
  .catch((error) => console.error(error))
```

## Методы результатов промисов
Существует три метода, которые позволяют работать с результатом выполнения вычисления внутри промиса:
- `then()`
- `catch()`
- `finally()`
```js
fetch(`https://swapi.dev/api/films/${id}/`)
  .then(function (response) {
    // этот then сработает, когда разрешится промис с запросом данных о фильме
    return response.json() // нужно распарсить ответ сервера, это асинхронная операция
  })
  .then(function (movie) {
    // этот then сработает, когда данные о фильме распарсятся
    const characterUrl = movie.characters[0]
    return fetch(characterUrl) // вызов fetch вернет промис, возвращаем его из колбэка, чтобы продолжить цепочку
  })
  .then(function (response) {
    // этот then сработает, когда разрешится промис с результатами запроса персонажа
    return response.json()
  })
  .then(function (character) {
    renderCharacterProfile(character)
  })
  .catch(function (err) {
    // catch сработает, когда любая из операций выше завершится ошибкой
    renderErrorMessage(err)
  })
```

>[!note] `catch()` обрабатывает ошибки от всех `then()` между ним и предыдущим `catch()`

### Promise.all()
Promise.all() -- метод возвращает промис, который выполнится тогда, когда будут выполнены все промисы, переданные в виде перечисляемого аргумента, или отклонен любой из переданных промисов.

`Promise.all` возвращает массив значений от всех промисов, которые были ему переданы (если все промисы успешны). Возвращаемый массив значений сохраняет порядок оригинального перечисляемого объекта, но не порядок выполнения промисов. Если какой-либо элемент перечисляемого объекта не является промисом, то он будет преобразован с помощью метода `Promise.resolve`.

**`Promise.all`** будет немедленно отклонён если один из переданных промисов будет отклонен: если у вас есть четыре промиса которые будут выполнены с задержкой и один, который будет отклонен немедленно - тогда **`Promise.all`** будет немедленно отклонён.
```js
const p1 = Promise.resolve(3);
const p2 = 1337;
const p3 = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, "foo");
});

Promise.all([p1, p2, p3]).then((values) => {
  console.log(values);
});

//Выведет:
// [3, 1337, "foo"]
```

### Promise.allSettled()
Метод **`Promise.allSettled()`** возвращает промис, который исполняется когда все полученные промисы завершены (исполнены или отклонены), содержащий массив результатов исполнения полученных промисов `({status: 'fullfielld' | 'rejected', value: unknown})`.
```js
const promise1 = Promise.resolve(3);
const promise2 = new Promise((resolve, reject) =>
  setTimeout(reject, 100, 'foo'),
);
const promises = [promise1, promise2];

Promise.allSettled(promises).then((results) =>
  results.forEach((result) => console.log(result.status)),
);

// Expected output:
// "fulfilled"
// "rejected"
```
### Promise.any()
`Метод Promise.any()` принимает итерируемый объект содержащий объекты промисов `Promise`. Как только один из промисов `Promise` выполнится успешно (`fulfill`), метод возвратит единственный объект `Promise` со значением выполненного промиса.
Если ни один из промисов не завершится успешно (если все промисы завершатся с ошибкой, т.е. `rejected`), тогда возвращённый объект Promise будет отклонён (`rejected`) с одним из значений: массив содержащий причины ошибки (отклонения), или `AggregateError` — подкласс `Error`, который объединяет выброшенные ошибки вместе.

### Promise.race()
Метод **Promise.race(iterable)** возвращает выполненный или отклонённый промис, в зависимости от того, с каким результатом завершится первый из переданных промисов, со значением или причиной отклонения этого промиса.
```js
const promise1 = new Promise((resolve, reject) => {
  setTimeout(resolve, 500, 'one');
});

const promise2 = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, 'two');
});

Promise.race([promise1, promise2]).then((value) => {
  console.log(value);
  // Both resolve, but promise2 is faster
});
// Expected output: "two"

```
## Асинхронные функции
Асинхронные функции -- это функции, которые возвращают промисы. Такие функции помечаются ключевым словом `async`.
Такие функции ВСЕГДА возвращают промис, даже если мы явно его не возвращаем (в функции нет ретурна)

Примеры создания асинхронных функций
```js
async function request() {}
const req = async () => {}

class SomeClass {
  async request() {}
}
```


## Почитать еще
[[async and await]]
[[Задачи Promises]]

