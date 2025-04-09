# 📘 Dokumentacja kontrolerów – AI Task App

## 🔐 Kontroler: `authController.js`

### POST `/api/auth/register`
- **Opis:** Rejestruje nowego użytkownika.
- **Wymagane dane wejściowe (JSON):**
  ```json
  {
    "email": "user@example.com",
    "password": "securepassword"
  }
  ```
- **Działanie:**
  - Tworzy nowego użytkownika w bazie danych.
  - Hasło jest haszowane (bcrypt).
  - Zwraca token JWT.
- **Odpowiedzi:**
  - `201 Created` + `{ token }`
  - `400 Bad Request` jeśli użytkownik już istnieje
  - `500 Internal Server Error` przy błędzie serwera

---

### POST `/api/auth/login`
- **Opis:** Loguje istniejącego użytkownika.
- **Wymagane dane wejściowe (JSON):**
  ```json
  {
    "email": "user@example.com",
    "password": "securepassword"
  }
  ```
- **Działanie:**
  - Sprawdza, czy użytkownik istnieje i hasło jest poprawne.
  - Zwraca token JWT.
- **Odpowiedzi:**
  - `200 OK` + `{ token }`
  - `401 Unauthorized` przy złych danych logowania
  - `500 Internal Server Error` przy błędzie serwera

---

## 🗂️ Kontroler: `taskController.js`

### POST `/api/tasks`
- **Opis:** Tworzy nowe zadanie przypisane do zalogowanego użytkownika.
- **Wymagania:** Autoryzacja JWT.
- **Wymagane dane wejściowe (JSON):**
  ```json
  {
    "description": "Opis problemu lub zadania"
  }
  ```
- **Odpowiedzi:**
  - `201 Created` + dane zadania
  - `500 Internal Server Error` przy błędzie zapisu

---

### GET `/api/tasks`
- **Opis:** Pobiera wszystkie zadania należące do zalogowanego użytkownika.
- **Wymagania:** Autoryzacja JWT.
- **Odpowiedzi:**
  - `200 OK` + tablica zadań
  - `500 Internal Server Error` przy błędzie pobierania

---

### PUT `/api/tasks/:id`
- **Opis:** Aktualizuje dane zadania.
- **Wymagania:** Autoryzacja JWT.
- **Dane wejściowe:** dowolne pola do zaktualizowania (np. `description`, `title`)
- **Odpowiedzi:**
  - `200 OK` + zaktualizowane zadanie
  - `404 Not Found` jeśli zadanie nie istnieje lub nie należy do użytkownika
  - `500 Internal Server Error` przy błędzie

---

### POST `/api/tasks/:id/close`
- **Opis:** Zamyka zadanie (ustawia `status: "closed"` i `closedAt`).
- **Wymagania:** Autoryzacja JWT.
- **Odpowiedzi:**
  - `200 OK` + zaktualizowane zadanie
  - `404 Not Found` jeśli zadanie nie istnieje lub nie należy do użytkownika
  - `500 Internal Server Error` przy błędzie
