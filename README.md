Коллекции для тестирования REST API с примерами автоматизации.

Коллекции

1. ReqRes.in Collection

Тестирование публичного API https://reqres.in/

Покрытые endpoints:

· GET /api/users - получение списка пользователей
· GET /api/users/:id - получение пользователя по ID
· POST /api/users - создание пользователя
· PUT /api/users/:id - обновление пользователя
· DELETE /api/users/:id - удаление пользователя

Пример теста:

```javascript
// Test for GET /api/users
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Response has valid JSON schema", function () {
    pm.response.to.have.jsonSchema({
        type: "object",
        required: ["page", "per_page", "total", "total_pages", "data"],
        properties: {
            page: { type: "number" },
            per_page: { type: "number" },
            total: { type: "number" },
            total_pages: { type: "number" },
            data: { type: "array" }
        }
    });
});
```

2. PetStore Collection

Тестирование Swagger PetStore API

Примеры тестов

Валидация ответа:

```javascript
pm.test("Validate response structure", function() {
    const jsonData = pm.response.json();
    pm.expect(jsonData).to.have.property('id');
    pm.expect(jsonData).to.have.property('createdAt');
    pm.expect(jsonData.email).to.include('@');
});
```

Проверка заголовков:

```javascript
pm.test("Content-Type is application/json", function () {
    pm.response.to.have.header("Content-Type", "application/json; charset=utf-8");
});
```

Цепочка запросов:

```javascript
// Create user then get by ID
const userId = pm.response.json().id;
pm.environment.set("user_id", userId);
```
