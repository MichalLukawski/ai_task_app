# 📍 Roadmap projektu – AI Task App (zaktualizowana)

Dokument opisuje chronologiczny i funkcjonalny plan rozwoju aplikacji AI Task App – systemu do zarządzania zadaniami z funkcją asystenta AI (GPT-4o), embeddingami i podsumowaniem wspieranym przez model językowy.

Każdy etap zawiera podsumowanie wykonanych działań, uzasadnienie techniczne oraz opis zmian, które wpłynęły na architekturę backendu, frontend i integrację z modelem AI.

---

## Etap 1 – Projekt techniczny (ukończony)

- Analiza przypadków użycia (zarządzanie zadaniami, AI, priorytetyzacja)
- Wybór stacku technologicznego:
  - Backend: Node.js + Express + MongoDB
  - Frontend: React + Vite + TailwindCSS
  - Integracja z OpenAI (GPT-4/GPT-4o)
- Opracowanie schematów danych:
  - `Task`, `User`, `ApiKey`
- Podział logiczny na `controllers`, `routes`, `services`, `middleware`

---

## Etap 2 – Rejestracja i logowanie (ukończony)

- Uwierzytelnianie z JWT (`jsonwebtoken`)
- Walidacja danych (`express-validator`)
- Haszowanie haseł (`bcrypt`)
- Endpointy:
  - `POST /api/auth/register`
  - `POST /api/auth/login`
- Dodano middleware `auth.js` do ochrony tras

---

## Etap 3 – Obsługa zadań (ukończony)

- Model `Task` z polami:
  - `title`, `description`, `status`, `difficulty`, `dueDate`, `summary`, `embedding`
- Trasy:
  - `GET /api/tasks`
  - `POST /api/tasks`
  - `PATCH /api/tasks/:id`
- Wprowadzenie `taskValidator.js` dla walidacji danych wejściowych

---

## Etap 4 – Integracja z AI (ukończony)

- Endpoint `POST /api/tasks/ai-create` → tworzenie zadań na podstawie opisu użytkownika
- Funkcja `getTaskStructureFromAI(description)` – wywołanie GPT-4o z function calling
- Dodanie `embeddingService.js`:
  - generowanie embeddingów
  - identyfikacja podobnych zadań (`similarTasks`)
- Zapis `embedding`, `similarTasks` do modelu `Task`

---

## Etap 5 – Zamykanie zadania z pomocą AI (ukończony)

- Endpoint `PATCH /api/tasks/:id/ai-close`
  - Walidacja `summary` (min. 40 znaków)
  - AI ocenia jakość podsumowania (`getSummaryAssessment(...)`)
  - AI wygładza stylistykę (`improveSummary(...)`)
- Dodanie parametru `force = true`, aby wymusić zamknięcie mimo niskiej jakości
- Dodanie `aiSummaryService.js` – warstwa pośrednia do logiki zamykania zadania z AI

---

## Etap 6 – Kopiowanie podsumowania (ukończony)

- Endpoint `PATCH /api/tasks/:id/close`
  - Kopiuje `summary` z innego zamkniętego zadania (`sourceTaskId`)
  - Nie korzysta z AI
- Walidacja obecności `summary` w zadaniu źródłowym

---

## Etap 7 – Refaktoryzacja i standaryzacja backendu (ukończony)

🔧 Największa zmiana techniczna od początku projektu.

- Ujednolicenie struktury katalogów i nazewnictwa:
  - `gptService.function.js` → `gptService.js`
  - dodanie `systemController.js`, `systemRoutes.js`
  - dodanie `authValidator.js`
- Wprowadzenie `utils/responseHandler.js`:
  - `sendSuccess`, `sendError`, `handleTryCatch`
- Wprowadzenie pełnej walidacji w każdej trasie (`validate.js`)
- Refaktoryzacja wszystkich `controllers/` do korzystania z `try/catch` przez wrappery
- Ustandaryzowanie odpowiedzi: każda trasa zwraca `status`, `message`, `data`
- Wprowadzenie `setOpenAIKey()` i szyfrowania AES-256-GCM (`openaiKeyManager.js`)
- Backend przygotowany do rozbudowy o role, zarządzanie kontem, uprawnienia
- Rozszerzenie middleware `auth.js` – przypisywanie `email`, `role`, `id` do `req.user`

---

## Etap 8 – Ulepszenie UX edycji zadań (ukończony)

- Refaktoryzacja `useTaskCardState.jsx`:
  - model `editedTask` lokalnie zarządza `difficulty`, `dueDate`
  - UI aktualizuje się natychmiast, zapis następuje po kliknięciu w kartę, poza nią, lub po naciśnięciu `Enter`
- Zmiana: brak autozapisu, zapis wyłącznie po intencjonalnym zakończeniu edycji
- Widok `TaskCardView` oparty o `editedTask` (natychmiastowa prezentacja zmian)
- Obsługa kliknięć i klawisza `Enter` w celu otwierania, zapisywania i zamykania karty
- Mechanizm `showSaved` oraz `isSaving` – UX informujący o trwającym zapisie
- Dodanie synchronizacji z backendem (`PATCH` → `GET`) po zapisaniu wartości
- Wsparcie `onTaskUpdated(...)` – przekazanie nowej wersji danych do `DashboardPage`
- Mechanizm fokusowania tylko jednej karty (`focusedCardId`)
- Obsługa zamykania karty, jeśli kliknięto ponownie bez wprowadzenia zmian

---

## Etap 9 – Planowane

- Edycja `title`, `description` (inline + AI poprawa stylu)
- Wyszukiwanie z użyciem embeddingów (`similarity search`)
- Eksport podsumowań (`summary`) do PDF
- Logowanie działań użytkownika (audyt)
- Kontrola ról (`admin`, `user`)
- Interfejs konfiguracji AI (model, temperatura, tokeny)
- Tryby pracy AI (mentor/debugger/ekspert)
- Testowanie rozwiązań GPT offline (prompt mirror + offline logs)
- Automatyczne testy komponentów (Jest/Playwright)
- Pełna dokumentacja dla zespołu (markdown + generator)

---

## 📘 Dokumentacja wspierająca

- `controllers.md`, `routes.md`, `services.md`, `validators.md`, `utils.md`
- `api_spec.md` – aktualna specyfikacja endpointów
- `project_overview.md`, `task_flow.md`, `auth_flow.md`
- `CHANGELOG.md` – zmiany wersji backendu
