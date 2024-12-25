# Конечная точка /signoff

Используется для выхода с КСО и закрытия сессии кассира. При проставленном флаге `declare=true` и подключенном ФР также
проверяется и при необходимости закрывается фискальная смена.

#### RSPOS -> POS

```shell
curl -L \
  -X POST \
  -H "Accept: application/json" \
  -H "Authorization: Bearer <BEARER-TOKEN>" \
  -H "X-Request-Id : <REQUEST-ID>"
  -H "Content-Type: application/json" \
  https://pos-api-endpoint/signoff \
  -d '{
    "clientId": "<hostname>"
    "authUuid": "<auth_uuid>",
    "declare" : true
}'
```

| property_name | property_type | description                                                     | mandatory |
|---------------|---------------|-----------------------------------------------------------------|-----------|
| clientId      | string        | Идентификатор клиента, например имя компьютера в локальной сети | +         |
| authUuid      | string        | Уникальный UUID аутентификации, полученный в методе /auth       | +         |
| declare       | bool          | Флаг закрытия фискальной смены (будет снят Z отчет)             | +         |

#### POS -> RSPOS

##### Успешный ответ

```json lines
{
  "transactionId": "5d8a5746-0d1f-1f8c-a583-ec96414bf6d4",
  "result": true
}
```

| property_name | property_type | description                        | mandatory |
|---------------|---------------|------------------------------------|-----------|
| transactionId | string        | Id транзакции в POS системе        | +         |
| result        | bool          | Флаг успешности выполнения запроса | +         |

##### Возможные интерпретируемые RSPOS ошибки

```json lines
{
  "result": false,
  "errorCode": 5001,
  "errorMessage": "Необходима открытая сессия"
}
```

```json lines
{
  "errorCode": 5703,
  "errorMessage": "Другой кассир работает на текущей кассе",
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
Если ошибка не интерпретируется RSPOS, то она будет показана в интерфейсе КСО в том виде как описано в
поле `ErrorMessage`

По окончании печати фискального документа об открытии смены RSPOS отправит в POS информацию о фискальных реквизитах.
Контракт [TODO]
