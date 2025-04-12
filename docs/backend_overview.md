# AI Task App – Backend Overview

## 🔧 Technologie

- Node.js + Express
- MongoDB + Mongoose
- JWT (autoryzacja)
- Bcrypt (hashowanie haseł)
- Dotenv (zmienne środowiskowe)
- CORS, middleware, modularna architektura
- OpenAI (GPT-4o + embeddings)
- Prettier

---

## 🏗️ Struktura katalogów backendu

```
backend/
├── config/             # Konfiguracja połączenia z MongoDB
├── controllers/        # Logika endpointów: authController, taskController
├── models/             # Schematy danych: User, Task
├── routes/             # Endpointy API: authRoutes, taskRoutes
├── middleware/         # Obsługa JWT (auth.js), walidacja (validate.js)
├── services/           # Integracja AI: gptService.function.js, aiSummaryService.js, embeddingService.js
├── utils/              # Funkcje pomocnicze: responseHandler
├── validators/         # Walidacja pól (express-validator)
├── prettier.config.js  # Konfiguracja formatowania kodu
├── .env                # Zmienne środowiskowe (lokalne)
└── server.js           # Główna aplikacja Express
```

---

## 📦 Endpointy (zrealizowane)

| Metoda | Endpoint                | Opis                                                         |
| ------ | ----------------------- | ------------------------------------------------------------ |
| POST   | /api/auth/register      | Rejestracja użytkownika                                      |
| POST   | /api/auth/login         | Logowanie i zwrot tokena JWT                                 |
| POST   | /api/tasks              | Tworzenie zadania ręcznie                                    |
| GET    | /api/tasks              | Lista zadań użytkownika                                      |
| PUT    | /api/tasks/:id          | Edycja zadania                                               |
| POST   | /api/tasks/ai-create    | Tworzenie zadania z pomocą GPT-4o (function call)            |
| POST   | /api/tasks/:id/ai-close | Zamykanie zadania (AI: ocena summary, force, lub kopiowanie) |

---

## 🧠 Integracja AI – GPT-4o + embeddings

- `gptService.function.js` wykorzystuje function calling (`create_task`, `assess_summary`, `improve_summary`)
- `aiSummaryService.js` obsługuje scenariusze zamykania zadania (opis własny, force, kopiowanie)
- `embeddingService.js` generuje embedding (`text-embedding-3-small`), porównuje z zakończonymi zadaniami
- Top 5 podobnych (`similarity >= 0.75`) przypisywane do `similarTasks`
- AI nigdy nie generuje `summary` automatycznie — użytkownik musi podać dane

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

## 🧪 Testy (planowane)

- Testy jednostkowe z użyciem `Jest` + `Supertest`
- Mockowanie MongoDB (np. z `mongodb-memory-server`)
- Testy: rejestracja, logowanie, CRUD zadań, AI + embeddingi

---

## 📄 Dokumentacja powiązana

- `project_overview.md`
- `api_spec.md`
- `controllers.md`
- `middleware.md`
- `utils.md`
- `services.md`
- `validators.md`
