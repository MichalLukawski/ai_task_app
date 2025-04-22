# 📘 Specyfikacja API – AI Task App (zaktualizowana wersja)

Dokument zawiera kompletną specyfikację interfejsu API aplikacji AI Task App. Opisane zostały wszystkie dostępne endpointy HTTP, struktura żądań i odpowiedzi, typy danych oraz wymagania autoryzacyjne. Dane wejściowe są walidowane za pomocą `express-validator`, a odpowiedzi formatowane zgodnie z modułem `utils/responseHandler.js`.

Wszystkie trasy (poza `/auth`) wymagają tokena JWT przesyłanego w nagłówku:

```
Authorization: Bearer <JWT>
```

---

## 🔐 Autoryzacja

### POST /api/auth/register

Rejestruje nowego użytkownika.

**Body:**

```json
{
  "email": "user@example.com",
  "password": "password123"
}
```

**Response:**

```json
{
  "status": "success",
  "message": "User registered successfully"
}
```

> Pola walidowane przez `authValidator.js`. Hasło jest szyfrowane z użyciem `bcrypt`. System gotowy do rozbudowy o e-mail verification i aktywację konta przez admina.

---

### POST /api/auth/login

Loguje użytkownika i zwraca token JWT.

**Body:**

```json
{
  "email": "user@example.com",
  "password": "password123"
}
```

**Response:**

```json
{
  "status": "success",
  "message": "Login successful",
  "data": {
    "token": "JWT_TOKEN"
  }
}
```

> Logowanie dostępne tylko dla zatwierdzonych użytkowników. Po zalogowaniu token zapisywany na froncie w `localStorage`.

---

## 🧠 Zadania – zarządzanie

### POST /api/tasks

Tworzy nowe zadanie ręcznie (bez AI).

**Body:**

```json
{
  "description": "Zintegrować serwis z nowym API uczelni",
  "title": "Integracja z API",
  "dueDate": "2025-05-01",
  "difficulty": 3
}
```

**Response:** `201 Created` + zwrócony task (pełny)

---

### POST /api/tasks/ai-create

Tworzy zadanie z pomocą GPT-4o (function calling).

**Body:**

```json
{
  "description": "Po uruchomieniu aplikacja mobilna natychmiast się zamyka"
}
```

**Response:** `201 Created`

Zwracane pola:

- `description` (poprawiony przez AI)
- `title` (opcjonalnie wygenerowany)
- `difficulty` (1–5)
- `dueDate` (jeśli rozpoznany)
- `embedding`
- `similarTasks` (array ID podobnych zadań)

---

### PATCH /api/tasks/:id

Aktualizuje zadanie (częściowo).

**Body:**

```json
{
  "title": "Zmieniony tytuł",
  "description": "Poprawiony opis",
  "dueDate": "2025-05-10",
  "status": "closed",
  "difficulty": 4
}
```

**Response:**

```json
{
  "status": "success",
  "message": "Task updated successfully",
  "data": {
    "_id": "...",
    "title": "...",
    ...
  }
}
```

> Backend zawsze zwraca pełny zaktualizowany obiekt `task` po zapisaniu.

---

### PATCH /api/tasks/:id/ai-close

Zamyka zadanie z pomocą AI.

**Body:**

```json
{
  "summary": "Zaktualizowano konfigurację webhooka i przetestowano połączenie.",
  "force": false
}
```

**Response:** `200 OK` lub `400 Bad Request`

> Jeśli `summary` < 40 znaków i `force = false`, operacja zostanie zablokowana.
> Jeśli `force = true`, AI wygładza tekst mimo wszystko (`improveSummary()`).

---

### PATCH /api/tasks/:id/close

Zamyka zadanie ręcznie, zapisując `summary` dostarczone przez użytkownika. Ten endpoint jest używany:

- w sytuacji gdy AI odrzuci zaproponowane podsumowanie (walidacja semantyczna)
- lub gdy użytkownik wprost chce wprowadzić własne podsumowanie bez użycia AI

**Body:**

```json
{
  "summary": "Opis rozwiązania problemu z wykrywaniem nieautoryzowanego tokenu."
}
```

**Response:** `200 OK` lub `400 Bad Request`

> Jeśli `summary` ma mniej niż 10 znaków – operacja zostaje odrzucona z błędem walidacji.
> Po zatwierdzeniu ustawiany jest `status: "closed"`, `closedAt`, `summary`.

Zamyka zadanie kopiując `summary` z innego zakończonego zadania.

**Body:**

```json
{
  "sourceTaskId": "6621a6f6e4a8d305ccf8d4d1"
}
```

**Response:** `200 OK` lub `400` jeśli brak `summary` w źródle

---

### GET /api/tasks

Zwraca wszystkie zadania zalogowanego użytkownika.

**Response:**

```json
[
  {
    "_id": "6621a6...",
    "title": "Błąd konfiguracji JWT",
    "description": "Token nie był wysyłany w nagłówku",
    "status": "closed",
    "difficulty": 3,
    "dueDate": "2025-05-10",
    "summary": "...",
    "ownerId": "661f3...",
    "similarTasks": ["661a...", "6609..."],
    "embedding": [0.123, 0.456, ...],
    "createdAt": "...",
    "updatedAt": "..."
  }
]
```

> Zadania są sortowane malejąco po `createdAt`.

---

## ⚙️ System OpenAI Key

### POST /api/system/openai-key

Zapisuje klucz OpenAI.

**Body:**

```json
{
  "apiKey": "sk-..."
}
```

**Response:** `200 OK`

> Klucz jest szyfrowany z użyciem AES-256-GCM i zapisywany w modelu `ApiKey`. Scope = "global".

---

## 🧩 Uwagi końcowe

- Wszystkie dane wejściowe są walidowane przez `express-validator`
- Obsługa błędów odbywa się przez `utils/responseHandler.js → sendError(...)`
- Wszystkie dane odpowiedzi są opakowane w `sendSuccess(...)`
- Wszystkie endpointy (poza `/auth`) wymagają tokena JWT
- Embeddingi są generowane automatycznie po utworzeniu zadania
- `similarTasks` i `embedding` nie są modyfikowalne ręcznie

---

### DELETE /api/tasks/:id

Usuwa zadanie permanentnie z bazy. Użytkownik musi być właścicielem zadania (`ownerId === req.user.id`).

**Response:**

```json
{
  "status": "success",
  "message": "Task permanently deleted."
}
```

> Endpoint wymaga autoryzacji. Brak potwierdzenia w protokole – odpowiedzialność po stronie UI (np. confirm()).
> Brak wersji "koszowej" – usunięcie jest nieodwracalne.

---

## 🧠 Możliwe rozszerzenia

- `GET /api/tasks/similar` – generowanie podobnych zadań na żądanie
- `POST /api/tasks/:id/feedback` – ocena działania AI
- `GET /api/system/openai-key` – pobieranie klucza (dla admina)
- `DELETE /api/tasks/:id` – usuwanie zadań (z potwierdzeniem)
