# Описание API

### Краткое описание 

Я буду рассматривать почти REST API сервис на примере ETH кошельков пользователей. Один юзер имеет один кошелек, поэтому будет иметь user_id и eth_amount. Тогда будут запросы: получить информацию о пользователе, изменить количество криптовалюты у юзера, удалить кошелек юзера, добавить новый кошелек для юзера и получить все кошельки юзеров.

### Представление данных

| Data type | Description | Variable |
| ------ | ------ | ------ |
| int |ID | user_id |
|string| имя пользователя | user_name|
| float | Количество криптовалюты | eth_amount |

Здесь ID у всех юзеров будут уникальными. 

### Пример кошелька юзера

```json
{
  "user_wallet": {
    "user_id": 12,
    "user_name": "user_1"
    "eth_amount": 0.451
  },
  "self" : {"link": "/user_wallet/{user_id}"}
}
```

### Описание возможных запросов

| Query type | URI | Description |
| ------ | ------ | ------ |
|  PUT | /user_wallet/<user_id> | Изменить количество криптовалюты |
|  GET | /user_wallet/<user_id> | Получить информацию о количестве криптовалюты в кошельке |
|  DELETE | /user_wallet/<user_id> | Удалить криптовалюту |
|  POST | /user_wallets | Добавить новый кошелек. Ожидается, что будут передан eth_amount и user_name |

# Описание API и каждого возможного запроса отдельно 

Ко всем ответам по запросу кроме возвращаемого значения, также будут передаваться линк с user_id
```json
{
    "user_wallet": { 
        "user_id": 12,
        "user_name": "user_1",
        "eth_amount": 0.451
    },
    "self" : {"link": "/user_wallet/{user_id}"}
}
```

### 1./user_wallet/{user_id} запросы

#### 1.a) Получить информацию о количестве криптовалюты в кошельке юзера (GET)

Результатом будет являться информация о криптовалюте в формате JSON с заданным ID. Если ID не существует или задан некорректно, будут выдаваться соответствующие сообщения.

| Value | Code | Description |
| ------ | ------ | ------ |
|  OK | 200 | Корректный запрос, в результате вернется JSON с описанием криптовалюты с заданным ID |
|  Bad request | 400 | Введен некорректный запрос |
|  Not found | 404 | Нет кошелька, у которого заданный user ID |

#### 1.b) Удалить кошелек юзера(DELETE)

Если кошелек с таким user ID существует, он будет удален. Если такого не было или задан некорректный формат для ID, то будет возвращена соответствующая ошибка.

| Value | Code | Description |
| ------ | ------ | ------ |
|  OK | 200 | Корректный запрос, в результате будет удалена криптовалюта с заданным ID |
|  Bad request | 400 | Введен некорректный запрос |
|  Not found | 404 | Нет кошелька, у которого заданный user ID, ничего не будет удалено |

#### 1.c) Изменить количество криптовалюты в кошельке юзера (PUT)

Если кошелек с таким user ID существует и корректно задано поле нового количества (eth_amount), то количество криптовалюты в кошельке с заданным ID будет изменено. Если некорректно заданы параметры или кошелька с таким user ID не существует, будут возвращены соответствующие ошибки. 

В запросе необходимо передать JSON с параметром <eth_amount> - новое количество криптовалюты с заданным user ID. Например,

```json
{
    "eth_amount": ["New amount as <float>"]
}
```

| Value | Code | Description |
| ------ | ------ | ------ |
|  OK | 200 | Корректный запрос, у кошелька с заданным ID было изменено поле отвечающее за количество, будет возвращен JSON кошелька с измененным количеством|
|  Bad request | 400 | Некорректный запрос или список передаваемых параметров |
|  Not found | 404 | Нет кошелька, у которого заданный user ID |

### 2. /user_wallets запросы

#### Добавить новый кошелек (POST)

В результате запроса в базу будет добавлен новую кошелек, всю информацию про которому нужно передать в JSON. Если поля eth_amount и user_name не будут заданы или заданы некорректно, то новый кошелек не будет добавлен.

Если запрос удалось выполнить, возвращается JSON, который будет описывать новую криптовалюту в кошельке. 

| Value | Code | Description |
| ------ | ------ | ------ |
|  OK | 200 | Корректный запрос, будет возвращен JSON, который будет описывать новый кошелек |
|  Bad request | 400 | Некорректный запрос или список передаваемых параметров |

