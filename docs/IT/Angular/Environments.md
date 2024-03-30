Environment это объект, в котором содержатся необходимые данные для билда и разработки приложения.

```ts
export const environment = {  
  production: false,  
  hostname: '192.168.100.13:8080'  
};
```

Теперь мы можем использовать для запросов {{environment.hostname}}, которые будут отличаться в зависимости от конфигурации.

```ts
this.http.get<Array<Project>>({{environment.hostname}} + '/api/v1/sbml-core/projects');
```

### Создание environment
1. В папке /src/environmtns создаем новый файл environment.name.ts
2. Экспортируем объект, со всеми необходимыми данными.
3. В файле angular.json находим "architect" -> "build" -> "configurations".
4. Во нужной конфигурации (если нужной нет, создаем новую), указываем свойство "fileReplacements"
```json
"yourConfig": {  
  "fileReplacements": [  
    {      
	  "replace": "src/environments/environment.ts",  
      "with": "src/environments/environment.name.ts"  
    }  
  ]
},
```
5. При запуске билда необходимо указать конфигурацию
   ```ts
   ng build --configuration=yourConfig
```
6. Для того, чтобы указать какой environment использовать для команды ng serve перейти
   "architect" -> "build" -> "configurations". 
7. Для нужной конфигурации добавить свойство "browserTarget"
   ```json
   "browserTarget": "sbml-frontend-adapter:build:yourConfig",
```
	Эта строчка означает, что при запуске проекта через ng serve будет использована конфигурация для билда yourConfig



