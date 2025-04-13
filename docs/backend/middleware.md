# 🧱 Dokumentacja middleware – AI Task App (aktualna wersja)

Middleware to funkcje pośredniczące w przetwarzaniu żądań HTTP, odpowiedzialne za autoryzację, walidację oraz globalną obsługę błędów.

---

## 🔐 Plik: `middleware/auth.js`

### Opis:

Middleware uwierzytelniający użytkownika na podstawie nagłówka `Authorization` z tokenem JWT.

### Działanie:

- Wymaga nagłówka `Authorization: Bearer <token>`
- Weryfikuje JWT przy użyciu `process.env.JWT_SECRET`
- W przypadku powodzenia dodaje `req.user = { id }` do obiektu `request`
- W przypadku niepowodzenia zwraca błąd `401 Unauthorized`

### Przykład odpowiedzi błędnej:

```json
{
  "status": "error",
  "message": "Invalid or expired token",
  "code": "INVALID_TOKEN"
}
```

---

## ✅ Plik: `middleware/validate.js`

### Opis:

Middleware walidacyjny – obsługuje wyniki walidatorów z `express-validator`.

### Działanie:

- Wywołuje `validationResult(req)`
- Jeśli są błędy:
  - Agreguje je
  - Wysyła odpowiedź z `sendError` (`400 Bad Request`)
- Jeśli nie ma błędów → przekazuje dalej do kontrolera

### Przykład odpowiedzi błędnej:

```json
{
  "status": "error",
  "message": "Validation failed",
  "errors": [
    {
      "field": "description",
      "message": "Description is required"
    },
    {
      "field": "status",
      "message": "Status must be either 'open' or 'closed'"
    }
  ]
}
```

---

## 🧠 Plik: `middleware/errorHandler.js`

### Opis:

Globalny middleware przechwytujący błędy nieobsłużone w innych miejscach.

### Działanie:

- Obsługuje błędy runtime (`try/catch`)
- Reaguje na `res.headersSent`
- Zwraca `500 Internal Server Error` jeśli nie podano kodu

### Przykład:

```json
{
  "status": "error",
  "message": "Internal server error"
}
```

---

## ✅ Zastosowanie w trasach

Przykład użycia:

```js
router.patch(
  "/tasks/:id/ai-close",
  auth,
  validateCloseTaskWithAI,
  validate,
  closeTaskWithAI
);
```

---

## 📄 Dokumentacja powiązana

- `validators.md` – dostarcza walidatory
- `utils/responseHandler.js` – `sendError`, `sendSuccess`
- `controllers/` – wykorzystuje `auth`, `validate`
