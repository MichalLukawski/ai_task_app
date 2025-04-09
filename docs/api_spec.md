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

---

### `POST /api/auth/login`

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
  "message": "Login successful",
  "data": {
    "token": "JWT_TOKEN_HERE"
  }
}
```

---

## 📘 API: Tasks

### `POST /api/tasks`

Tworzenie nowego zadania.

- **Headers:** `Authorization: Bearer <JWT>`
- **Body:**
```json
{
  "description": "Nie działa API uczelni",
  "title": "Awaria API",
  "dueDate": "2025-05-01"
}
```

- **Success Response:**
```json
{
  "status": "success",
  "message": "Task created successfully",
  "data": {
    "_id": "...",
    "description": "...",
    "title": "...",
    "status": "open",
    "dueDate": "2025-05-01T00:00:00.000Z",
    "createdAt": "...",
    ...
  }
}
```

---

### `GET /api/tasks`

Pobieranie listy zadań użytkownika.

- **Headers:** `Authorization: Bearer <JWT>`

---

### `PUT /api/tasks/:id`

Aktualizacja zadania.

- **Headers:** `Authorization: Bearer <JWT>`
- **Body:**
```json
{
  "description": "Zaktualizowany opis",
  "title": "Zaktualizowany tytuł",
  "status": "closed",
  "dueDate": "2025-05-10"
}
```

---

### `POST /api/tasks/:id/close`

Zamykanie zadania.

---

## 📘 API: AI Assistant *(planowane)*

### `POST /api/ai/similar-tasks`

Porównanie problemu z historią zadań.

---

## 📘 API: System

### `GET /api/health`

Sprawdzenie działania backendu i połączenia z bazą.

---

## 🛡️ Autoryzacja

- Wszystkie trasy `tasks/`, `ai/` wymagają tokena JWT w nagłówku `Authorization`
- Token dekodowany przez middleware `auth.js`

---

## 📄 Dokumentacja powiązana

- `project_overview.md`
- `backend_overview.md`
- `api_spec.md`
- `validators.md`
- `middleware.md`
- `utils.md`
