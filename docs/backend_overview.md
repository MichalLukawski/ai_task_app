# AI Task App – Backend Overview

## 🔧 Technologie

- Node.js + Express
- MongoDB + Mongoose
- JWT (autoryzacja)
- Bcrypt (hashowanie haseł)
- Dotenv (zmienne środowiskowe)
- CORS, middleware, modularna architektura
- OpenAI (GPT-4o)
- Prettier

---

## 🏗️ Struktura katalogów backendu

```
backend/
├── config/           # Konfiguracja połączenia z MongoDB
├── controllers/      # Logika endpointów: authController, taskController
├── models/           # Schematy danych: User, Task
├── routes/           # Endpointy API: authRoutes, taskRoutes
├── middleware/       # Obsługa JWT (auth.js), walidacja (validate.js)
├── services/         # Integracja z GPT: gptService.js
├── utils/            # Funkcje pomocnicze: responseHandler, logger
├── validators/       # Walidacja pól (express-validator)
├── logs/             # Logi diagnostyczne, np. fallbacki GPT
├── prettier.config.js# Konfiguracja formatowania kodu
├── .env              # Zmienne środowiskowe (lokalne)
└── server.js         # Główna aplikacja Express
```

---

## 📦 Endpointy (zrealizowane)

| Metoda | Endpoint                 | Opis                                               |
|--------|--------------------------|----------------------------------------------------|
| POST   | /api/auth/register       | Rejestracja użytkownika                            |
| POST   | /api/auth/login          | Logowanie i zwrot tokena JWT                       |
| POST   | /api/tasks               | Tworzenie zadania ręcznie                          |
| GET    | /api/tasks               | Lista zadań użytkownika                            |
| PUT    | /api/tasks/:id           | Edycja zadania                                     |
| POST   | /api/tasks/:id/close     | Zamykanie zadania (status + closedAt)             |
| POST   | /api/tasks/ai-create     | Tworzenie zadania z pomocą GPT-4o                 |

---

## 🧠 Integracja AI – GPT-4o

- Moduł `gptService.js` obsługuje generowanie struktury zadania w formacie JSON
- Używany model: `gpt-4o`, prompt uwzględnia bieżącą datę
- Czyszczenie odpowiedzi z markdown
- Obsługa błędów JSON (`JSON.parse`)
- **Fallback:** odpowiedź zapisywana jako `notes`, log do `logs/gpt_fallbacks.log`
- Funkcja pomocnicza `logGPTFallback()` zapisuje błędy do logu

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
- `logger.js` – `logGPTFallback()`:
  - Zapisywanie nieparsowalnych odpowiedzi GPT do `logs/gpt_fallbacks.log`

---

## 🔗 Połączenie z MongoDB

- Plik `config/db.js`
- `mongoose.connect(process.env.MONGO_URI)`
- Serwer startuje po udanym połączeniu

---

## 🧪 Testy (planowane)

- Testy jednostkowe z użyciem `Jest` + `Supertest`
- Mockowanie MongoDB (np. z `mongodb-memory-server`)
- Testy: rejestracja, logowanie, CRUD zadań, AI

---

## 📄 Dokumentacja powiązana

- `project_overview.md`
- `api_spec.md`
- `controllers.md`
- `middleware.md`
- `utils.md`
- `services.md`
- `validators.md`
