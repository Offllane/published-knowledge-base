Проксирование подменяет localhos:port, на адрес сервера. Это нужно для того, чтобы при разработке браузер не ругался на CORS errors. 
Т.к. бэкенд не может постоянно добавлять в allow-origin заголовок для каждого разработчика, то нужно добавлять проксирование, чтобы браузер думал, что все запросы с localhost летят на localhost.

Например http://192.168.100.13:8080 с проксированием заменится на localhost:4200, и браузер не выдаст CORS error.

>[!warning] Только при разработке (для ng serve)
>Проксирование работает только при разработке. При деплое проксирование работать не будет. Запросы будут отправляться по тому же адресу, где будет опубликован frontend. Если фронтенд и бэкенд расположены на одном домене -- все отлично. Если нет, то необходимо использовать environment

## Создание proxy
1. В папке /src создаем файл proxy.name.conf.json
```json
   {  
  "/api/*": {  // указывает какие запросы должны быть проксированы
    "target": "http://192.168.100.13:8080", // на что будет заменен localhost 
    "secure": false,  
    "changeOrigin": true, // если домен отличается от того, на котором мы располагаемся, то true 
    "logLevel": "debug" 
  }  
}
```
2. В файле angular.json находим "architect" -> "serve" -> "configurations".
3. Создаем новую конфигурацию и добавляем строчку (либо добавляем в уже имеющуюся). 
   "proxyConfig": "src/proxy.name.conf.json" (тот файл, который мы создали в первом пункте)
   
   Пример:
```json
"serve": {  
  "configurations": {  
    "production": {  
      "browserTarget": "sbml-frontend-adapter:build:production"  
    },  
    "development": {  
      "browserTarget": "sbml-frontend-adapter:build:development",  
      "proxyConfig": "src/proxy.development.conf.json"  
    },  
    "localNetwork": {  
      "browserTarget": "sbml-frontend-adapter:build:localNetwork",  
      "proxyConfig": "src/proxy.local-network.conf.json"  
    }  
  },  "defaultConfiguration": "development"  
}
```
5. При запуске проекта необходимо указать конфигурацию.
   Например: 
```js
ng serve --configuration=localNetwork
```

В подготовке билда проксирование не используется. Необходимо указывать адрес сервера напрямую в запросе. Если серверов несколько (например тестовый и прод), то эту проблему поможет решить создание различных [[Environments]].

>[!note] Подмена ссылки
>Если в запросе не указана явно ссылка, то англуар подставит в начало текущий домен.
>Т.е. '/api/v1/sbml-core/projects' заменится на domen/api/v1/sbml-core/projects. Если ссылка будет явно указана, то подмены не будет
>http://192.168.100.13:8080/api/v1/sbml-core/projects
>
