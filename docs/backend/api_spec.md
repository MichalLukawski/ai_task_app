# 📘 Specyfikacja API – AI Task App (aktualna wersja)

Poniższa dokumentacja opisuje wszystkie dostępne endpointy API w aplikacji AI Task App. Zawiera wymagane dane wejściowe, odpowiedzi, formaty JSON oraz logikę działania. Wszystkie trasy (poza `/auth`) wymagają tokena JWT przesyłanego w nagłówku:

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

> Rejestracja wymaga późniejszego zatwierdzenia przez administratora oraz (opcjonalnie) potwierdzenia e-mail – system gotowy na 2-etapową aktywację.

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

> Logowanie możliwe tylko, jeśli użytkownik został zatwierdzony przez admina (`approvedByAdmin: true`) i ma potwierdzony e-mail (`emailVerified: true`), jeśli funkcja została włączona.

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

**Response:**

- `201 Created`

---

### POST /api/tasks/ai-create

Tworzy zadanie z pomocą GPT-4o (function calling).

**Body:**

```json
{
  "description": "Po uruchomieniu aplikacja mobilna natychmiast się zamyka"
}
```

**Response:**

- `201 Created` – zadanie utworzone z AI
- Pole `description` jest wymagane (min. 5 znaków)
- Backend generuje `embedding`, przypisuje `similarTasks`

---

### PATCH /api/tasks/:id

Aktualizuje istniejące zadanie (manualnie).

**Body:**

```json
{
  "title": "Zmieniony tytuł",
  "description": "Poprawiony opis",
  "dueDate": "2025-05-10",
  "status": "closed"
}
```

**Response:**

- `200 OK` – zadanie zaktualizowane
- `404 Not Found` – zadanie nie istnieje lub nie należy do użytkownika

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

**Opis:**

- AI ocenia jakość `summary`
- Jeśli `summary` < 40 znaków i `force` = `false` → błąd
- Jeśli `force` = `true` → AI wygładza tekst mimo długości
- Pole `sourceTaskId` nieobsługiwane w tym endpointzie

**Response:**

- `200 OK` – podsumowanie zaakceptowane i wygładzone przez AI
- `400 Bad Request` – zbyt słabe `summary` bez `force`
- `400` – brak `summary`

---

### PATCH /api/tasks/:id/close

Zamyka zadanie poprzez skopiowanie `summary` z innego zadania.

**Body:**

```json
{
  "sourceTaskId": "6621a6f6e4a8d305ccf8d4d1"
}
```

**Response:**

- `200 OK` – `summary` zostało skopiowane
- `400` – brak `sourceTaskId` lub brak `summary` w źródle

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
    "embedding": [0.123, 0.456, ...]
  }
]
```

---

## 🔐 System OpenAI API Key

### POST /api/system/openai-key

Zapisuje zaszyfrowany klucz OpenAI w bazie danych.

**Body:**

```json
{
  "apiKey": "sk-..."
}
```

**Opis:**

- Klucz jest szyfrowany AES-256-GCM
- Przechowywany w kolekcji `apiKeys` (model `ApiKey`)
- Obsługiwany `scope = "global"` (w przyszłości także per-user)
- Możliwość późniejszej rotacji

**Response:**

- `200 OK` – klucz zapisany

---

## 📎 Uwagi ogólne

- Wszystkie endpointy z `/tasks`, `/system` wymagają tokena JWT (`Authorization: Bearer`)
- Token zapisywany na frontendzie w `localStorage`
- Aplikacja rozróżnia:
  - `summary` użytkownika (oceniany przez AI)
  - `summary` skopiowany z innego zadania
- Embeddingi są generowane automatycznie po utworzeniu zadania
- Pola `similarTasks` i `embedding` nie są edytowalne przez użytkownika

---

## 🧠 Przyszłe rozszerzenia (planowane)

- `GET /api/tasks/similar` – jawne wyszukiwanie podobnych zadań
- `POST /api/tasks/:id/feedback` – informacja, czy podobne zadanie było pomocne
- Obsługa `sourceTaskId` również w AI
- Endpoint do rotacji klucza OpenAI (`/system/openai-key/rotate`)
