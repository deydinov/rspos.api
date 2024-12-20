# Конечная точка /auth

Используется для аутентификации оператора в сценариях открытия КСО и авторизации действий, требующих повышенных привилегий.

#### RSPOS -> 1C POS

```shell
curl -L \
  -X POST \
  -H "Accept: application/json" \
  -H "Authorization: Bearer <BEARER-TOKEN>" \
  -H "X-Request-Id : <REQUEST-ID>"
  -H "Content-Type: application/json" \
  https://api-endpoint/auth \
  -d '{
    "login": "<cashier login>",            // Логин кассира
    "password": "<cashier password>",      // Пароль кассира
}'
```

#### 1C POS -> RSPOS

##### Успешный ответ
```json lines
{
  "employee_id": "1",               // Id сотрудника
  "firstName": "Галина",            // Имя сотрудника
  "lastName": "Иванова",            // Фамилия сотрудника (опционально)
  "middleName" : "Петровна",        // Отчество сотрудника (опционально)
  "inn": "1234567890",              // ИНН сотрудника (опционально)
  "canAuthorize" : false,           // Флаг сотрудника с повышенными привилегиями (true для кассира с повышенными привилегиями)   
  "result": true
}
```

##### Возможные ошибки

```json lines
{
    "result": false,
    "errorCode": 401,
    "errorMessage": "Некорректный логин или пароль"
}
```

```json lines
{
  "result": false,
  "errorCode": 400,
  "errorMessage": "Некорректный запрос"
}
```

```json lines
{
    "result": false,
    "errorCode": 404,
    "errorMessage": "Оператор не найден"
}
```

```json lines
{
    "result": false,
    "errorCode": 500,
    "errorMessage": "Внутренняя ошибка. Обратитесь к администратору"
}
```
