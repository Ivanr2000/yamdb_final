### Проект Yamdb: ![Build status](https://github.com/Ivanr2000/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)
Backend и api для сбора произведений и отзывов. API позволяет получить всю основную информацию социальной сети. Часть frontend-а отсутствует.

Тестовый проект развернут по адресу http://84.201.139.203/api/v1/ , другие доступные url можно найти в документации ниже.

### Как запустить проект:

Сделать форк репозитория:

```
git clone https://github.com/Ivanr2000/yamdb_final
```

Прописать в настройках репозитория в разделе SECRETS все необходимые переменные в соответствии с файлом yamdb_workflow.yml (после равно приведены примеры или описания того, что должен содержать ключ):
```
DB_ENGINE=django.db.backends.postgresql
DB_NAME=postgres
POSTGRES_USER=<имя пользователя postgress>
POSTGRES_PASSWORD=<пароль postgress>
DB_HOST=db
DB_PORT=<port_number> - порт для базы данных
DOCKER_PASSWORD=<пароль докера>
DOCKER_USERNAME=<пользователь докера>
HOST=<адрес сервера>
PASSPHRASE=<парольная фраза ssh>
SSH_KEY=<ssh ключ сервера>
TELEGRAM_TO=<id чата телеграмм для отправки сообщений>
TELEGRAM_TOKEN=<токен бота телеграмм>
USER=<пользователь на сервере>
```

В файле README скорревктировать бейдж и сделать push на сервер в векту мастер, после этого будет запущено workflow и произойдет запуск проекта.

Подключиться к серверу, сделать миграции, завести пользователей, обновить статику.

```
sudo docker-compose exec web python manage.py migrate
sudo docker-compose exec web python manage.py createsuperuser
sudo docker-compose exec web python manage.py collectstatic --no-input
```

### Основные ендпойнты (urls):
```
http://{url}/api/v1/auth/signup/ - предназначен для самморегистрации пользователей, получения кода подтверждения
http://{url}/api/v1/auth/token/ - предназначен для получения токена, после получения кода подтверждения
http://{url}/api/v1/users/ - предназначен для самостоятельного изменения данных пользователей - на эндпойнте me, или же для работы с пользовотелями администратором - эндпойнты имена пользователей
http://{url}/api/v1/categories/ - предназначен для просмотра категорий пользователями, создания \ изменения категорий произведений - администратором
http://{url}/api/v1/genres/ - предназначен для просмотра жанров пользователями, создания \ изменения жанров произведений - администратором
http://{url}/api/v1/titles/ - предназначен для просмотра произведений пользователями, создания \ изменения произведений - администратором
http://{url}/api/v1/titles/{title_id}/reviews/ - предназначен для просмотра \ создания отзывов пользователями, изменения \ удаления - администратором, модератором, автором
http://{url}/api/v1/titles/{title_id}/reviews/{review_id}/comments/ - предназначен для просмотра \ создания комментариев на отзывы пользователями, изменения \ удаления - администратором, модератором, автором
```
### Примеры обращений к API:

#### Самостоятельно зарегистрироваться и получить код подтвердения для получения токена:

Обратиться по методу Post на - ``` http://{url}/api/v1/auth/signup/ ```
В теле запроса передать параметры username и email и их значения. После этого, если пользователь новыйй произойдет его регистрация, если пользователь уже существует, то регистрация не произойдет, на указанную почту будет выслан код подтверждения:
Пример тела запроса:
```
{
    "username": "test1",
    "email": "test12@yamdb.ru"
}
```
#### Получить токен для авторизации пользователя:

Обратиться по методу Post на - ``` http://{url}/api/v1/auth/token/ ```
В теле запроса передать параметры username и confirmation_code и их значения.
Полученный токен использовать для авторизации в сервисе api по методу bearer.
Пример тела запроса:
```
{
    "username": "test1",
    "confirmation_code": "123456"
}
```
Пример ответа:
```
{
    "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNjUzOTI3ODgxL"
}
```
#### Получить список произведений:

Метод Get - ``` http://{url}/api/v1/titles/ ```

Ответ
```
[
  {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": [
      {
        "id": 0,
        "name": "string",
        "year": 0,
        "rating": 0,
        "description": "string",
        "genre": [
          {
            "name": "string",
            "slug": "string"
          }
        ],
        "category": {
          "name": "string",
          "slug": "string"
        }
      }
    ]
  }
]

```
#### Добавить произведение (доступно только администратору):

Обратиться по методу Post на эндпойнт - ``` http://{url}/api/v1/titles/ ```
В теле запроса передать название произведения, год создания, описание, список жанров, категорию
Тело запроса:
```
{
  "name": "string",
  "year": 0,
  "description": "string",
  "genre": ["string",],
  "category": "string"
}
```
Ответ:
```
{
  "id": 0,
  "name": "string",
  "year": 0,
  "rating": 0,
  "description": "string",
  "genre": [
    {
      "name": "string",
      "slug": "string"
    }
  ],
  "category": {
    "name": "string",
    "slug": "string"
  }
}

```
#### Просмотреть детали по произведению:

Обратиться по методу Get на эндпойнт - ``` http://{url}/api/v1/titles/{titles_id}/ ```
Ответ:
```
{
  "id": 0,
  "name": "string",
  "year": 0,
  "rating": 0,
  "description": "string",
  "genre": [
    {
      "name": "string",
      "slug": "string"
    }
  ],
  "category": {
    "name": "string",
    "slug": "string"
  }
}

```
Администратору на эндпойнте ``` http://{url}/api/v1/titles/{titles_id}/ ``` также доступны методы Patch и Delete

#### Получить список отзывов:

Метод Get - ``` http://{url}/api/v1/titles/{title_id}/reviews/ ```

Ответ:
```
[
  {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": [
      {
        "id": 0,
        "text": "string",
        "author": "string",
        "score": 1,
        "pub_date": "2019-08-24T14:15:22Z"
      }
    ]
  }
]

```
#### Получить отзыв по id:

Метод Get - ``` http://{url}/api/v1/titles/{title_id}/reviews/{review_id}/ ```

Ответ:
```
{
  "id": 0,
  "text": "string",
  "author": "string",
  "score": 1,
  "pub_date": "2019-08-24T14:15:22Z"
}

```
#### Добавить отзыв:

Отправить запрос по методу Post - ``` http://{url}/api/v1/titles/{title_id}/reviews/ ```

Тело запроса:
```
{
  "text": "string",
  "score": 1
}
Ответ:
{
  "id": 0,
  "text": "string",
  "author": "string",
  "score": 1,
  "pub_date": "2019-08-24T14:15:22Z"
}
```
Администратору, модератору и автору на эндпойнте ``` http://{url}/api/v1/titles/{title_id}/reviews/{review_id}/ ``` также доступны методы Patch и Delete