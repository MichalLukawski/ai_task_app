# 📘 Dokumentacja kontrolerów – AI Task App

## 🔐 Kontroler: authController.js

### POST /api/auth/register

Rejestruje nowego użytkownika.
Zwraca JWT po rejestracji.

### POST /api/auth/login

Loguje użytkownika i zwraca token JWT.

---

## 🗂️ Kontroler: taskController.js

### POST /api/tasks

Tworzy nowe zadanie ręcznie.

**Body:**

```json
{
  "description": "Opis problemu",
  "title": "Opcjonalny tytuł",
  "status": "open",
  "dueDate": "2025-05-01",
  "difficulty": 3
}
```

### POST /api/tasks/ai-create

Tworzy nowe zadanie z pomocą AI (GPT-4o).

- Wymaga tylko pola `description`
- GPT generuje: `title`, `description`, `dueDate?`, `difficulty`
- Po zapisaniu zadania automatycznie generowany jest embedding i przypisywane są `similarTasks`

**Body:**

```json
{
  "description": "Nie działa webhook GitHub"
}
```

---

### PATCH /api/tasks/:id/ai-close

Zamyka zadanie z pomocą AI.

- Wymaga: `summary` (opis rozwiązania)
- Opcjonalnie: `force: true` – jeśli opis jest za krótki lub zbyt słaby
- AI ocenia jakość podsumowania i wygładza je
- Jeśli opis jest nieakceptowalny i `force` nie jest ustawiony → zwraca błąd
- Nie można używać `sourceTaskId` w tym endpointzie

**Body:**

```json
{
  "summary": "Poprawiono konfigurację webhooka GitHub.",
  "force": false
}
```

**Odpowiedzi:**

- 200 OK – zadanie zamknięte z podsumowaniem
- 400 – podsumowanie zbyt słabe bez `force`
- 400 – brak pola `summary`

---

### PATCH /api/tasks/:id/close

Zamyka zadanie poprzez skopiowanie `summary` z innego zadania.

- Wymaga: `sourceTaskId` (ID zakończonego zadania)
- Nie można przesyłać `summary`
- AI nie bierze udziału
- Wartość `summary` kopiowana 1:1 z innego zadania

**Body:**

```json
{
  "sourceTaskId": "661cabc..."
}
```

**Odpowiedzi:**

- 200 OK – zadanie zamknięte, `summary` skopiowane
- 400 – brak `sourceTaskId`
- 400 – wskazane zadanie nie istnieje lub nie zawiera `summary`

---

### GET /api/tasks

Zwraca listę zadań użytkownika.

**Response:**

```json
[
  {
    "_id": "...",
    "title": "...",
    "description": "...",
    "status": "open",
    "difficulty": 3,
    "dueDate": "...",
    "createdAt": "...",
    "ownerId": "...",
    "summary": "...",
    "similarTasks": [...],
    "embedding": [...]
  }
]
```

---

### PUT /api/tasks/:id

Aktualizuje istniejące zadanie.

- Aktualizować można: `title`, `description`, `dueDate`, `status`

**Body:**

```json
{
  "title": "Nowy tytuł",
  "description": "Nowy opis",
  "dueDate": "2025-05-10",
  "status": "closed"
}
```

**Odpowiedzi:**

- 200 OK – zadanie zaktualizowane
- 404 – zadanie nie istnieje lub nie należy do użytkownika

---

## 🔐 Wymagania JWT

Wszystkie trasy `/api/tasks/*` wymagają autoryzacji przez `Bearer <JWT>`.
