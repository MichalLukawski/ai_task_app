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

- **Error Responses:**
```json
{
  "status": "error",
  "message": "Invalid email or password",
  "code": "INVALID_CREDENTIALS"
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
  "description": "Nie działa API uczelni"
}
```

- **Success Response:**
```json
{
  "status": "success",
  "message": "Task created successfully",
  "data": {
    "_id": "...",
    "description": "Nie działa API uczelni",
    "status": "open",
    ...
  }
}
```

---

### `GET /api/tasks`

Pobieranie listy zadań użytkownika.

- **Headers:** `Authorization: Bearer <JWT>`
- **Response:**
```json
{
  "status": "success",
  "message": "Tasks retrieved successfully",
  "data": [ ... ]
}
```

---

### `PUT /api/tasks/:id`

Aktualizacja zadania.

- **Headers:** `Authorization: Bearer <JWT>`
- **Body:** dowolne pola do zmiany (np. `description`, `title`)
- **Response:**
```json
{
  "status": "success",
  "message": "Task updated successfully",
  "data": { ... }
}
```

---

### `POST /api/tasks/:id/close`

Zamykanie zadania.

- **Headers:** `Authorization: Bearer <JWT>`
- **Response:**
```json
{
  "status": "success",
  "message": "Task closed successfully",
  "data": { ... }
}
```

---

## 📘 API: AI Assistant *(planowane)*

### `POST /api/ai/similar-tasks`

Porównanie problemu z historią zadań.

- **Body:**
```json
{
  "query": "Mam problem z autoryzacją JWT"
}
```

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
- `controllers.md`
- `middleware.md`
- `utils.md`
- `project_roadmap.md`
