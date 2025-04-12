# 🗺️ Roadmap projektu – AI Task App

Dokument ten przedstawia harmonogram i etapy rozwoju aplikacji AI Task App, wraz z podziałem funkcjonalnym oraz stanem realizacji. Uwzględniono główne filary systemu: zadania, AI, embeddingi, backend, frontend oraz kierunki rozwoju.

---

## 🔹 Etap 0 – Fundament backendu (✅ zrealizowano)

- Inicjalizacja projektu Node.js + Express
- Konfiguracja MongoDB (lokalnie i w chmurze)
- Uwierzytelnianie użytkownika (JWT)
- Middleware: autoryzacja, walidacja, obsługa błędów
- Model `User`, podstawowe operacje (rejestracja, logowanie)
- Struktura kodu z `controllers`, `routes`, `services`, `validators`, `utils`

---

## 🔹 Etap 1 – System zarządzania zadaniami (✅ zrealizowano)

- Model `Task.js` zawierający: `description`, `title`, `dueDate`, `difficulty`, `summary`, `status`, `ownerId`, `similarTasks`, `embedding`
- Endpointy:
  - `POST /api/tasks` – tworzenie zadania ręcznego
  - `PATCH /api/tasks/:id` – edycja wybranych pól (`PATCH` zamiast `PUT`)
  - `GET /api/tasks` – lista zadań użytkownika
- Walidatory `validateTaskInput`, `validateUpdateTaskInput`
- Middleware do spójnej obsługi błędów

---

## 🔹 Etap 2 – Integracja AI (✅ zrealizowano)

- Połączenie z OpenAI GPT-4o (API + function calling)
- Endpoint `POST /api/tasks/ai-create`:
  - tylko `description` jako input
  - generowanie `title`, `description`, `difficulty`, `dueDate` (jeśli występuje)
- Embeddingi generowane automatycznie (`text-embedding-3-small`)
- Automatyczne przypisywanie `similarTasks` (similarity ≥ 0.75)

---

## 🔹 Etap 3 – Zamykanie zadań (✅ zrealizowano)

- Endpoint `PATCH /api/tasks/:id/ai-close`:
  - `summary` oceniany przez AI (jakość, styl)
  - Jeśli zbyt słaby → AI odrzuca
  - Użytkownik może wymusić użycie (`force: true`)
- Endpoint `PATCH /api/tasks/:id/close`:
  - Kopiowanie `summary` z innego zadania (`sourceTaskId`)
  - Brak użycia AI, brak własnego `summary`
- Pełna separacja AI vs manualne kopiowanie (zabezpieczone backendem)

---

## 🔹 Etap 4 – Frontend (⏳ planowany)

- React + TailwindCSS
- Dashboard z listą zadań (`GET`)
- Formularze tworzenia (`POST`) i edycji (`PATCH`)
- Interfejs do zamykania:
  - własny opis → `/ai-close`
  - kopiowanie → `/close`
- Obsługa sesji JWT
- Wizualizacja podobnych zadań (`similarTasks`)

---

## 🔹 Etap 5 – Historia i semantyczne wyszukiwanie (⏳ planowane)

- Endpoint `POST /api/ai/similar-tasks` do wyszukiwania podobnych zadań
- Możliwość ręcznego oznaczania trafności (`czy podobne było pomocne`)
- Budowa wewnętrznej bazy wiedzy użytkownika

---

## 🔹 Etap 6 – Rozszerzenia i wersja produkcyjna (⏳ planowane)

- Role `admin`, `readonly`, organizacje
- Eksport danych (CSV/JSON)
- Webhooki, powiadomienia email
- Widok statystyk i analizy zadań
- Wersja mobilna (PWA)
- Backup MongoDB + szyfrowanie klucza OpenAI (AES)

---

## 📌 Stan na dziś

- Etapy 0–3 zostały zrealizowane w całości
- Projekt jest gotowy do implementacji frontendowej
- Backend obsługuje AI, embeddingi, ocenę jakości podsumowań i pełne rozdzielenie logiki `/close` i `/ai-close`
- Wszystkie operacje zgodne z REST (`POST`, `PATCH`)
