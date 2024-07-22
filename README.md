# shellcore
	Данный проект создан для удобного построения ядра учётных записей для дальнейших сервисов. Его можно использовать как один из микросервисов, не используя логику jwt токенов в других системах, а также для исполнения принципа инкапсуляции в масштабах сервисов.
	Его функционал версия 0.1 - регистрация пользователя, авторизация пользователя, обновление токена, получение данных пользлвателя, проверка авторизации.

<h3>Какие требования для работы программы?</h3>
- Python
- Docker

<h3>Как установить?</h3>
1. Перейти в директорию проекта
2. Сделать первую миграцию базы данных
```bash
docker-compose run shellcore python manage.py migrate
```
3. Создать суперпользователь (по желанию, он нужен для доступа к админ панели django по пути localhost:8000/admin)
```bash
docker-compose run shellcore python manage.py createsuperuser
```
4. Собрать проект
```bash
docker-compose build
```
5. Запустить проект
```bash
docker-compose up
```

<h2>API для пользования микросервиса</h2>
<h4>Примечание! в двойных фигурных скобках динамические данные, либо которые вы должны вписать, либо которые вы получите. Так называемые переменные</h4> 

<h4>Регистрация</h4>
- Запрос - POST
- url - localhost:8000/api/auth/registrating
- body - json
```json
{
	"first_name" : "{{your first name}}",
	"last_name" : "{{your last name}}",
	"username" : "{{your username}}",
	"password" : "{{your password}}",
	"password2" : "{{confirm your password}}"
}
```

<h4>Авторизация</h4>
- Запрос - POST
- url - localhost:8000/api/auth/login
- body - json
```json
{
	"username" : "{{your username}}",
	"password" : "{{your password}}"
}
```
- answer - json
```json
body - {
    "refresh_token": "{{your refresh token}}",
    "access_token": "{{your access token}}"
}
header - {
	"X-CSRFToken" : "{{your csrftoken}}"
}
```

<h4>Примечание! Эти токены следует записывать в cookie</h4> 
<h4>Обновление access токена</h4>
- Запрос - POST
- url - localhost:8000/api/auth/refresh-token
- body - не требуется
- header - обязательные теги
```json
{
	"Cookie" : "access={{your access token}}; csrftoken={{your csrftoken}}; refresh={{your refresh token}}"
}
```
- answer - json
```json
body - {
    "access_token": "{{your access token}}",
    "csrftoken" : "{{your csrftoken}}"
}
header - {
	"X-CSRFToken" : "{{your csrftoken}}"
}
```

<h4>Получить данные пользователя</h4>
- Запрос - GET
- url - localhost:8000/api/auth/user
- body - не требуется
- header - обязательные теги
```json
{
	"Cookie" : "access={{your access token}}; csrftoken={{your csrftoken}}; refresh={{your refresh token}}",
	"Content-Type" : "application/json",
	"Auhorization" : "Bearer {{your access token}}"
}
```
- answer - json
```json
{
    "id": {{your id}},
    "username": "{{your username}}",
    "is_staff": {{your staff status}},
    "first_name": "{{your first name}}",
    "last_name": "{{your last name}}"
}
```

<h4>Проверить актуальность авторизации</h4>
- Запрос - GET
- url - localhost:8000/api/auth/check
- body - не требуется
- header - обязательные теги
```json
{
	"Cookie" : "access={{your access token}}; csrftoken={{your csrftoken}}; refresh={{your refresh token}}",
	"Content-Type" : "application/json",
	"Auhorization" : "Bearer {{your access token}}"
}
```
- answer - если авторизован - text
```
"authorized"
```

