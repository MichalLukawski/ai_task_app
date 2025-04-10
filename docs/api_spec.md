# AI Task App – API Specification

## 📘 API: Authentication

### POST /api/auth/register
Rejestracja nowego użytkownika.

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

---

### POST /api/auth/login
Logowanie użytkownika.

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
    "token": "JWT_TOKEN_HERE"
  }
}
```

---

## 📘 API: Tasks

### POST /api/tasks
Tworzenie nowego zadania (manualnie).

**Headers:** Authorization: Bearer <JWT>
**Body:**
```json
{
  "description": "Nie działa API uczelni",
  "title": "Awaria API",
  "dueDate": "2025-05-01"
}
```

**Response:**
```json
{
  "status": "success",
  "message": "Task created successfully",
  "data": {
    "_id": "...",
    "description": "...",
    "title": "...",
    "status": "open",
    "dueDate": "...",
    "createdAt": "..."
  }
}
```

---

### POST /api/tasks/ai-create
Tworzenie zadania z pomocą GPT-4o.

**Headers:** Authorization: Bearer <JWT>
**Body:**
```json
{
  "description": "Nie działa API uczelni, chyba brakuje nagłówka Authorization"
}
```

**Response:**
```json
{
  "status": "success",
  "message": "AI-generated task created",
  "data": {
    "_id": "...",
    "description": "...",
    "notes": "...",
    "status": "open",
    "createdAt": "...",
    "ownerId": "..."
  }
}
```

---

## 📘 API: System

### GET /api/health
Sprawdzenie działania backendu.

---

## 🛡️ Autoryzacja
Wszystkie trasy z `/api/tasks` i `/api/ai` wymagają tokena JWT w nagłówku Authorization.
