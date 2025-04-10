# AI Task App – Realistyczny przegląd projektu (stan na podstawie kodu)

## 📘 Opis ogólny projektu

AI Task App to aplikacja webowa wspierana przez GPT-4o, której celem jest wspomaganie osób technicznych (programistów, administratorów IT, naukowców) w zarządzaniu zadaniami, dokumentowaniu problemów oraz wyszukiwaniu podobnych sytuacji z przeszłości. Architektura przewiduje osobne moduły dla backendu (Node.js/Express) i frontend (React), z integracją AI poprzez OpenAI API.

---

## 🎯 Cele techniczne (wg założeń)

- Rejestracja i logowanie użytkownika (JWT)
- Tworzenie zadań wspieranych przez GPT
- Przeszukiwanie historii zadań przez AI
- Frontendowy dashboard do zarządzania zadaniami
- Skalowalność dla zespołów i organizacji
- Przechowywanie danych w MongoDB (lokalnie i w chmurze)

---

## 📌 Faktyczny stan implementacji

### ✅ Backend:

- Zrealizowano:
  - `authController.js` – rejestracja i logowanie użytkownika (z JWT i bcrypt)
  - `authRoutes.js` – routing do logowania i rejestracji
  - `User.js` – model użytkownika z rolą
  - `Task.js` – model zadania z polami: opis, status, daty, notatki, termin (`dueDate`)
  - `taskController.js` – CRUD zadań + zamknięcie + tworzenie z AI (`createWithAI`)
  - `taskRoutes.js` – routing do obsługi zadań, w tym `/ai-create`
  - `services/gptService.js` – integracja z OpenAI API (GPT-4o)
  - `middleware/auth.js`, `validate.js` – autoryzacja i walidacja
  - `utils/responseHandler.js` – jednolity system odpowiedzi
  - `validators/taskValidator.js` – walidacja pól zadania
  - `prettier.config.js` – ujednolicenie stylu kodu w całym backendzie

- Brakuje:
  - semantycznego porównywania (`/api/ai/similar-tasks`)
  - frontendowego UI dla widoku tasków (planowane)

---

## ❌ Frontend:

- Frontend nie istnieje – brak plików React (`src/`, `components/`, `pages/`, itd.)
- Obecny jest tylko szkielet z `README.md`

---

## 🧠 Architektura logiczna

```
[ User (przeglądarka) ]
        ↓
[ Frontend – React ]        ← planowane
        ↓ axios/fetch
[ Backend – Express ]       ← pełna logika (auth, tasks, AI)
        ↓
[ MongoDB + GPT-4o API ]    ← działa od wersji 0.0.6
```

---

## 🛠️ Technologie (wdrożone)

- Backend: Node.js, Express, JWT, bcrypt, dotenv, express-validator
- Baza danych: MongoDB (lokalna i chmurowa)
- AI: OpenAI GPT-4o (`openai` SDK)
- Formatowanie: Prettier
- Frontend: planowany (React + Tailwind)

---

## 🚧 Roadmapa – porównanie planu z realizacją

| Funkcja                          | Planowane | Zrealizowane         |
|----------------------------------|-----------|----------------------|
| Rejestracja i logowanie (JWT)   | ✅         | ✅ pełne             |
| Tworzenie zadań z AI             | ✅         | ✅ (`POST /ai-create`) |
| Obsługa terminów wykonania       | ✅         | ✅                   |
| Przeszukiwanie historii (AI)     | ✅         | ❌ brak              |
| Frontend: dashboard              | ✅         | ❌ brak              |
| Middleware do ról i ochrony      | ✅         | 🟡 JWT działa        |
| Semantyczne porównanie           | ✅         | ❌ brak              |
| Klucz API + integracja GPT       | ✅         | ✅                   |

---

## 🔄 Historia wersji

### v0.0.6 – 2025-04-10

- Dodano integrację GPT-4o do backendu
- Nowy endpoint: `POST /api/tasks/ai-create`
- Moduł `gptService.js` i obsługa błędów OpenAI
- Formatowanie kodu z Prettier (`prettier.config.js`)
- Wersja gotowa do testów i dalszego rozwoju AI

---

## 📄 Dokumentacja

- `project_overview.md` – ogólny kontekst projektu i jego aktualny stan
- `backend_overview.md` – opis struktury backendu i przepływów logiki
- `api_spec.md` – specyfikacja endpointów REST API
- `validators.md`, `middleware.md`, `utils.md` – dokumentacja komponentów pomocniczych
- `project_roadmap.md` – status etapów implementacji
