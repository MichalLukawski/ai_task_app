# 🧠 Project Overview – AI Task App (zaktualizowana wersja)

AI Task App to inteligentny system do zarządzania zadaniami z wbudowaną obsługą sztucznej inteligencji, analizą semantyczną i zaawansowanym przepływem edycji. Celem projektu jest stworzenie platformy, która łączy prostotę zarządzania zadaniami z mocą modeli językowych (GPT-4o) w celu automatyzacji i wsparcia użytkownika w podejmowaniu decyzji.

---

## 🔧 Główne funkcje systemu

- Tworzenie i edycja zadań (ręczna oraz przez AI)
- Automatyczne zamykanie zadań z oceną `summary` przez GPT-4o
- Generowanie `embeddingów` i znajdowanie podobnych zadań
- Zapisywanie i szyfrowanie klucza OpenAI
- Uwierzytelnianie z JWT (logowanie, rejestracja)
- Standaryzowana struktura odpowiedzi i błędów API
- Edytowalna karta zadania z mechanizmem optymistycznej aktualizacji UI

---

## 📁 Struktura projektu (monorepo)

```
ai_task_app/
├── backend/    ← serwer Node.js + Express
├── frontend/   ← aplikacja React + Vite + Tailwind
└── docs/       ← dokumentacja markdown
```

---

## 📦 Stack technologiczny

- **Backend:** Node.js + Express + MongoDB (Mongoose)
- **Frontend:** React + Vite + TailwindCSS
- **AI:** OpenAI API (GPT-4o, text-embedding-3-small)
- **Autoryzacja:** JWT (`jsonwebtoken`) + `bcrypt`
- **Walidacja:** `express-validator`
- **Obsługa błędów:** `handleTryCatch`, `sendError`, `sendSuccess`

---

## 🔁 Przepływy główne

### 1. Tworzenie zadania z AI:

- Użytkownik podaje opis zadania
- Backend wykorzystuje `getTaskStructureFromAI()` (function calling)
- Zadanie otrzymuje `title`, `difficulty`, `dueDate`
- Tworzony jest `embedding` i przypisywane `similarTasks`
- Całość zapisywana jako `Task`

### 2. Zamykanie zadania z AI:

- Użytkownik wprowadza `summary`
- GPT-4o ocenia (`getSummaryAssessment`) lub poprawia (`improveSummary`)
- Walidowana jest długość i jakość podsumowania
- Zadanie zostaje zamknięte (`status = closed`)

### 3. Zamykanie przez kopiowanie:

- Użytkownik wybiera `sourceTaskId`
- Podsumowanie kopiowane 1:1
- Brak użycia AI

### 4. Edycja zadania:

- UI aktualizuje lokalny `editedTask`
- Zmiany widoczne natychmiast w karcie (nawet przed zapisem)
- `save()` wykonywane po kliknięciu poza kartę, Enter, itp.
- Wysyłany `PATCH` i aktualizacja listy zadań

---

## 🧱 Architektura backendu (skrót)

| Warstwa        | Opis                                     |
| -------------- | ---------------------------------------- |
| `routes/`      | Definicja tras API                       |
| `controllers/` | Logika wykonawcza                        |
| `services/`    | Połączenia z AI, generowanie embeddingów |
| `middleware/`  | JWT, walidacja, obsługa błędów           |
| `validators/`  | Spójne reguły danych wejściowych         |
| `utils/`       | Obsługa odpowiedzi, błędów               |

---

## 🧠 AI & Embeddingi

- Model: GPT-4o (function calling)
- Zadania AI:
  - Tworzenie struktury zadania (`create_task`)
  - Ocena podsumowania (`evaluate_summary`)
  - Wygładzanie języka (`improve_summary`)
- Embeddingi:
  - Generowane przez `text-embedding-3-small`
  - Porównywane przez `cosine similarity`
  - Przypisywane do `task.similarTasks`

---

## 🔐 OpenAI Key Management

- Endpoint: `POST /api/system/openai-key`
- Klucz szyfrowany AES-256-GCM
- Zapisywany w modelu `ApiKey`
- Odczyt i zapis przez `openaiKeyManager.js`
- Obsługa `rotatedAt`, `scope = "global"`

---

## 🔧 Refaktoryzacje backendu

- `handleTryCatch(fn)` – wrapper błędów `async` we wszystkich trasach
- Spójna struktura odpowiedzi (`status`, `message`, `data`)
- Standaryzacja folderów:
  - `gptService.function.js` → `gptService.js`
  - `systemController.js` dodany jako osobna jednostka
  - `authValidator.js` → walidacja loginu i rejestracji
- Wprowadzenie `sendError(code, message, status)`
- Middleware `auth.js` rozszerzony o `email`, `role`

---

## 🧑‍💻 Frontend – React Vite (skrót)

- `DashboardPage` pobiera i wyświetla zadania
- `TaskCard` zarządza stanem edycji (`useTaskCardState`)
- `TaskCardEdit` – komponent edytowalny
- `TaskCardView` – widok readonly z aktualnym stanem
- `editedTask` – lokalny obiekt stanu (optymistyczna aktualizacja)
- `useApi()` – abstrakcja nad `axios` (GET, POST, PATCH)

---

## 🔍 Status projektu

- ✅ Backend zrefaktoryzowany i ustandaryzowany
- ✅ Frontend obsługuje widoki i edycję
- ✅ Integracja AI i embeddingów działa
- 🔄 Trwa rozbudowa edytora i systemu ról
- 📄 Dokumentacja kompletna (controllers, routes, middleware, validators, API)

---

## 📄 Powiązane dokumenty

- `controllers.md`, `routes.md`, `middleware.md`, `validators.md`, `services.md`
- `api_spec.md`, `project_roadmap.md`, `task_flow.md`, `auth_flow.md`
- `CHANGELOG.md`, `README.md`
