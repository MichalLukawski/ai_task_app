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

Tworzenie zadania z pomocą GPT-4o (function calling).

**Headers:** Authorization: Bearer <JWT>  
**Body:**

```json
{
  "description": "Nie działa API uczelni, prawdopodobnie brak nagłówka Authorization. Mam czas do 15 kwietnia"
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
    "title": "...",
    "dueDate": "2025-04-15",
    "difficulty": 3,
    "similarTasks": [...],
    "embedding": [...],
    "status": "open",
    "createdAt": "...",
    "ownerId": "..."
  }
}
```

---

### POST /api/tasks/:id/ai-close

Zamykanie zadania z pomocą AI lub kopiowania podsumowania z innego zadania.

**Headers:** Authorization: Bearer <JWT>  
**Body (przykładowy):**

```json
{
  "summary": "Zmieniono konfigurację webhooka GitHub i przetestowano działanie.",
  "force": false,
  "sourceTaskId": null
}
```

**Możliwe scenariusze:**

- `summary` >= 40 znaków oraz tekst, który pozwala na stworzenie użytecznego opisu → AI wygładza i zapisuje
- `summary` < 40 znaków → wymagane `force: true`
- `sourceTaskId` → kopiujemy `summary` z innego zadania (bez AI)
- brak `summary` i `sourceTaskId` → błąd

**Responses:**

- 200 OK – zadanie zamknięte, `summary` zapisane
- 400 – opis zbyt krótki bez `force`
- 400 – brak danych do zamknięcia (`summary` lub `sourceTaskId`)

---

## 📘 API: System

### GET /api/health

Sprawdzenie działania backendu.

---

## 📌 Planowane rozszerzenia API

- `POST /api/ai/similar-tasks` – zwraca podobne zadania na podstawie embeddingów
