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
├── controllers/      # Logika endpointów
├── models/           # Schematy danych (np. User)
├── routes/           # Endpointy API
├── utils/            # Funkcje pomocnicze (np. responseHandler)
├── .env              # Zmienne środowiskowe (lokalne)
├── server.js         # Główna aplikacja Express
└── .gitignore
```

---

## 📦 Endpointy (aktualne)

| Metoda | Endpoint              | Opis                         |
|--------|------------------------|------------------------------|
| POST   | /api/auth/register     | Rejestracja użytkownika      |
| (plan) | /api/auth/login        | Logowanie i JWT              |
| (plan) | /api/tasks             | Tworzenie zadania            |
| (plan) | /api/tasks/:id         | Edycja / zamknięcie zadania  |

---

## 🔐 Rejestracja użytkownika

- Endpoint: `POST /api/auth/register`
- Proces:
  1. Sprawdzenie, czy e-mail już istnieje
  2. Hashowanie hasła (`bcrypt`)
  3. Zapis do kolekcji `users`
  4. Odpowiedź JSON (`success` / `error`)

- Hasło hashowane w `UserSchema.pre('save')`
- Komunikaty spójne dzięki `sendSuccess()` / `sendError()` z `utils/responseHandler.js`

---

## ⚙️ Middleware (planowane)

- `requireAuth` – autoryzacja tokenem JWT
- `requireRole('admin')` – kontrola ról
- `validateInput` – walidacja danych wejściowych

---

## 🔗 Połączenie z MongoDB

Zrealizowane w pliku `config/db.js`:

- Użycie `mongoose.connect(process.env.MONGO_URI)`
- W razie błędu: `process.exit(1)`
- Serwer startuje dopiero po poprawnym połączeniu

---

## 🔒 Autoryzacja (planowana)

- JWT podpisywany kluczem z `.env`
- Token przesyłany w nagłówku `Authorization: Bearer`
- Middleware `requireAuth` będzie weryfikował token i dodawał `req.user`

---

## 🧪 Testy (planowane)

- Framework: Jest + Supertest
- Testy rejestracji, logowania, endpointów zadań
- Osobna baza danych testowa (np. `ai-task-app-test`)

---

## 📄 Dokumentacja powiązana

- `project_overview.md` – pełny kontekst projektu, cele, architektura, repozytoria, AI, modularność
- `backend_overview.md` – opis struktury backendu, endpointów, technologii i modelu autoryzacji
- `frontend_overview.md` – opis frontendu, komponentów, architektury, interfejsów użytkownika
- `api_spec.md` – specyfikacja endpointów REST API (auth, tasks, AI), dane wejściowe/wyjściowe
- `ai_integration.md` – jak GPT-4 wspiera zadania: tworzenie, ocena, zamykanie, priorytetyzacja
- `project_roadmap.md` – roadmapa projektu: fazy rozwoju, MVP, AI, skalowanie, funkcje zespołowe