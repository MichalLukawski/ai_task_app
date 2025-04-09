# AI Task App – Backend Overview

## 🔧 Technologie

- Node.js + Express
- MongoDB + Mongoose
- JWT (autoryzacja)
- Bcrypt (hashowanie haseł)
- Dotenv (zmienne środowiskowe)
- CORS, middleware, modularna architektura

---

## 🏗️ Struktura katalogów backendu

```
backend/
├── config/           # Konfiguracja połączenia z MongoDB
├── controllers/      # Logika endpointów: authController, taskController
├── models/           # Schematy danych: User, Task
├── routes/           # Endpointy API: authRoutes, taskRoutes
├── middleware/       # Obsługa JWT (auth.js), walidacja (planowana)
├── utils/            # Funkcje pomocnicze: responseHandler
├── .env              # Zmienne środowiskowe (lokalne)
├── server.js         # Główna aplikacja Express
└── .gitignore
```

---

## 📦 Endpointy (zrealizowane)

| Metoda | Endpoint                 | Opis                          |
|--------|--------------------------|-------------------------------|
| POST   | /api/auth/register       | Rejestracja użytkownika       |
| POST   | /api/auth/login          | Logowanie i zwrot tokena JWT |
| POST   | /api/tasks               | Tworzenie zadania             |
| GET    | /api/tasks               | Lista zadań użytkownika       |
| PUT    | /api/tasks/:id           | Edycja zadania                |
| POST   | /api/tasks/:id/close     | Zamykanie zadania (AI: plan)  |

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

## 🧩 Middleware

- `auth.js` – middleware JWT: sprawdza `Authorization: Bearer`, dekoduje `req.user`
- `validate.js` – planowany: walidacja danych wejściowych (`express-validator`)
- `requireRole.js` – planowany: kontrola ról (admin/user)

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

## 🔐 Autoryzacja

- JWT generowane przy logowaniu (`/api/auth/login`)
- Token wymagany w trasach `/api/tasks`
- Token przesyłany w nagłówku: `Authorization: Bearer <token>`

---

## 🧪 Testy (planowane)

- Testy jednostkowe z użyciem `Jest` + `Supertest`
- Mockowanie MongoDB (np. z `mongodb-memory-server`)
- Testy: rejestracja, logowanie, CRUD zadań

---

## 📄 Dokumentacja powiązana

- `project_overview.md` – ogólny kontekst projektu i status implementacji
- `api_spec.md` – specyfikacja REST API (auth, tasks, AI)
- `utils.md` – dokumentacja `sendSuccess` / `sendError`
- `middleware.md` – opis middleware JWT i planowane walidacje
- `controllers.md` – opis logiki endpointów (auth, tasks)
- `project_roadmap.md` – etapy rozwoju backendu i dalsze plany

