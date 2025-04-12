# 🧱 AI Task App – Backend Overview (wersja rozszerzona)

Backend AI Task App stanowi serce aplikacji – odpowiada za przetwarzanie logiki biznesowej, integrację z GPT-4o i modelami embeddingowymi, walidację danych wejściowych oraz kontrolę nad danymi użytkownika i zadań. Poniższy dokument stanowi pełny przegląd architektury, funkcjonalności oraz technologii wykorzystanych po stronie serwera.

---

## 🧩 Główne odpowiedzialności backendu

- Autoryzacja użytkowników i zarządzanie kontem (`User`)
- Operacje CRUD na zadaniach (`Task`)
- Obsługa zamykania zadań z pomocą AI lub przez kopiowanie rozwiązań
- Integracja z modelem GPT-4o i embeddingami OpenAI
- Porównywanie podobnych zadań
- Generowanie podsumowań i ocena ich jakości
- Walidacja danych wejściowych i obsługa błędów

---

## 🧰 Technologie i biblioteki

- **Node.js** – platforma uruchomieniowa
- **Express.js** – framework serwerowy
- **MongoDB** – nierelacyjna baza danych
- **Mongoose** – ODM do MongoDB
- **OpenAI SDK** – integracja z GPT-4o i embeddingami
- **express-validator** – walidacja danych z żądań HTTP
- **JWT** – uwierzytelnianie użytkowników
- **bcrypt** – hashowanie haseł
- **dotenv** – zarządzanie zmiennymi środowiskowymi
- **Prettier** – formatowanie kodu źródłowego

---

## 📁 Struktura katalogów

```
backend/
├── config/             # Połączenie z MongoDB
├── controllers/        # Logika tras API: authController.js, taskController.js
├── middleware/         # Middleware: auth.js (JWT), validate.js
├── models/             # Schematy danych: User.js, Task.js
├── routes/             # Routing: authRoutes.js, taskRoutes.js
├── services/           # Integracja z GPT-4o i embeddingami
│   ├── gptService.function.js
│   ├── aiSummaryService.js
│   └── embeddingService.js
├── utils/              # responseHandler.js – spójna obsługa odpowiedzi
├── validators/         # Walidacja: taskValidator.js
└── server.js           # Punkt wejścia aplikacji
```

---

## 📦 Modele danych

### 🔹 User

- `email`, `password` (haszowany)
- `role` (domyślnie: `user`)
- `createdAt`

### 🔹 Task

- `title`, `description`, `dueDate`, `difficulty`
- `summary` – wygenerowany przez AI lub skopiowany
- `status` – `"open"` lub `"closed"`
- `closedAt`, `createdAt`
- `ownerId` – użytkownik przypisany do zadania
- `embedding` – wektor reprezentacji semantycznej
- `similarTasks` – ID podobnych zadań (wyliczane automatycznie)

---

## 🔗 Integracja z OpenAI

### 🔸 `gptService.function.js`

- Obsługa function calling:
  - `create_task` – tworzenie struktury zadania
  - `assess_summary` – ocena jakości opisu
  - `improve_summary` – wygładzanie języka

### 🔸 `aiSummaryService.js`

- Funkcja `processTaskClosure()`:
  - Ocena długości i jakości `summary`
  - Odrzucenie zbyt krótkiego lub słabego opisu (chyba że `force: true`)
  - Użycie `improveSummary()` do wygładzania zaakceptowanych podsumowań

---

## 🔍 Embeddingi i porównywanie zadań

### 🔸 `embeddingService.js`

- Wykorzystuje model OpenAI `text-embedding-3-small`
- Wygenerowany embedding (`Float[]`) zapisywany w zadaniu
- Porównanie cosine similarity z embeddingami zadań zakończonych
- Zadania o podobieństwie ≥ 0.75 są przypisywane do `similarTasks` (maksymalnie 5)

---

## 🔐 Autoryzacja i bezpieczeństwo

- Token JWT generowany przy logowaniu
- Middleware `auth.js` sprawdza poprawność tokena i ustawia `req.user`
- Zadania dostępne wyłącznie dla właściciela (`ownerId`)
- Hasła użytkowników są haszowane (`bcrypt`)

---

## 📑 Walidacja danych

- Każdy endpoint posiada przypisany walidator (np. `validateTaskInput`, `validateUpdateTaskInput`, `validateCreateTaskWithAI`)
- Obsługa błędów walidacji przez `validate.js` i `responseHandler.js`
- Walidacja zgodna z metodą HTTP: `POST` (wymaga pól), `PATCH` (pola opcjonalne)
- Zabezpieczenie przed niewłaściwym użyciem endpointów (`summary` tylko dla AI)

---

## ✍️ Endpointy API

| Metoda | Endpoint                  | Opis                                                  |
| ------ | ------------------------- | ----------------------------------------------------- |
| POST   | `/api/auth/register`      | Rejestracja użytkownika                               |
| POST   | `/api/auth/login`         | Logowanie i JWT                                       |
| POST   | `/api/tasks`              | Tworzenie zadania ręcznie                             |
| POST   | `/api/tasks/ai-create`    | Tworzenie zadania z pomocą GPT-4o                     |
| PATCH  | `/api/tasks/:id`          | Częściowa edycja zadania                              |
| PATCH  | `/api/tasks/:id/ai-close` | Zamykanie z pomocą AI – ocena + wygładzenie `summary` |
| PATCH  | `/api/tasks/:id/close`    | Zamykanie przez kopiowanie `summary` z innego zadania |
| GET    | `/api/tasks`              | Lista zadań użytkownika                               |

---

## 🧪 Testowanie i jakość kodu

- Planowane pokrycie testami: `jest` + `supertest`
- Oddzielne testy dla walidatorów, integracji z AI oraz logiki zamykania
- Mockowanie MongoDB i odpowiedzi OpenAI

---

## 🔁 Formatowanie i styl

- Prettier (`prettier.config.js`) – automatyczne formatowanie kodu
- Jednolity styl kodu we wszystkich plikach
- Obsługa błędów za pomocą `sendSuccess` i `sendError`

---

## 🧹 Formatowanie kodu

- Plik `prettier.config.js` definiuje styl kodu backendu
- Formatowanie wykonywane przez `npm run format`

---

## 🧩 Middleware

- `auth.js` – middleware JWT: sprawdza `Authorization: Bearer`, dekoduje `req.user`
- `validate.js` – obsługa błędów walidacji z `express-validator`

---

## 🧰 Utils

- `responseHandler.js` – funkcje `sendSuccess` i `sendError`:
  - Ujednolicone odpowiedzi API w całej aplikacji

---

## 🔗 Połączenie z MongoDB

- Plik `config/db.js`
- `mongoose.connect(process.env.MONGO_URI)`
- Serwer startuje po udanym połączeniu

---

## 🚀 Gotowość do rozwoju

- Kod backendu jest gotowy do integracji z frontendem React
- API w pełni rozdzielone: AI (`/ai-close`) vs manualne (`/close`)
- Umożliwia budowę bazy wiedzy użytkownika opartej na embeddingach
- Gotowe fundamenty pod: role, organizacje, webhooki, eksport danych, statystyki

## 📄 Dokumentacja powiązana

- `project_overview.md`
- `api_spec.md`
- `controllers.md`
- `middleware.md`
- `utils.md`
- `services.md`
- `validators.md`
