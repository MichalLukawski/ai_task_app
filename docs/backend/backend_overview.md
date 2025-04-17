# 🧱 Backend Overview – AI Task App (zaktualizowana wersja)

Backend aplikacji AI Task App zbudowany jest w oparciu o architekturę REST API, modularną strukturę folderów i konwencje projektowe zapewniające przejrzystość, łatwą rozbudowę i pełną izolację logiki domenowej. Backend pełni rolę API serwera i integratora warstw: frontendowej, bazy danych oraz zewnętrznego API OpenAI.

---

## 🔧 Technologie i zależności główne

- **Node.js + Express** – główna platforma serwerowa
- **MongoDB (Mongoose)** – baza danych dokumentowa
- **JWT (`jsonwebtoken`)** – autoryzacja
- **bcrypt** – szyfrowanie haseł
- **OpenAI API** – integracja GPT-4o i embeddingów
- **express-validator** – walidacja danych wejściowych
- **dotenv** – konfiguracja zmiennych środowiskowych
- **crypto** – szyfrowanie klucza OpenAI (AES-256-GCM)
- **axios** – opcjonalne zapytania z backendu do zewnętrznych API

---

## 📁 Struktura katalogów backendu

```
backend/
├── controllers/         # Logika wykonawcza dla tras API
├── routes/              # Deklaracja tras i middleware
├── services/            # Warstwa domenowa – AI, embeddingi, klucze
├── middleware/          # Uwierzytelnianie, walidacja, błędy
├── validators/          # Definicje walidacji danych wejściowych
├── models/              # Schematy Mongoose
├── utils/               # Uniwersalne funkcje wspierające
├── config/              # Konfiguracja środowiska (opcjonalnie)
└── server.js            # Główny plik uruchomieniowy Express
```

---

## ⚙️ Moduły backendowe i odpowiedzialności

### `controllers/`

- `authController.js` – logowanie i rejestracja użytkowników
- `taskController.js` – obsługa zadań: tworzenie, edycja, zamykanie
- `systemController.js` – konfiguracja klucza OpenAI
- Wszystkie funkcje używają `sendSuccess` / `sendError`
- Obsługa błędów przez `handleTryCatch(...)` (brak `try/catch` lokalnych)
- Funkcja `createTaskWithAI` przetwarza `POST /tasks/ai-create` poprzez GPT

---

### `routes/`

- Trasy podzielone tematycznie:
  - `authRoutes.js`
  - `taskRoutes.js`
  - `systemRoutes.js`
- Middleware:
  - `auth` (JWT)
  - `validate*` (walidatory `express-validator`)
- Wszystkie funkcje opakowane w `handleTryCatch(...)`

---

### `services/`

- `gptService.js` – low-level połączenie z GPT-4o (z `function_calling`)
  - funkcje: `getTaskStructureFromAI`, `getSummaryAssessment`, `improveSummary`
  - `getTaskStructureFromAI()` analizuje i waliduje dane z GPT, korzysta z `JSON.parse(...)` do odczytu argumentów
- `aiSummaryService.js` – logika zamykania zadań przy użyciu AI
  - funkcja `processTaskClosure()` kontroluje przepływ od oceny `summary` po jego poprawę stylistyczną
- `embeddingService.js` – generowanie i porównywanie embeddingów
  - `generateEmbedding`, `findSimilarTasks`, `generateAndAttachEmbedding`
- `openaiKeyManager.js` – szyfrowanie, zapis i odczyt klucza OpenAI
  - AES-256-GCM, obsługa `rotatedAt` i `scope`

> Warstwa usług zawiera wyłącznie logikę domenową – żadnej logiki HTTP, tras, odpowiedzi, res/req.

---

### `middleware/`

- `auth.js` – walidacja tokena JWT, przypisanie `req.user`
- `validate.js` – sprawdzenie błędów z `express-validator`
- `errorHandler.js` – globalna obsługa błędów (opcjonalnie)

---

### `validators/`

- `authValidator.js` – walidacja loginu i rejestracji
- `taskValidator.js` – tworzenie, edycja, zamykanie zadań
- Walidacja wspierana przez `validate.js` – zwraca błąd `VALIDATION_ERROR`

---

### `models/`

- `Task` – podstawowa jednostka danych:
  - `title`, `description`, `status`, `difficulty`, `dueDate`
  - `summary`, `embedding[]`, `similarTasks[]`, `ownerId`
- `User` – zawiera `email`, `password` (haszowany), role (na przyszłość)
- `ApiKey` – przechowuje zaszyfrowany klucz OpenAI + daty rotacji

---

### `utils/`

- `responseHandler.js`:
  - `sendSuccess(res, msg, data)`
  - `sendError(res, msg, status, code)`
  - `handleTryCatch(fn)` – obsługa `async/await` błędów
- Planowane: `formatDate`, `logObject`, `parseAIResponse`

---

## 🔐 Autoryzacja

- JWT przekazywany w nagłówku `Authorization`
- Token zawiera tylko `id` użytkownika
- Użytkownik dołączany do `req.user` (z `email`, `role`)
- Autoryzacja obowiązuje dla wszystkich tras poza `/auth/*`

---

## 🤖 Integracja z AI (OpenAI)

- Model: `gpt-4o`
- Wykorzystanie `function_calling` z parametrem `tool_choice: required`
- Scenariusze:
  - `create_task` – tworzenie struktury zadania z opisu
  - `assess_summary` – ocena jakości `summary`
  - `improve_summary` – poprawa stylistyki

Funkcje zostały rozdzielone, a ich wywołanie determinowane jest nazwą funkcji w polu `tool_calls`. Zwracana zawartość `arguments` jest zawsze parsowana przez `JSON.parse()`.

---

## 📊 Embeddingi i porównywanie

- Model: `text-embedding-3-small`
- Generowanie embeddingów przez `generateEmbedding(text)`
- Porównanie: `findSimilarTasks(newEmbedding)` – `cosine similarity`
- Próg: 0.75
- Zwracane maksymalnie 5 podobnych zadań (`similarTasks`)
- Dane zapisywane do: `task.embedding`, `task.similarTasks`

---

## 🛡️ Obsługa klucza OpenAI

- Szyfrowanie: AES-256-GCM (moduł `crypto`)
- Przechowywanie zaszyfrowanego klucza w kolekcji `ApiKey`
- Dekodowanie możliwe tylko przy obecności `SECRET_ENCRYPTION_KEY`
- Fallback: `OPENAI_API_KEY` z `.env`
- Endpoint do zarządzania: `POST /api/system/openai-key`
- Pola w bazie:
  - `scope`, `encryptedKey`, `iv`, `tag`, `rotatedAt`

---

## 🧩 Błędy i odpowiedzi

- `sendSuccess(...)` – ujednolicony format odpowiedzi
- `sendError(...)` – obsługa kodów błędów, własne `code`
- `handleTryCatch(...)` – pełna eliminacja try/catch w kodzie kontrolerów
- Kody błędów:
  - `VALIDATION_ERROR`
  - `NO_TOKEN`
  - `INVALID_TOKEN`
  - `SUMMARY_TOO_SHORT`
  - `EMBEDDING_ERROR`
  - `MISSING_TITLE_FROM_GPT`

---

## 🧠 Przykład przepływu: zamykanie zadania z AI

1. Użytkownik wpisuje `summary` i zatwierdza
2. Wywoływane jest `PATCH /api/tasks/:id/ai-close`
3. Walidacja `summary` i `force`
4. `aiSummaryService.processTaskClosure(...)`
5. Ocena jakości tekstu → poprawa stylistyki → zapis
6. Task otrzymuje `summary`, `status = closed`

---

## 🔁 Komunikacja z frontendem

- Wszystkie odpowiedzi: `status`, `message`, `data`
- Brak `statusCode` – kod HTTP w nagłówku
- Frontend przekazuje `token` przez `Authorization`
- W przypadku `PATCH`/`POST` → frontend wykonuje `GET` dla synchronizacji danych (`refetchAfterSave`)
- Backend zwraca zawsze aktualny stan zadania po zapisaniu

---

## 📄 Dokumentacja uzupełniająca

- `controllers.md` – opis logiki funkcji
- `routes.md` – mapa tras i middleware
- `services.md` – logika AI i embeddingów
- `validators.md`, `utils.md`, `api_spec.md`
