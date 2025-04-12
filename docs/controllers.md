# 📘 Dokumentacja kontrolerów – AI Task App

## 🔐 Kontroler: authController.js

### POST /api/auth/register

- Rejestruje nowego użytkownika.
- Hasło haszowane (`bcrypt`), zwracany JWT.

### POST /api/auth/login

- Sprawdza poprawność danych logowania.
- Zwraca token JWT przy sukcesie.

---

## 🗂️ Kontroler: taskController.js

### POST /api/tasks

- Tworzy nowe zadanie przypisane do użytkownika.
- Walidacja pól: `description`, `title`, `status`, `dueDate`.

---

### POST /api/tasks/ai-create

- Tworzy zadanie na podstawie opisu użytkownika z pomocą GPT-4o (function calling).
- Wywołuje `getTaskStructureFromAI(description)` → dane: `title`, `description`, `dueDate?`, `difficulty?`
- Zapisuje zadanie do MongoDB
- Następnie uruchamia `generateAndAttachEmbedding(taskId)`:
  - generuje embedding (`text-embedding-3-small`)
  - przypisuje `similarTasks` (top 5 z `similarity >= 0.75`)
- Walidacja: `description` minimum 5 znaków

**Przykład body:**

```json
{
  "description": "Nie działa API uczelni, prawdopodobnie brak Authorization. Mam czas do 20 maja 2025"
}
```

**Typowa odpowiedź:**

```json
{
  "title": "Naprawa API uczelni",
  "description": "Zidentyfikuj i napraw problem z API uczelni...",
  "dueDate": "2025-05-20",
  "difficulty": 4,
  "similarTasks": [...],
  "embedding": [...]
}
```

---

### POST /api/tasks/:id/ai-close

Zamyka zadanie z pomocą AI lub kopiowania istniejącego podsumowania.

- Jeśli `summary` jest dostarczone, ma ≥ 40 znaków oraz pozwala na stworzenie watościowego opisu, GPT wygładza i zapisuje
- Jeśli `summary` jest zbyt krótkie, system wymaga `force: true` i tylko wygładza
- Jeśli użytkownik wskaże `sourceTaskId`, kopiujemy `summary` z tego zadania
- Jeśli `summary` i `sourceTaskId` są puste – zwracany jest błąd

**Body:**

```json
{
  "summary": "Zmieniono token w webhooku i przetestowano działanie.",
  "force": false,
  "sourceTaskId": null
}
```

**Odpowiedzi:**

- 200 OK: zadanie zamknięte, `summary` zapisane
- 400: brak podsumowania i brak `sourceTaskId`
- 400: opis za krótki i brak `force`

---

### GET /api/tasks

- Zwraca wszystkie zadania zalogowanego użytkownika (`ownerId`)

---

### PUT /api/tasks/:id

- Aktualizuje istniejące zadanie (tytuł, opis, termin, status)

---

## 🔐 Wymagania JWT

Wszystkie powyższe metody poza `/auth/*` wymagają tokena JWT (`Authorization: Bearer <token>`)
