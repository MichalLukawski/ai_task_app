# 🛠️ Dokumentacja utils – AI Task App (zaktualizowana)

---

## 📄 Plik: `utils/responseHandler.js`

Moduł odpowiedzialny za jednolitą strukturę odpowiedzi API oraz przechwytywanie błędów `async/await` w kontrolerach. Stosowany we wszystkich głównych kontrolerach i trasach. Współpracuje z middleware takimi jak `auth.js`, `validate.js` oraz `errorHandler.js`.

---

### `sendSuccess(res, message, data, status = 200)`

- **Opis:** Wysyła odpowiedź JSON z komunikatem sukcesu.
- **Parametry:**
  - `res` – obiekt odpowiedzi Express
  - `message` – tekst komunikatu (np. `"Task updated successfully"`)
  - `data` – dane użytkowe zwracane do klienta (może być `null`)
  - `status` – kod HTTP (domyślnie 200)

**Przykład odpowiedzi:**

```json
{
  "status": "success",
  "message": "Login successful",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIs..."
  }
}
```

---

### `sendError(res, message, status = 500, code = null)`

- **Opis:** Wysyła odpowiedź JSON informującą o błędzie.
- **Parametry:**
  - `res` – obiekt odpowiedzi Express
  - `message` – opis błędu
  - `status` – kod HTTP (np. 400, 401, 500)
  - `code` – (opcjonalnie) własny kod błędu aplikacji (np. `VALIDATION_ERROR`, `NO_TOKEN`)

**Przykład odpowiedzi:**

```json
{
  "status": "error",
  "message": "Invalid or expired token",
  "code": "INVALID_TOKEN"
}
```

---

### `handleTryCatch(asyncFn)`

- **Opis:** Wrapper dla funkcji asynchronicznych używany w routerach do obsługi błędów.
- **Zastosowanie:** Zamiast pisać `try/catch` w każdym kontrolerze – przekazujemy funkcję do `handleTryCatch`.
- **Zwraca:** Nową funkcję `(req, res, next)` z automatycznym przechwytywaniem wyjątków.

**Przykład użycia w trasie:**

```js
router.patch(
  "/tasks/:id",
  auth,
  validateUpdateTaskInput,
  validate,
  handleTryCatch(updateTask)
);
```

- Jeśli `updateTask` rzuci błąd → zostanie on automatycznie złapany i przekazany do `sendError`.

---

## 🔁 Wewnętrzne konwencje

- Wszystkie kontrolery używają `sendSuccess`, `sendError`
- `sendError` obsługuje błędy walidacji, autoryzacji i błędy wewnętrzne
- `handleTryCatch` eliminuje konieczność `try/catch` w `authController`, `taskController`, `systemController`

---

## 📁 Planowane i możliwe rozszerzenia `utils/`

Moduł `utils/` będzie rozszerzany o następujące narzędzia:

- `formatDate(date)` – ustandaryzowane formatowanie dat (np. `YYYY-MM-DD`)
- `isTaskOwner(req, task)` – sprawdzenie, czy użytkownik jest właścicielem zadania
- `generateToken(length)` – generator tokenów (np. do aktywacji e-mail)
- `parseAIResponse(tool_call)` – ekstrakcja argumentów z `function_calling`
- `logObjectDeep(obj)` – debugowanie zagnieżdżonych struktur obiektów

---

## 📄 Dokumentacja powiązana

- `controllers.md` – zastosowanie `sendSuccess`, `sendError`
- `routes.md` – zastosowanie `handleTryCatch` w trasach
- `middleware.md` – jak `sendError` współpracuje z `validate.js`, `auth.js`
- `services.md` – przechwytywanie błędów w integracji z OpenAI (`try/catch` → `sendError`)
