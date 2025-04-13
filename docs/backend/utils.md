# 🛠️ Dokumentacja utils – AI Task App (aktualna wersja)

---

## 📄 Plik: `utils/responseHandler.js`

### `sendSuccess(res, message, data, status = 200)`

- **Opis:** Wysyła jednolitą odpowiedź typu sukces (`status: "success"`).
- **Parametry:**
  - `res` – obiekt odpowiedzi Express
  - `message` – tekstowy komunikat, np. `"Login successful"`
  - `data` – dane opcjonalne (np. `token`, `task`, `user`)
  - `status` – HTTP status code (domyślnie 200)

**Przykład odpowiedzi:**

```json
{
  "status": "success",
  "message": "Task created successfully",
  "data": {
    "title": "Błąd z JWT",
    "difficulty": 3
  }
}
```

---

### `sendError(res, message, status = 500, code = null)`

- **Opis:** Wysyła jednolitą odpowiedź błędu (`status: "error"`).
- **Parametry:**
  - `res` – obiekt odpowiedzi Express
  - `message` – tekst błędu, np. `"Invalid token"`
  - `status` – kod błędu HTTP, np. `400`, `401`, `500`
  - `code` – kod błędu wewnętrznego (opcjonalnie), np. `INVALID_TOKEN`, `VALIDATION_FAILED`

**Przykład odpowiedzi:**

```json
{
  "status": "error",
  "message": "Invalid token",
  "code": "INVALID_TOKEN"
}
```

---

## 📁 Przyszłe rozszerzenia `utils/`

Planowane są dodatkowe moduły pomocnicze, m.in.:

- funkcje do walidacji ról (`isAdmin`, `isTaskOwner`)
- formatowanie dat (`formatDate`)
- generatory tokenów do aktywacji konta / weryfikacji e-mail
- narzędzia do debugowania danych (np. `logObjectDeep`)

---

## 📄 Dokumentacja powiązana

- `middleware.md` – obsługa błędów (np. `errorHandler`)
- `controllers.md` – użycie `sendSuccess` i `sendError` w odpowiedziach
- `validators.md` – współpraca z `sendError` dla walidacji danych
