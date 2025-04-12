# AI Task App – Realistyczny przegląd projektu (aktualizacja: 2025-04-12)

## 📘 Opis ogólny projektu

AI Task App to aplikacja webowa wspierana przez GPT-4o, której celem jest wspomaganie osób technicznych (programistów, administratorów IT, naukowców) w zarządzaniu zadaniami, dokumentowaniu problemów oraz wyszukiwaniu podobnych sytuacji z przeszłości. Architektura przewiduje osobne moduły dla backendu (Node.js/Express) i frontend (React), z integracją AI poprzez OpenAI API.

---

## 🎯 Cele techniczne

- Rejestracja i logowanie użytkownika (JWT)
- Tworzenie zadań wspieranych przez GPT (function calling)
- Porównywanie zadań przez embeddingi
- Zamykanie zadania z pomocą AI lub kopiowania
- Dashboard do zarządzania zadaniami
- Skalowalność dla zespołów i organizacji
- Przechowywanie danych w MongoDB (lokalnie i w chmurze)

---

## 📌 Faktyczny stan implementacji

### ✅ Backend:

- Zrealizowano:

  - `authController.js` – rejestracja i logowanie użytkownika (JWT i bcrypt)
  - `taskController.js` – CRUD zadań + `ai-create` + `ai-close`
  - `gptService.function.js` – function calling (`create_task`, `assess_summary`, `improve_summary`)
  - `aiSummaryService.js` – cała logika zamykania zadania (`summary`, `force`, `sourceTaskId`)
  - `embeddingService.js` – generowanie embeddingów i wyszukiwanie podobnych zadań
  - `taskRoutes.js` – routing `/ai-create`, `/ai-close`
  - `utils/responseHandler.js` – spójna obsługa odpowiedzi API
  - `validators/` – m.in. `validateCloseTaskInput`
  - Obsługa pola `difficulty` generowanego przez AI
  - Obsługa `similarTasks` przypisywanych automatycznie (jeśli similarity >= 0.75)
  - Walidacja długości `summary` (min. 40 znaków)

- Logika zamykania zadania:
  - AI może tylko wygładzać lub oceniać – nie generuje samodzielnych rozwiązań
  - Użytkownik może wymusić słaby opis (`force: true`)
  - Możliwość skopiowania `summary` z innego zadania (`sourceTaskId`)
  - Brak danych → błąd (AI nie działa automatycznie)

---

### ❌ Frontend:

- Brak kodu źródłowego frontendu (tylko struktura projektu)
- Brakuje UI do:
  - Tworzenia i edycji zadań
  - Podglądu podobnych zadań
  - Zamykania zadania z pomocą AI (`/ai-close`)
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
- AI: OpenAI GPT-4o (function calling) + `text-embedding-3-small`
- Formatowanie: Prettier
- Frontend: planowany (React + Tailwind)

---

## 🚧 Roadmapa – porównanie planu z realizacją

| Funkcja                          | Planowane | Zrealizowane                                        |
| -------------------------------- | --------- | --------------------------------------------------- |
| Rejestracja i logowanie (JWT)    | ✅        | ✅                                                  |
| Tworzenie zadań z AI             | ✅        | ✅ (`POST /ai-create`)                              |
| Obsługa terminów wykonania       | ✅        | ✅                                                  |
| Porównywanie podobnych zadań     | ✅        | ✅ (embedding + cosine similarity)                  |
| Ocena trudności (`difficulty`)   | ✅        | ✅ (generowane przez GPT)                           |
| Zamknięcie zadania z AI          | ✅        | ✅ (`POST /:id/ai-close`)                           |
| Automatyczne summary z podobnych | ❌        | ❌ (świadoma decyzja: użytkownik musi wskazać dane) |
| Frontend: dashboard              | ✅        | ❌ brak                                             |

---

## 🔄 Historia wersji

### v0.0.9 – 2025-04-12

- Wdrożono endpoint `POST /api/tasks/:id/ai-close`
- AI ocenia jakość podsumowania (`getSummaryAssessment`)
- Dodano `improveSummary` (wygładzanie tekstu)
- Obsługa `force: true` i `sourceTaskId`
- Brak generowania summary z `similarTasks` – pełna kontrola użytkownika

---

## 📄 Dokumentacja

- `project_overview.md`
- `backend_overview.md`
- `controllers.md`
- `api_spec.md`
- `utils.md`
- `services.md`
- `ai_integration.md`
- `validators.md`
