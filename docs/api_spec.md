# AI Task App – API Specification

## 📘 API: Authentication

### `POST /api/auth/register`

Rejestracja nowego użytkownika.

- **Body (JSON):**

```json
{
  "email": "user@example.com",
  "password": "password123"
}
```

- **Success Response:**

```json
{
  "status": "success",
  "message": "User registered successfully"
}
```

- **Error Responses:**

```json
{
  "status": "error",
  "message": "Email already registered",
  "code": "EMAIL_EXISTS"
}
```

---

### `POST /api/auth/login` *(planowane)*

Logowanie użytkownika.

- **Body (JSON):**

```json
{
  "email": "user@example.com",
  "password": "password123"
}
```

- **Success Response:**

```json
{
  "status": "success",
  "token": "JWT_TOKEN_HERE"
}
```

- **Error Responses:** Nieprawidłowy e-mail/hasło, konto nie istnieje

---

## 📘 API: Tasks *(planowane)*

### `POST /api/tasks`

Tworzenie nowego zadania.

- **Headers:** `Authorization: Bearer <JWT>`
- **Body:**

```json
{
  "description": "Nie działa API uczelni"
}
```

- **Response:**

```json
{
  "status": "success",
  "data": {
    "title": "Problem z integracją z API uczelni",
    "notes": "...",
    "dueDate": "2025-04-15",
    "difficulty": "medium"
  }
}
```

---

### `GET /api/tasks`

Pobieranie listy zadań użytkownika (z paginacją, filtrowaniem).

---

### `GET /api/tasks/:id`

Pobranie szczegółów konkretnego zadania.

---

### `PUT /api/tasks/:id`

Aktualizacja zadania.

---

### `POST /api/tasks/:id/close`

Zamykanie zadania z pomocą GPT (generowanie podsumowania).

---

## 📘 API: AI Assistant *(planowane)*

### `POST /api/ai/similar-tasks`

Zwraca zadania najbardziej podobne do opisu problemu.

- **Body:**
```json
{
  "query": "Mam problem z autoryzacją JWT"
}
```

- **Response:**
```json
{
  "matches": [
    { "title": "JWT token expired", "solution": "...", "date": "2025-03-01" }
  ]
}
```

---

### `POST /api/ai/plan-order`

GPT proponuje kolejność wykonania zadań wg trudności, pilności, deadline.

---

## 📘 API: System *(planowane)*

### `GET /api/health`

Sprawdza czy API działa, połączenie z Mongo i konfiguracja środowiska.

---

## 🛡️ Autoryzacja

- Wszystkie trasy `tasks/`, `ai/` wymagają tokena JWT w nagłówku `Authorization`
- Użytkownik musi być zalogowany
- Role `admin`, `user` (middleware `requireRole`)

---

## 📄 Dokumentacja powiązana

- `project_overview.md` – pełny kontekst projektu, cele, architektura, repozytoria, AI, modularność
- `backend_overview.md` – opis struktury backendu, endpointów, technologii i modelu autoryzacji
- `frontend_overview.md` – opis frontendu, komponentów, architektury, interfejsów użytkownika
- `api_spec.md` – specyfikacja endpointów REST API (auth, tasks, AI), dane wejściowe/wyjściowe
- `ai_integration.md` – jak GPT-4 wspiera zadania: tworzenie, ocena, zamykanie, priorytetyzacja
- `project_roadmap.md` – roadmapa projektu: fazy rozwoju, MVP, AI, skalowanie, funkcje zespołowe