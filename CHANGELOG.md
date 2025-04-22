# 📦 CHANGELOG – AI Task App (Backend-Only, Monorepo)

Poniżej przedstawiono **zmiany backendowe** w projekcie AI Task App prowadzonym jako monorepo. Pominięto zmiany frontendowe i skupiono się wyłącznie na ewolucji API, integracji z AI, refaktoryzacji struktury backendu oraz dokumentacji warstwy serwera.

---

## [0.0.15] – 2025-04-18

### ✨ Ogólne zmiany w monorepo (frontend + backend)

- Rozbudowa modelu `Task`:
  - Dodano pola: `summary`, `closedAt`, `status`, `similarTasks[]`
  - Obsługa zamykania zadania:
    - przez AI (`PATCH /tasks/:id/ai-close`)
    - ręcznie (`PATCH /tasks/:id/close`)
    - kopiowanie z innego (`PATCH /tasks/:id/close-copy`)
- Nowe komponenty i logika frontendowa:
  - `CloseWithAiBox` – dymek do wpisania podsumowania
  - `AiSummaryRejectedModal` – modal do potwierdzenia zapisu mimo odrzucenia AI
  - `SimilarTasksPopup` – komponent z listą podobnych zadań
  - `TaskCardSummary` – prezentacja podsumowania i daty zamknięcia
- Obsługa podobnych zadań (`similarTasks[]`):
  - backend: `.populate()` w `getTaskById`
  - frontend: toggle i render komponentu z danymi
- Trwałe usuwanie zadania (`DELETE /api/tasks/:id`) z potwierdzeniem po stronie UI
- Refetch `GET /tasks/:id` po zapisie, AI-close, close, delete
- Obsługa błędów AI (`AI_REJECTED`) z przejęciem logiki i wymuszeniem zapisu przez użytkownika

---

## [0.0.14] – 2025-04-17

### 🔧 Backend – synchronizacja i refaktoryzacja API

- Backend dostosowany do nowego modelu synchronizacji danych:
  - `PATCH` do `Task` zwraca najnowszy stan zadania
  - Frontend po aktualizacji wykonuje `GET /api/tasks/:id`
- Uspójnienie obsługi błędów:
  - Nowe kody: `MISSING_TITLE_FROM_GPT`, `VALIDATION_ERROR`
- Aktualizacja `taskValidator.js`:
  - Obsługa brakującego `title` i dynamiczna walidacja pól edytowanych
- Dodanie lepszej diagnostyki błędów (logowanie, fallbacki)

---

## [0.0.12] – 2025-04-14

### Changed

- Refaktoryzacja struktury katalogów i komponentów
- Integracja z backendem OpenAI / GPT / Embedding
- Aktualizacja changelogów i dokumentacji wersjonowanej

### Planned

- Osobny panel administratora
- Deployment aplikacji na VPS lub Vercel + Mongo Atlas

## [0.0.11] – 2025-04-13

### Added

- Uruchamianie frontend + backend jedną komendą `npm run dev` (z `concurrently`)
- Główne `README.md` opisujące:
  - strukturę monorepo
  - uruchomienie projektu
  - zależności pomiędzy warstwami
- Podział dokumentacji na frontend/backend + katalog ogólny
- Ujednolicenie numeracji wersji (frontend `0.0.1`, backend `0.0.11`)

### Changed

- Rozdzielenie changelogów i dokumentacji dla backendu i frontendu
- Uporządkowanie plików `.env` i zmiennych środowiskowych dla każdej warstwy
- Ustandaryzowanie stylu plików `.md` (nagłówki, bloki kodu, tabele)

### Planned

- Synchronizacja wersji backend + frontend w CI/CD
- Jednolity system tagowania release’ów Git
- Dashboard + interfejs administratora
