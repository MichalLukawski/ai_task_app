# 🧱 Dokumentacja middleware – AI Task App (zaktualizowana)

Middleware to funkcje pośredniczące w przetwarzaniu żądań HTTP. W aplikacji AI Task App odpowiadają one za uwierzytelnianie, walidację danych wejściowych, globalną obsługę błędów oraz przekazywanie logicznych wyjątków do jednolitego systemu odpowiedzi.

---

## 🔐 Plik: `middleware/auth.js`

### Opis:

Middleware uwierzytelniający użytkownika na podstawie tokena JWT przekazywanego w nagłówku `Authorization`.

### Działanie:

- Wymaga nagłówka `Authorization: Bearer <token>`
- Weryfikuje token JWT przy użyciu `process.env.JWT_SECRET`
- Pobiera użytkownika z bazy (`User.findById(...)`)
- Jeśli użytkownik istnieje:
  - Przypisuje do `req.user`: `id`, `email`, `role`
- Jeśli użytkownik nie istnieje lub token jest niepoprawny:
  - Zwraca błąd `401 Unauthorized`

### Przykład odpowiedzi błędnej:

```json
{
  "status": "error",
  "message": "Invalid or expired token",
  "code": "INVALID_TOKEN"
}
```

> ℹ️ To middleware jest stosowane we wszystkich trasach chronionych (np. `/api/tasks`, `/api/system/*`).

---

## ✅ Plik: `middleware/validate.js`

### Opis:

Middleware obsługujący wynik walidacji z `express-validator`.

### Działanie:

- Wywołuje `validationResult(req)`
- Jeśli są błędy:
  - Mapuje je do pojedynczego komunikatu tekstowego
  - Zwraca `400 Bad Request` z kodem `VALIDATION_ERROR`
- Jeśli brak błędów → przekazuje żądanie do następnego handlera

### Przykład odpowiedzi błędnej:

```json
{
  "status": "error",
  "message": "Email is required; Password must be at least 6 characters long",
  "code": "VALIDATION_ERROR"
}
```

> ℹ️ Middleware wykorzystywany w połączeniu z `authValidator.js` i `taskValidator.js`.

---

## 🧠 Plik: `middleware/errorHandler.js`

### Opis:

Globalny middleware przechwytujący błędy nieobsłużone przez kontrolery ani routery.

### Działanie:

- Obsługuje błędy typu runtime (`try/catch`)
- Sprawdza `res.headersSent` – jeśli odpowiedź została wysłana, przekazuje błąd dalej
- W przypadku nieoczekiwanego błędu – zwraca `500 Internal Server Error`

### Przykład:

```json
{
  "status": "error",
  "message": "Internal server error"
}
```

> ℹ️ Używany w konfiguracji głównej aplikacji Express jako ostatni middleware (`app.use(errorHandler)`).

---

## ✅ Zastosowanie w trasach

Przykład użycia:

```js
router.patch(
  "/tasks/:id/ai-close",
  auth,
  validateCloseTaskWithAI,
  validate,
  handleTryCatch(closeTaskWithAI)
);
```

- `auth` → weryfikuje tożsamość
- `validateCloseTaskWithAI` → przygotowuje reguły walidacji
- `validate` → sprawdza poprawność danych
- `handleTryCatch(...)` → zapewnia przechwycenie błędów async

---

## 📄 Dokumentacja powiązana

- `validators.md` – reguły walidacji (`express-validator`)
- `utils/responseHandler.js` – `sendError`, `sendSuccess`, `handleTryCatch`
- `controllers/` – logika główna wykorzystująca middleware
- `routes/` – miejsca wywołań middleware w trasach

---

## 🔄 Możliwe rozszerzenia

- Dodanie `middleware/requireAdmin.js` do kontroli ról
- Middleware `logger.js` do rejestrowania zapytań i odpowiedzi
- Obsługa limitów zapytań (`rate-limiting`) na poziomie middleware

---

## 🆕 Nowe i potwierdzone zastosowania middleware (2025-04)

### 🔄 Nowe ścieżki z użyciem middleware

| Endpoint                          | auth.js | validate.js | Uwagi                                                |
| --------------------------------- | ------- | ----------- | ---------------------------------------------------- |
| `PATCH /api/tasks/:id/ai-close`   | ✅      | ❌          | Brak walidatora, sprawdzane w `processTaskClosure()` |
| `PATCH /api/tasks/:id/close`      | ✅      | ✅          | Używa nowego walidatora `validateCloseTaskManually`  |
| `PATCH /api/tasks/:id/close-copy` | ✅      | ✅          | Używa `validateCloseTaskFromOther`                   |
| `DELETE /api/tasks/:id`           | ✅      | ❌          | Nie wymaga `body`, więc nie potrzebuje walidatora    |

> Wszystkie te trasy zabezpieczone są `auth.js`, a niektóre z nich używają dodatkowych reguł walidacyjnych przez `validate.js`.

---

## 🔍 Opis `handleTryCatch` (z `utils/responseHandler.js`)

Choć nie znajduje się bezpośrednio w folderze `middleware/`, funkcjonuje jako _asynchroniczny middleware typu wrapper_.

**Działanie:**

- Obejmuje każdy `async`-handler kontrolera
- Automatycznie przechwytuje błędy i przekazuje je do obsługi błędów globalnych

**Przykład:**

```js
router.patch(
  "/:id/ai-close",
  auth,
  handleTryCatch(taskController.closeTaskWithAI)
);
```

---

## ✅ Utrzymane dobre praktyki

- Middleware są **kompozycyjnie stosowane** w każdej trasie.
- `validate.js` gwarantuje jednolite formaty komunikatów błędów.
- Brak powtórzonej logiki walidacyjnej w kontrolerach – całość delegowana do middleware.
- Każda trasa zabezpieczona JWT (brak tras publicznych w `/api/tasks`).

---
