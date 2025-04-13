# 📘 Dokumentacja kontrolerów – AI Task App (aktualna wersja)

Dokumentacja opisuje funkcje eksportowane przez kontrolery backendu. Każdy kontroler odpowiada za realizację logiki konkretnego modułu (autoryzacja, zadania, integracja z AI, system).

---

## 🔐 Kontroler: `authController.js`

### POST /api/auth/register

Rejestruje nowego użytkownika w systemie.

- Wymagane pola: `email`, `password`
- Nowy użytkownik nie jest aktywny dopóki:
  - nie potwierdzi adresu e-mail (jeśli system aktywny)
  - nie zostanie zatwierdzony przez administratora (`approvedByAdmin: true`)
- Hasło jest haszowane (bcrypt)

---

### POST /api/auth/login

Loguje użytkownika i zwraca token JWT.

- Sprawdza poprawność e-maila i hasła
- Sprawdza, czy użytkownik jest zatwierdzony przez administratora
- Token JWT zawiera `userId`

---

## 🧠 Kontroler: `taskController.js`

Wszystkie funkcje wymagają uwierzytelnienia JWT.

---

### POST /api/tasks

Tworzy nowe zadanie manualnie.

- Wymaga: `description`
- Opcjonalnie: `title`, `dueDate`, `status`, `difficulty`

---

### POST /api/tasks/ai-create

Tworzy zadanie z pomocą GPT-4o.

- Użytkownik podaje tylko `description`
- GPT generuje `title`, `description`, `difficulty`, `dueDate?`
- Po zapisaniu zadania:
  - generowany embedding
  - przypisywane `similarTasks`

---

### PATCH /api/tasks/:id

Aktualizuje zadanie (częściowo).

- Aktualizować można: `title`, `description`, `status`, `dueDate`, `difficulty`
- Walidacja tylko jeśli pole zostało przesłane

---

### PATCH /api/tasks/:id/ai-close

Zamyka zadanie z pomocą AI.

- Wymaga: `summary`
- Opcjonalnie: `force: true`
- AI ocenia jakość i wygładza podsumowanie
- Brak wsparcia dla `sourceTaskId` – pole ignorowane

---

### PATCH /api/tasks/:id/close

Zamyka zadanie kopiując `summary` z innego.

- Wymaga: `sourceTaskId` (musisz wskazać inne zakończone zadanie)
- Nie można przesyłać `summary`
- AI nie uczestniczy – kopiowanie 1:1

---

### GET /api/tasks

Zwraca wszystkie zadania użytkownika.

- Zadania posortowane po `createdAt` malejąco (jeśli sortowanie aktywne)
- Zawiera m.in.: `title`, `description`, `status`, `difficulty`, `dueDate`, `summary`, `embedding`, `similarTasks`

---

## 🔐 Kontroler: `systemController.js`

### POST /api/system/openai-key

Zapisuje zaszyfrowany klucz OpenAI w bazie danych (`ApiKey`).

- Wymaga: `apiKey`
- Klucz szyfrowany AES-256-GCM
- Obsługiwany `scope = "global"`
- Jeśli istnieje – zostaje nadpisany (`rotatedAt`)

---

## 🧠 Middleware wspierające kontrolery

- `auth.js` – weryfikacja JWT, przypisanie `req.user`
- `validate.js` – przetwarza błędy z `express-validator`
- `taskValidator.js` – zestaw walidatorów zależnych od trasy
- `aiSummaryService.js` – logika walidacji `summary` przez AI
- `embeddingService.js` – generowanie i porównywanie embeddingów

---

## 🧩 Uwagi końcowe

- Wszystkie kontrolery są modularne, eksportują pojedyncze funkcje (`createWithAI`, `closeWithAI`, itd.)
- Błędy są obsługiwane globalnie przez middleware `errorHandler`
- Model `Task` jest centralnym zasobem zarządzanym przez AI i użytkownika
