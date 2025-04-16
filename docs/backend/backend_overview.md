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

- `gptService.js` – low-level połączenie z GPT-4o
- `aiSummaryService.js` – logika zamykania zadań przy użyciu AI
- `embeddingService.js` – generowanie i porównywanie embeddingów
- `openaiKeyManager.js` – szyfrowanie, zapis i odczyt klucza OpenAI

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
- Spójne komunikaty walidacyjne
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

## 🤖 Integracja z AI

- Model: `gpt-4o`, `function_calling`, `tool_choice: required`
- Scenariusze:
  - Tworzenie zadania (`create_task`)
  - Ocena `summary` (`evaluate_summary`)
  - Poprawa stylistyki (`improve_summary`)
- Obsługa przez `gptService.js`, wykorzystywane przez `aiSummaryService`

---

## 📊 Embeddingi i porównywanie

- Wykorzystanie `text-embedding-3-small` (OpenAI)
- Generowanie z połączenia `title + description`
- Porównywanie `cosine similarity`
- Próg podobieństwa: 0.75
- Maksymalnie 5 podobnych zadań
- Zapis do `task.embedding` i `task.similarTasks`

---

## 🛡️ Obsługa klucza OpenAI

- Szyfrowanie: AES-256-GCM (z `crypto`)
- Klucz deszyfrowany tylko przy użyciu `SECRET_ENCRYPTION_KEY`
- Endpoint: `POST /api/system/openai-key`
- Wsparcie dla `scope` i daty `rotatedAt`

---

## 🧩 Błędy i odpowiedzi

- `sendSuccess(...)` – ujednolicony format odpowiedzi
- `sendError(...)` – obsługa kodów błędów, własne `code`
- `handleTryCatch(...)` – pełna eliminacja try/catch w kodzie kontrolerów
- Przykłady błędów:
  - `VALIDATION_ERROR`
  - `NO_TOKEN`
  - `INVALID_TOKEN`
  - `SUMMARY_TOO_SHORT`
  - `EMBEDDING_ERROR`

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
- Frontend odbiera dane i aktualizuje lokalny state (`onTaskUpdated`)
- Frontend przekazuje `token` przez `Authorization`

---

## 📄 Dokumentacja uzupełniająca

- `controllers.md` – opis logiki funkcji
- `routes.md` – mapa tras i middleware
- `services.md` – logika AI i embeddingów
- `validators.md`, `utils.md`, `api_spec.md`
