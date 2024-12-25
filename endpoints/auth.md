# Конечная точка /auth

Используется для аутентификации оператора в сценариях 'Открытие КСО' и 'Авторизации действий, требующих повышенных привилегий'.

#### RSPOS -> POS

```shell
curl -L \
  -X POST \
  -H "Accept: application/json" \
  -H "Authorization: Bearer <BEARER-TOKEN>" \
  -H "X-Request-Id : <REQUEST-ID>"
  -H "Content-Type: application/json" \
  https://pos-api-endpoint/auth \
  -d '{
    "login": "<cashier login>",
    "password": "<cashier password>"
}'
```

| property_name | property_type | description    | mandatory |
|---------------|---------------|----------------|-----------|
| login         | string        | Логин кассира  | +         |
| password      | string        | Пароль кассира | +         |


#### POS -> RSPOS

##### Успешный ответ
```json lines
{
  "operatorId": 1,
  "operatorName": "1",
  "firstName": "Галина",
  "lastName": "Иванова",
  "middleName": "Петровна",
  "inn": "000000000000",
  "authUuid": "2a1ce4fe-23e5-47a4-bfe4-09a36ebde1c5",
  "canAuthorize": false,
  "result": true
}
```

| property_name | property_type | description                             | mandatory |
|---------------|---------------|-----------------------------------------|-----------|
| operatorId    | int           | Id кассира                              | +         |
| name          | string        | Логин кассира                           | +         |
| firstName     | string        | Имя кассира                             | +         |
| lastName      | string        | Фамилия кассира                         |           |
| middleName    | string        | Отчество кассира                        |           |
| inn           | string        | Инн кассира                             |           |
| authUuid      | string        | Уникальный UUID аутентификации*         | +         |
| canAuthorize  | bool          | Флаг кассира с повышенными привилегиями | +         |
| result        | bool          | Флаг успешности выполнения запроса      | +         |

*В качестве authUuid (Уникальный UUID аутентификации) может передаваться любой уникальный идентификатор, например UUID кассира.

##### Возможные интерпретируемые RSPOS ошибки

```json lines
{
  "errorCode": 401,
  "errorMessage": "Некорректный логин или пароль",
  "result": false
}
```

```json lines
{
  "errorCode": 400,
  "errorMessage": "Некорректный запрос",
  "result": false
}
```

```json lines
{
  "errorCode": 404,
  "errorMessage": "Оператор не найден",
  "result": false
}
```

```json lines
{
  "errorCode": 500,
  "errorMessage": "Внутренняя ошибка. Обратитесь к администратору",
  "result": false
}
```

ПОС может вернуть любую ошибку. 
Если ошибка не интерпретируется RSPOS, то она будет показана в интерфейсе КСО в том виде как описано в поле `ErrorMessage`