# AI Task App – Realistyczny przegląd projektu (aktualizacja: 2025-04-11)

## 📘 Opis ogólny projektu

AI Task App to aplikacja webowa wspierana przez GPT-4o, której celem jest wspomaganie osób technicznych (programistów, administratorów IT, naukowców) w zarządzaniu zadaniami, dokumentowaniu problemów oraz wyszukiwaniu podobnych sytuacji z przeszłości. Architektura przewiduje osobne moduły dla backendu (Node.js/Express) i frontend (React), z integracją AI poprzez OpenAI API.

---

## 🎯 Cele techniczne (wg założeń)

- Rejestracja i logowanie użytkownika (JWT)
- Tworzenie zadań wspieranych przez GPT (function calling)
- Porównywanie zadań przez embeddingi
- Dashboard do zarządzania zadaniami
- Skalowalność dla zespołów i organizacji
- Przechowywanie danych w MongoDB (lokalnie i w chmurze)

---

## 📌 Faktyczny stan implementacji

### ✅ Backend:

- Zrealizowano:

  - `authController.js` – rejestracja i logowanie użytkownika (JWT i bcrypt)
  - `taskController.js` – CRUD zadań + zamykanie + tworzenie z AI (`createWithAI`)
  - `gptService.function.js` – generowanie struktury zadania przez GPT-4o (function calling)
  - `embeddingService.js` – generowanie embeddingów i wyszukiwanie podobnych zadań
  - `taskRoutes.js` – routing dla zadań, w tym `/ai-create`
  - `utils/responseHandler.js` – spójna obsługa odpowiedzi API
  - `services/`, `middleware/`, `validators/` – modularna architektura
  - Obsługa pola `difficulty` generowanego przez AI
  - Obsługa `similarTasks` przypisywanych automatycznie (jeśli similarity >= 0.75)

- W planach:
  - Zamknięcie zadania z podsumowaniem generowanym przez AI (`summary`)
  - Zatwierdzanie podobieństw (`similarTasks`) ręcznie przez użytkownika

---

### ❌ Frontend:

- Brak kodu źródłowego frontendu (tylko struktura projektu)
- Brakuje UI do:
  - Tworzenia i edycji zadań
  - Podglądu podobnych zadań
  - Zamykania zadania z pomocą AI
  - Obsługi logowania i tokenów JWT

---

## 🧠 Architektura logiczna

```
[ User (przeglądarka) ]
        ↓
[ Frontend – React ]        ← planowane
        ↓ axios/fetch
[ Backend – Express ]       ← pełna logika (auth, tasks, AI, embeddings)
        ↓
[ MongoDB + OpenAI (GPT + embeddings) ]
```

---

## 🛠️ Technologie (wdrożone)

- Backend: Node.js, Express, JWT, bcrypt, dotenv, express-validator
- Baza danych: MongoDB (lokalna i chmurowa)
- AI: OpenAI GPT-4o (`openai` SDK) + `text-embedding-3-small`
- Formatowanie: Prettier
- Frontend: planowany (React + Tailwind)

---

## 🚧 Roadmapa – porównanie planu z realizacją

| Funkcja                        | Planowane | Zrealizowane                                         |
| ------------------------------ | --------- | ---------------------------------------------------- |
| Rejestracja i logowanie (JWT)  | ✅        | ✅                                                   |
| Tworzenie zadań z AI           | ✅        | ✅ (`POST /ai-create`)                               |
| Obsługa terminów wykonania     | ✅        | ✅                                                   |
| Obsługa fallback JSON GPT      | ❌        | ❌ (już niepotrzebna – replaced by function calling) |
| Porównywanie podobnych zadań   | ✅        | ✅ (embedding + cosine similarity)                   |
| Ocena trudności (`difficulty`) | ✅        | ✅ (generowane przez GPT)                            |
| Frontend: dashboard            | ✅        | ❌ brak                                              |

---

## 🔄 Historia wersji

### v0.0.8 – 2025-04-11

- Wdrożono function calling (GPT-4o)
- AI generuje `title`, `description`, `dueDate`, `difficulty`
- Dodano system embeddingów i automatyczne przypisywanie `similarTasks`
- Usunięto fallback JSON – niepotrzebny przy function calling
- Przygotowano `embeddingService.js` i integrację z `taskController.js`

---

## 📄 Dokumentacja

- `project_overview.md`
- `backend_overview.md`
- `controllers.md`
- `api_spec.md`
- `utils.md`
- `services.md`
- `ai_integration.md`
