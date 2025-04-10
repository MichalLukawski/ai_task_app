# 📘 Dokumentacja kontrolerów – AI Task App

## 🔐 Kontroler: `authController.js`

### POST `/api/auth/register`
- Rejestruje nowego użytkownika.
- Hasło haszowane (`bcrypt`), zwracany JWT.

### POST `/api/auth/login`
- Sprawdza poprawność danych logowania.
- Zwraca token JWT przy sukcesie.

---

## 🗂️ Kontroler: `taskController.js`

### POST `/api/tasks`
- Tworzy nowe zadanie przypisane do użytkownika.
- Walidacja `description`, `title`, `status`, `dueDate`.

---

### POST `/api/tasks/ai-create`
- Tworzy zadanie na podstawie opisu użytkownika z pomocą GPT-4o.
- **Nowość od v0.0.7**:
  - Oczekuje odpowiedzi w formacie JSON
  - Czyszczenie markdown (```json)
  - Parsowanie JSON (`JSON.parse`)
  - W przypadku błędu: fallback → zapis do pola `notes`, log do `logs/gpt_fallbacks.log`
- W przyszłości możliwe dodanie pola `difficulty` oraz porównywanie podobnych zadań.
- Walidacja: `description` minimum 5 znaków.

**Body:**
```json
{
  "description": "Nie działa API uczelni, prawdopodobnie brak Authorization"
}
```

**Typowa odpowiedź:**
```json
{
  "title": "Naprawa integracji API",
  "description": "Zadanie polega na przywróceniu poprawnego działania endpointu API uczelni...",
  "dueDate": "2025-04-15",
  "notes": "Wydaje się pilne."
}
```

---

### GET `/api/tasks`
- Zwraca wszystkie zadania zalogowanego użytkownika (`ownerId`)

---

### PUT `/api/tasks/:id`
- Aktualizuje istniejące zadanie (tytuł, opis, termin, status)

---

### POST `/api/tasks/:id/close`
- Zamyka zadanie (`status = closed`, `closedAt = now()`)
- Planowane: automatyczne podsumowanie działania z pomocą GPT

---

## 🔐 Wymagania JWT

Wszystkie powyższe metody poza `/auth/*` wymagają tokena JWT (`Authorization: Bearer <token>`)

