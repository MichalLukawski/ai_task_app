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
├── utils/            # Funkcje pomocnicze: responseHandler
├── validators/       # Walidacja pól (express-validator)
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

## 🔐 Rejestracja i logowanie

- Rejestracja:
  - Sprawdzenie, czy email istnieje
  - Hashowanie hasła (`bcrypt`)
  - Zapis do `User`
  - Odpowiedź: `sendSuccess` lub `sendError`

- Logowanie:
  - Weryfikacja danych
  - Generowanie JWT (`jsonwebtoken`)
  - Middleware sprawdzający token (`auth.js`)

---

## 🧠 Integracja AI – GPT-4o

- Nowy plik `services/gptService.js`
- Obsługuje endpoint `POST /api/tasks/ai-create`
- Korzysta z modelu `gpt-4o` do wygenerowania notatki (`notes`)
- Obsługuje błędy API, walidację promptu
- Konfigurowany przez `OPENAI_API_KEY` w `.env`

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
  - Wspiera kodowanie komunikatów i statusów błędów

---

## 🔗 Połączenie z MongoDB

W pliku `config/db.js`:
- `mongoose.connect(process.env.MONGO_URI)`
- W razie błędu: `process.exit(1)`
- Serwer startuje dopiero po połączeniu z bazą

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
- `validators.md`
- `project_roadmap.md`
