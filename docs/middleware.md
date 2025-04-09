# 🧱 Dokumentacja middleware – AI Task App

## 🧾 Plik: `middleware/auth.js`

### Opis:
Middleware autoryzacyjny sprawdzający nagłówek `Authorization` z tokenem JWT i weryfikujący jego poprawność.

### Działanie:
- Oczekuje nagłówka `Authorization: Bearer <token>`
- Weryfikuje token przy użyciu `JWT_SECRET`
- Dodaje `req.user = { id: <userId> }` do requestu
- W przypadku błędu zwraca `401 Unauthorized` z `sendError`

### Przykład błędnej odpowiedzi:
```json
{
  "status": "error",
  "message": "Invalid or expired token",
  "code": "INVALID_TOKEN"
}
```

---

## 🧾 Plik: `middleware/validate.js`

### Opis:
Middleware obsługujący błędy walidacji z `express-validator`.

### Działanie:
- Wywołuje `validationResult(req)`
- Jeśli są błędy, agreguje je i zwraca przez `sendError`
- Jeśli nie ma błędów, przekazuje request dalej

### Przykład błędnej odpowiedzi:
```json
{
  "status": "error",
  "message": "Description is required; Status must be either 'open' or 'closed'",
  "code": "VALIDATION_ERROR"
}
```

---

Ten middleware należy stosować po walidatorach w trasach, np.:
```js
router.post("/", validateTaskInput, validate, taskController.createTask);
```
