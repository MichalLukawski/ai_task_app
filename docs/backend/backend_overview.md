# 🧱 AI Task App – Backend Overview (pełna wersja, rozszerzona)

Ten dokument stanowi pełny opis warstwy backendowej aplikacji AI Task App, służącej do zarządzania zadaniami użytkowników z pomocą sztucznej inteligencji (GPT-4o) oraz mechanizmu semantycznego porównywania problemów.

Dokument ma na celu zaprezentowanie:

- architektury backendu,
- relacji między modułami,
- decyzji projektowych,
- oraz technicznych szczegółów integracji z AI i OpenAI.

---

## 🎯 Główna rola backendu

Backend pełni funkcję centralnego systemu aplikacji:

- zarządza kontami użytkowników,
- zapewnia bezpieczny dostęp przez JWT,
- zapisuje i pobiera zadania (`Task`),
- integruje się z GPT-4o i embeddingami OpenAI,
- wykonuje automatyczne klasyfikacje i oceny,
- porównuje zadania semantycznie i przypisuje podobne przypadki.

---

## 📐 Architektura – przegląd modułów

```
backend/
├── config/                # Połączenie z MongoDB (mongoose)
├── controllers/           # Główna logika dla tras API
├── middleware/            # Autoryzacja, walidacja, błędy
├── models/                # Schematy Mongoose
├── routes/                # Pliki routingu
├── services/              # Integracja z GPT, embeddingi, AI logic
├── utils/                 # responseHandler (formatowanie odpowiedzi)
├── validators/            # express-validator dla tras
└── server.js              # Punkt startowy aplikacji
```

---

## 🧾 Modele danych

### 👤 User

- `email`, `password` (haszowany)
- `role`: `"user"` lub `"admin"`
- `emailVerified`: weryfikacja przez email (planowane)
- `approvedByAdmin`: zatwierdzanie rejestracji przez administratora
- Powiązany z zadaniami poprzez `ownerId`

### 📄 Task

- `title`, `description`, `status`
- `dueDate`, `difficulty`, `summary`, `notes`
- `embedding`: wektor semantyczny (float[])
- `similarTasks`: lista `_id` zakończonych zadań o podobnej treści
- Tworzone manualnie lub przez GPT

### 🔐 ApiKey

- Przechowuje zaszyfrowany klucz OpenAI (`encryptedKey`, `iv`, `tag`)
- Algorytm: `AES-256-GCM`
- Obsługuje `scope = "global"` (w przyszłości: `userId`)
- Służy do zarządzania kluczem dostępu do GPT/embeddingów

---

## 🔗 Integracja z GPT (OpenAI)

### 📁 `services/gptService.function.js`

- `getTaskStructureFromAI(description)` → `create_task`
- `getSummaryAssessment(task, userSummary)` → `assess_summary`
- `improveSummary(summary)` → `improve_summary`
- Wszystko z użyciem `function_calling` i `tool_choice`

### 📁 `services/aiSummaryService.js`

- Obsługuje zamykanie zadania:
  - Waliduje jakość `summary` (40+ znaków)
  - W razie potrzeby pozwala na `force`
  - Przesyła do `gptService` do wygładzenia

---

## 🧠 Embeddingi i podobieństwo zadań

### 📁 `services/embeddingService.js`

- Generuje embeddingi z `text-embedding-3-small`
- Porównuje je przez cosine similarity
- Dla nowych zadań przypisuje max 5 podobnych (`similarTasks`)
- Próg podobieństwa: `0.75`

---

## 🔐 Bezpieczeństwo

- JWT do uwierzytelnienia użytkownika
- bcrypt do haszowania haseł
- AES-256-GCM do szyfrowania kluczy API
- Middleware `auth.js`, `validate.js`, `errorHandler.js`

---

## 🧪 Przykład przepływu użytkownika (user journey)

1. Użytkownik rejestruje konto → trafia jako nieaktywne (`approvedByAdmin = false`)
2. Po zatwierdzeniu przez admina → może się zalogować
3. Tworzy zadanie z pomocą AI → GPT generuje dane → embedding porównuje z przeszłością
4. Zamykając zadanie, wpisuje podsumowanie → AI ocenia jego jakość
5. Jeśli poprawne → AI wygładza język i zapisuje `summary`

---

## 📊 Warstwa usług – zależności między modułami

- `routes/taskRoutes.js`
  → `controllers/taskController.js`
  → `services/gptService.function.js`
  → `services/aiSummaryService.js`
  → `services/embeddingService.js`
  → `models/Task.js`

- `routes/systemRoutes.js`
  → `controllers/systemController.js`
  → `services/openaiKeyManager.js`
  → `models/ApiKey.js`

---

## 🌐 Endpointy i ich funkcje

- `POST /api/tasks/ai-create` – tworzy zadanie z pomocą AI
- `PATCH /api/tasks/:id/ai-close` – zamyka zadanie z pomocą AI (`summary`)
- `PATCH /api/tasks/:id/close` – kopiuje podsumowanie z innego zadania
- `POST /api/system/openai-key` – szyfruje i zapisuje klucz OpenAI
- `GET /api/tasks` – lista zadań użytkownika

---

## 📁 Walidacja i odpowiedzi

- `validators/taskValidator.js` + `validate.js`
- Ujednolicone odpowiedzi: `utils/responseHandler.js`
- Błędy walidacji: kod 400, lista pól + komunikat

---

## 📈 Planowane rozszerzenia backendu

- Link aktywacyjny e-mail (z tokenem)
- Panel admina (frontend) do zatwierdzania kont
- Per-user OpenAI API Key (`scope = userId`)
- Logika feedbacku „czy podobne zadanie pomogło”
- Ograniczenia użycia AI (np. dzienny limit zapytań)

---

## 📄 Powiązane dokumenty

- `project_overview.md`
- `services.md`
- `api_spec.md`
- `controllers.md`
- `validators.md`
- `middleware.md`
- `utils.md`
- `db_schema.md`
