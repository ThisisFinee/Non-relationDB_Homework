# Предварительные требования:
+ Установленный Docker
+ Файл [listingsAndReviews.json](https://disk.360.yandex.ru/d/_hBStCoEoQfOSg) в корневой директории проекта
# Домашняя работа:
## 1. Сборка образа
```bash
docker build -t mongo-airbnb .
```
## 2. Запуск контейнера
```bash
docker run -d -p 27017:27017 --name mongo-airbnb-container mongo-airbnb
```
## 3. Работа с контейнером
### 3.1. Подключение к контейнеру
```bash
docker exec -it mongo-airbnb-container bash
```
### 3.2 Запуск скрипта получения данных из json
```bash
source /docker-entrypoint-initdb.d/init.sh
```
### 3.2. Проверка загруженных данных
1) Переход в консоль mongo:
```bash
mongosh
```
2) Использование нужного db:
```bash
use sample_airbnb
```
3) Подсчёт кол-ва документов(должно быть 5555)
```javascript
db.listingsAndReviews.countDocuments()
```
## 4. Выполнение запросов(Выполняем в mongosh)
### 4.1. Количество объектов в Лондоне
**Запрос:**
```javascript
db.listingsAndReviews.countDocuments({
  "address.market": "London"
})
```
**Результат: 0**

### 4.2. Количество объектов с ценой выше $200
**Запрос:**
```javascript
db.listingsAndReviews.countDocuments({
  "price": { $gt: 200 }
})
```
**Результат: 1842**

### 4.3. Объект с минимальной ценой
**Запрос:**
```javascript
db.listingsAndReviews.find(
  {},
  { "name": 1, "price": 1, "address.market": 1, "_id": 0 }
).sort({ "price": 1 }).limit(1).pretty()
```
**Результат:**
```
[
  {
    name: 'Room on spacious appartment',
    price: Decimal128('9.00'),
    address: { market: 'Porto'}
  }
]
```
### 4.4. Город с максимальным количеством объектов
**Запрос:**
```javascript
db.listingsAndReviews.aggregate([
  {
    $group: {
      _id: "$address.market",
      count: { $sum: 1 }
    }
  },
  {
    $sort: { count: -1 }
  },
  {
    $limit: 1
  }
])
```
**Результат:**
```[ { _id: 'Istanbul', count: 660 } ]```

4.5. Объект с наибольшим количеством отзывов
**Запрос:**
```javascript
db.listingsAndReviews.find(
  {},
  { 
    "name": 1, 
    "number_of_reviews": 1, 
    "address.market": 1,
    "_id": 0 
  }
).sort({ "number_of_reviews": -1 }).limit(1).pretty()
```
**Результат:**
```
[
  {
    name: '#Private Studio - Waikiki Dream',
    number_of_reviews: 533,
    address: { market: 'Oahu' }
  }
]
```
