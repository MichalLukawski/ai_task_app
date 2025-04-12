# 📘 Specyfikacja API – AI Task App (wersja polska)

Poniżej znajduje się pełna dokumentacja API aplikacji AI Task App – zawierająca strukturę endpointów, wymagane pola, odpowiedzi i zasady działania dla każdego przypadku. Wszystkie trasy (poza `/auth`) wymagają tokena JWT (`Authorization: Bearer <token>`).

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

---

## 🧠 Zarządzanie zadaniami

### POST /api/tasks

Tworzy nowe zadanie ręcznie (bez AI).

- Pole wymagane: `description` (min. 5 znaków)
- Pola opcjonalne: `title`, `dueDate`, `status`, `difficulty`

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

- 201 Created – zadanie utworzone

---

### POST /api/tasks/ai-create

Tworzy zadanie z pomocą GPT-4o (function calling).

- Wymaga: `description` (string)
- GPT generuje: `title`, `description`, `dueDate?`, `difficulty`
- Backend generuje embedding i przypisuje `similarTasks` (max 5)

**Body:**

```json
{
  "description": "Po uruchomieniu aplikacja mobilna natychmiast się zamyka"
}
```

**Response:**

- 201 Created – zadanie utworzone na podstawie odpowiedzi AI

---

### PATCH /api/tasks/:id

Aktualizuje istniejące zadanie.

- Można zaktualizować: `title`, `description`, `dueDate`, `status`

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

- 200 OK – zadanie zaktualizowane
- 404 – zadanie nie istnieje lub nie należy do użytkownika

---

### PATCH /api/tasks/:id/ai-close

Zamyka zadanie z pomocą AI.

- Pole wymagane: `summary` – opis rozwiązania
- AI ocenia, czy opis jest wystarczający i wygładza go
- Jeśli opis jest zbyt krótki lub słaby → zwraca błąd
- Użytkownik może wymusić użycie opisu przez `force: true`
- Pole `sourceTaskId` nie jest obsługiwane – kopiowanie tylko przez `/close`

**Body:**

```json
{
  "summary": "Zaktualizowano konfigurację webhooka i przetestowano połączenie.",
  "force": false
}
```

**Response:**

- 200 OK – zadanie zamknięte z podsumowaniem wygenerowanym przez AI
- 400 – podsumowanie zbyt słabe bez `force`
- 400 – brak pola `summary`

---

### PATCH /api/tasks/:id/close

Zamyka zadanie przez skopiowanie `summary` z innego, wcześniej zamkniętego zadania.

- Pole wymagane: `sourceTaskId` (ID Mongo zadania, z którego kopiujemy podsumowanie)
- Pole `summary` nie może być obecne
- Nie wykorzystuje AI

**Body:**

```json
{
  "sourceTaskId": "6621a6f6e4a8d305ccf8d4d1"
}
```

**Response:**

- 200 OK – zadanie zamknięte, `summary` zostało skopiowane
- 400 – brak `sourceTaskId` lub `summary` nie istnieje w źródłowym zadaniu

---

### GET /api/tasks

Zwraca wszystkie zadania należące do zalogowanego użytkownika.

**Response:**

```json
[
  {
    "_id": "...",
    "title": "...",
    "description": "...",
    "status": "open",
    "difficulty": 2,
    "dueDate": "2025-05-10",
    "summary": null,
    "createdAt": "...",
    "ownerId": "...",
    "similarTasks": [...],
    "embedding": [...]
  }
]
```

---

## 📎 Uwagi ogólne

- Wszystkie trasy `/api/tasks/*` wymagają tokena JWT w nagłówku:  
  `Authorization: Bearer <JWT>`
- `summary` można wysyłać tylko w `/ai-close` i zawsze podlega ocenie przez AI
- Endpoint `/close` obsługuje wyłącznie kopiowanie gotowego `summary` z innego zadania
