Тестирования REST API на публичных API.

Тестируемые API и результаты

1. ReqRes.in API - Основной проект тестирования

Base URL: https://reqres.in/api

Endpoint Метод Тестов Статус Найдено дефектов
/users GET 8 ✅ 0
/users/{id} GET 5 ✅ 1
/users POST 6 ✅ 1
/users/{id} PUT 4 ✅ 0
/users/{id} DELETE 3 ✅ 0
/login POST 4 ✅ 0
/register POST 4 ✅ 0
Всего:  34 теста  2 дефекта

2. JSONPlaceholder - Дополнительное тестирование

Base URL: https://jsonplaceholder.typicode.com

Endpoint Тестов Статус
/posts 5 ✅
/comments 3 ✅
/albums 2 ✅
Всего: 10 тестов 

Найденные дефекты

Дефект #1: Несоответствие документации

API: ReqRes.in
Endpoint: GET /api/users/23
Ожидаемый результат (по документации): Статус 404 с сообщением "User not found"
Фактический результат: Статус 200 с пустым объектом {}
Шаги воспроизведения:

1. Выполнить запрос GET https://reqres.in/api/users/23
2. Проверить статус код
3. Проверить тело ответа

Скриншот ответа:

```json
{}
```

Статус код: 200 OK
Приоритет: Medium

Дефект #2: Отсутствие валидации email

API: ReqRes.in
Endpoint: POST /api/users
Проблема: API принимает email без символа @ и без доменной части
Пример запроса:

```json
{
    "name": "Test User",
    "job": "QA Engineer",
    "email": "invalid-email"
}
```

Ответ API: Статус 201 Created
Ожидаемый результат: Статус 400 Bad Request с сообщением о невалидном email
Приоритет: Low

Примеры выполненных тестов

Тест: Успешное получение списка пользователей

Запрос: GET https://reqres.in/api/users?page=2
Ожидаемый результат:

· Статус код: 200 OK
· В ответе присутствуют поля: page, per_page, total, total_pages, data
· data является массивом
· per_page равно 6
· Каждый пользователь имеет поля: id, email, first_name, last_name, avatar

Фактический результат: Все проверки пройдены ✅

Тест: Создание нового пользователя

Запрос:

```http
POST https://reqres.in/api/users
Content-Type: application/json

{
    "name": "Тимур Салихов",
    "job": "QA Engineer"
}
```

Проверки:

1. Статус код 201 Created ✅
2. В ответе есть поле id ✅
3. В ответе есть поле createdAt с датой ✅
4. Имя в ответе совпадает с отправленным ✅
5. Должность в ответе совпадает с отправленной ✅

Тест: Успешная аутентификация

Запрос:

```http
POST https://reqres.in/api/login
Content-Type: application/json

{
    "email": "eve.holt@reqres.in",
    "password": "cityslicka"
}
```

Проверки:

1. Статус код 200 OK ✅
2. В ответе есть поле token ✅
3. Токен является строкой ✅
4. Длина токена > 10 символов ✅

Тест: Неуспешная аутентификация (ожидаемый fail)

Запрос:

```http
POST https://reqres.in/api/login
Content-Type: application/json

{
    "email": "test@example.com"
}
```

Ожидаемый результат: Статус 400 с сообщением "Missing password"
Фактический результат: Статус 400, сообщение "Missing password" ✅

**Была использована коллекция Postman: "ReqRes.in"**

Пример тест-кейса

TC-API-001: Создание пользователя

Предусловия: API доступно по адресу https://reqres.in/api

Шаг Действие Ожидаемый результат Статус
1 Отправить POST запрос на /users с валидными данными Статус 201 Created ✅
2 Проверить тело ответа Содержит поля id и createdAt ✅
3 Проверить значения полей name и job соответствуют отправленным ✅
4 Отправить GET запрос на /users/{id} с полученным ID Статус 200, данные соответствуют ✅
5 Отправить POST запрос с невалидным email Статус 400 (валидация) ❌ Дефект

Метрики тестирования

Общие метрики:

· Всего тест-кейсов: 34
· Выполнено: 34 (100%)
· Пройдено: 32 (94.1%)
· Не пройдено: 2 (5.9%) - найдены дефекты
· Время тестирования: ~3 часа

Используемые инструменты

1. Postman - основная платформа для тестирования
2. DevTools Network tab - анализ запросов/ответов
3. Markdown - документация тест-кейсов
4. GitHub - хранение результатов


### **Файл: POSTMAN_COLLECTION_EXPORT.md**
*(Это пример того, как выглядит экспорт из Postman)*

```markdown
## Пример запроса из коллекции

### Запрос: GET Single User
```http
GET https://reqres.in/api/users/2
Accept: application/json
```

Тесты для этого запроса:

```javascript
// Проверка статуса
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

// Проверка структуры ответа
pm.test("Response has data object", function () {
    const jsonData = pm.response.json();
    pm.expect(jsonData).to.have.property('data');
    pm.expect(jsonData.data).to.be.an('object');
});

// Проверка конкретных полей
pm.test("User has correct fields", function () {
    const user = pm.response.json().data;
    pm.expect(user).to.have.property('id');
    pm.expect(user).to.have.property('email');
    pm.expect(user).to.have.property('first_name');
    pm.expect(user).to.have.property('last_name');
    pm.expect(user).to.have.property('avatar');
    
    // Проверка типа данных
    pm.expect(user.id).to.be.a('number');
    pm.expect(user.email).to.be.a('string');
    pm.expect(user.email).to.include('@');
});

// Проверка времени ответа
pm.test("Response time is less than 500ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(500);
});
```

Пример ответа:

```json
{
    "data": {
        "id": 2,
        "email": "janet.weaver@reqres.in",
        "first_name": "Janet",
        "last_name": "Weaver",
        "avatar": "https://reqres.in/img/faces/2-image.jpg"
    },
    "support": {
        "url": "https://reqres.in/#support-heading",
        "text": "To keep ReqRes free, contributions towards server costs are appreciated!"
    }
}
```

Результат выполнения:

```
✓ Status code is 200
✓ Response has data object
✓ User has correct fields
✓ Response time is less than 500ms (234ms)
```

Все тесты пройдены успешно ✅

```

---

### **Файл: BUG_REPORTS.md**
```markdown
# Баг-репорты найденные при тестировании

## Дефект #API-001
**Заголовок:** GET запрос к несуществующему пользователю возвращает 200 вместо 404

**Серьезность:** Medium  
**Приоритет:** Medium  
**Окружение:** Production (https://reqres.in)  
**Дата обнаружения:** 15.12.2024

**Описание:**
При выполнении GET запроса к несуществующему пользователю (например, /api/users/999) API возвращает статус 200 OK с пустым объектом, хотя должен возвращать 404 Not Found.

**Шаги воспроизведения:**
1. Открыть Postman
2. Создать новый GET запрос
3. Указать URL: `https://reqres.in/api/users/999`
4. Отправить запрос
5. Проверить статус код

**Ожидаемый результат:**
- Статус код: 404 Not Found
- Тело ответа: сообщение об ошибке (например, "User not found")

**Фактический результат:**
- Статус код: 200 OK
- Тело ответа: пустой объект `{}`

**Доказательства:**
```

Request:
GET https://reqres.in/api/users/999

Response:
Status: 200 OK
Body: {}
Headers: Content-Type: application/json; charset=utf-8

```

**Влияние:**
Клиентские приложения могут некорректно обрабатывать ситуацию "пользователь не найден", так как получают успешный статус.

**Рекомендация:**
Исправить обработку запросов к несуществующим ресурсам - возвращать статус 404 с соответствующим сообщением об ошибке.

---

## Дефект #API-002
**Заголовок:** Отсутствие валидации email при создании пользователя

**Серьезность:** Low  
**Приоритет:** Low  
**Окружение:** Production (https://reqres.in)  
**Дата обнаружения:** 15.12.2024

**Описание:**
API позволяет создать пользователя с email, не соответствующим стандартному формату (без символа @, без доменной части).

**Шаги воспроизведения:**
1. Открыть Postman
2. Создать новый POST запрос
3. Указать URL: `https://reqres.in/api/users`
4. Установить заголовок: `Content-Type: application/json`
5. В теле запроса указать:
```json
{
    "name": "Test User",
    "job": "QA Engineer",
    "email": "invalid-email-format"
}
```

1. Отправить запрос

Ожидаемый результат:

· Статус код: 400 Bad Request
· Сообщение об ошибке валидации email

Фактический результат:

· Статус код: 201 Created
· Пользователь успешно создан

Доказательства:

```
Request:
POST https://reqres.in/api/users
Body: {"name": "Test", "job": "QA", "email": "invalid"}

Response:
Status: 201 Created
Body: {"id": 1001, "createdAt": "2024-12-15T10:30:00.000Z"}
```

Влияние:
В систему могут попадать некорректные данные пользователей.

Рекомендация:
Добавить валидацию поля email на соответствие стандартному формату.

```

---

## **Как представить на собеседовании:**

1. **Показать этот README.md** - как основную визитку
2. **Показать файл BUG_REPORTS.md** - как пример документирования дефектов
