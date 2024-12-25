# Конечная точка /posinfo

Используется для инициализации RSPOS.

#### RSPOS -> POS

```shell
curl -L \
  -X POST \
  -H "Accept: application/json" \
  -H "Authorization: Bearer <BEARER-TOKEN>" \
  -H "X-Request-Id : <REQUEST-ID>"
  -H "Content-Type: application/json" \
  https://pos-api-endpoint/posinfo \
  -d '{
    "clientId": "<hostname>"
}'
```

| property_name | property_type | description                                                     | mandatory |
|---------------|---------------|-----------------------------------------------------------------|-----------|
| clientId      | string        | Идентификатор клиента, например имя компьютера в локальной сети | +         |

#### POS -> RSPOS

##### Успешный ответ

```json lines
{
  "storeId": 35,
  "workstationId": 5,
  "clientId": "SSCO",
  "rolloverTaNumber": 9999,
  "timezone": "Europe/Moscow",
  "activeLanguage": "ru-RU",
  "supportedLanguages": [
    "ru-RU",
    "en-GB"
  ],
  "isOpened": false,
  "operatorInfo": {
    "operatorId": 1,
    "operatorName": "1",
    "firstName": "Галина",
    "lastName": "Иванова",
    "middleName": "Петровна",
    "inn": "000000000000",
    "authUuid": "2a1ce4fe-23e5-17a4-bfe4-09a36ebde1c5",
    "canAuthorize": false
  },
  "result": true
}
```

| property_name            | property_type | description                                   | mandatory |
|--------------------------|---------------|-----------------------------------------------|-----------|
| storeId                  | int           | Номер магазина                                | +         |
| workstationId            | int           | Номер кассы                                   | +         |
| clientId                 | string        | Идентификатор клиента                         | +         |
| rolloverTaNumber         | int           | Максимальный номер счетчика транзакции*       |           |
| timezone                 | string        | Часовой пояс магазина                         | +         |
| activeLanguage           | string        | ISO код языка магазина по умолчанию           | +         |
| supportedLanguages       | string[]      | Список поддерживаемых ISO кодов языков        | +         |
| isOpened                 | bool          | Флаг, указывающий что смена на кассе открыта  | +         |
| **operatorInfo**         | **object**    | **Информация о кассире (если isOpened=true)** |           |
| &nbsp;&nbsp;operatorId   | int           | Id кассира                                    | +         |
| &nbsp;&nbsp;operatorName | string        | Логин кассира                                 | +         |
| &nbsp;&nbsp;firstName    | string        | Имя кассира                                   | +         |
| &nbsp;&nbsp;lastName     | string        | Фамилия кассира                               |           |
| &nbsp;&nbsp;middleName   | string        | Отчество кассира                              |           |
| &nbsp;&nbsp;inn          | string        | Инн кассира                                   |           |
| &nbsp;&nbsp;authUuid     | string        | Уникальный UUID аутентификации                | +         |
| &nbsp;&nbsp;canAuthorize | bool          | Флаг кассира с повышенными привилегиями       | +         |
| result                   | bool          | Флаг успешности выполнения запроса            | +         |

*Максимальный счетчик номера транзакции - это внутренний счетчик транзакций, который ведется на каждой кассе.
По достижению максимального номера счетчик сбрасывается на 1. Если не передать будет использоваться значение по
умолчанию 9999

##### Возможные интерпретируемые RSPOS ошибки

```json lines
{
  "errorCode": 404,
  "errorMessage": "Клиент не найден",
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
  "errorCode": 500,
  "errorMessage": "Внутренняя ошибка. Обратитесь к администратору",
  "result": false
}
```

ПОС может вернуть любую ошибку.
Если ошибка не интерпретируется RSPOS, то она будет показана в интерфейсе КСО в том виде как описано в
поле `ErrorMessage`