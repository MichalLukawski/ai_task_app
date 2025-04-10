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
  - `authController.js` – rejestracja i logowanie użytkownika (JWT i bcrypt)
  - `taskController.js` – CRUD zadań + zamknięcie + tworzenie z AI (`createWithAI`)
  - `gptService.js` – generowanie struktury zadania (JSON), fallback, logowanie
  - `utils/logger.js` – logowanie błędów GPT (`logs/gpt_fallbacks.log`)
  - `taskRoutes.js` – routing dla zadań, w tym `/ai-create`
  - `services/`, `middleware/`, `validators/` – modularna architektura
  - Obsługa daty i oczyszczania markdown z odpowiedzi GPT

- W planach:
  - `difficulty` (ocena trudności zadania przez GPT)
  - `similar-tasks` (embedding + porównanie przez cosine similarity)
  - Generowanie podsumowań przy zamykaniu zadania

---

### ❌ Frontend:

- Brak kodu źródłowego frontendu (tylko szkielet i dokumentacja)
- Brakuje UI do:
  - Tworzenia/edycji zadań
  - Podglądu podobnych zadań
  - Weryfikacji odpowiedzi GPT
  - Obsługi logowania JWT

---

## 🧠 Architektura logiczna

```
[ User (przeglądarka) ]
        ↓
[ Frontend – React ]        ← planowane
        ↓ axios/fetch
[ Backend – Express ]       ← pełna logika (auth, tasks, AI, fallback)
        ↓
[ MongoDB + GPT-4o API + Logs ]
```

---

## 🛠️ Technologie (wdrożone)

- Backend: Node.js, Express, JWT, bcrypt, dotenv, express-validator
- Baza danych: MongoDB (lokalna i chmurowa)
- AI: OpenAI GPT-4o (`openai` SDK) + logika JSON/fallback
- Logging: `logs/gpt_fallbacks.log`
- Formatowanie: Prettier
- Frontend: planowany (React + Tailwind)

---

## 🚧 Roadmapa – porównanie planu z realizacją

| Funkcja                          | Planowane | Zrealizowane         |
|----------------------------------|-----------|----------------------|
| Rejestracja i logowanie (JWT)   | ✅         | ✅                   |
| Tworzenie zadań z AI             | ✅         | ✅ (`POST /ai-create`) |
| Obsługa terminów wykonania       | ✅         | ✅                   |
| Fallback + log błędnych JSON GPT | ❌         | ✅ nowość v0.0.7     |
| Przeszukiwanie historii (AI)     | ✅         | 🔄 w planach         |
| Ocena trudności (`difficulty`)   | ✅         | ❌ planowane         |
| Frontend: dashboard              | ✅         | ❌ brak              |

---

## 🔄 Historia wersji

### v0.0.7 – 2025-04-10

- GPT zwraca strukturę zadania w formacie JSON
- Obsługa błędnego JSON (`try/catch`) → fallback do `notes`
- Logowanie błędów do `logs/gpt_fallbacks.log`
- Rozpoczęcie przygotowań do embeddingów i porównań semantycznych

---

## 📄 Dokumentacja

- `project_roadmap.md`
- `backend_overview.md`
- `api_spec.md`
- `utils.md`
- `services.md`
- `ai_integration.md`
