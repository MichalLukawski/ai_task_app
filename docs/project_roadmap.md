# 📍 AI Task App – Roadmapa projektu

## 🔄 Etap 0: Fundament backendu (**ZREALIZOWANE**)

- [x] Inicjalizacja projektu Node.js
- [x] Konfiguracja Express i MongoDB
- [x] Model użytkownika (`User.js`)
- [x] Rejestracja i logowanie z JWT (bazowe endpointy)
- [x] Middleware `auth.js` do ochrony tras
- [x] Obsługa błędów i kodów odpowiedzi (`utils/responseHandler.js`)
- [x] Struktura odpowiedzi: `sendSuccess`, `sendError`

---

## 🚀 Etap 1: System zadań (**ZREALIZOWANE**)

- [x] Model `Task.js` z polami: opis, status, daty, notatki AI
- [x] Dodanie pola `dueDate` (termin wykonania) + obsługa
- [x] Endpoint `POST /api/tasks` – tworzenie zadania
- [x] Endpoint `POST /api/tasks/:id/ai-close` – zamykanie z pomocą AI / kopiowania
- [x] Endpoint `GET /api/tasks` – lista zadań użytkownika
- [x] Endpoint `PUT /api/tasks/:id` – edycja
- [x] Autoryzacja i filtracja po `ownerId`
- [x] Walidacja pól (`express-validator`)
- [x] Middleware `validate.js` i `taskValidator.js`

---

## 🤖 Etap 2: Integracja GPT (**ZREALIZOWANE**)

- [x] Połączenie z OpenAI API (model GPT-4o)
- [x] Endpoint `POST /api/tasks/ai-create` – generowanie zadania
- [x] Mechanizm function calling (`create_task`, `assess_summary`, `improve_summary`)
- [x] Obsługa `difficulty`
- [x] Weryfikacja jakości podsumowania (`getSummaryAssessment`)
- [x] Wygładzanie słabych podsumowań (`improveSummary`)
- [x] Możliwość świadomego wymuszenia opisu przez użytkownika (`force`)
- [x] Możliwość użycia `sourceTaskId` – kopiowanie podsumowania
- [x] Brak automatycznego generowania `summary` przez AI z podobnych zadań
- [ ] Przechowywanie i szyfrowanie klucza API

---

## 🧠 Etap 3: Semantyczna historia (**PLANOWANE**)

- [x] Tworzenie embeddingów z opisów zadań (`text-embedding-3-small`)
- [x] Porównanie nowego zadania z historią przez cosine similarity
- [x] Top 5 zadań (`similarity >= 0.75`) zapisywane jako `similarTasks`
- [ ] Endpoint `POST /api/ai/similar-tasks` – ręczne wyszukiwanie podobnych
- [ ] Użytkownik potwierdza podobieństwo → trafiają do `similarTasks`
- [ ] Wizualizacja podobieństw i ich użyteczności

---

## 🎨 Etap 4: Frontend (**DO ZROBIENIA**)

- [ ] Inicjalizacja projektu React + Tailwind
- [ ] Formularz logowania i rejestracji
- [ ] Dashboard z listą zadań
- [ ] Formularz tworzenia zadania
- [ ] Widok szczegółów i zamykanie zadań (`ai-close`)
- [ ] Wybór `sourceTaskId`, komunikaty AI, przycisk „Force”

---

## 📊 Etap 5: Rozwój i produkcja (**PLANOWANE**)

- [ ] Tryb zespołowy i organizacje
- [ ] Role: admin, user, readonly
- [ ] Eksport danych do CSV / JSON
- [ ] Widok statystyk użytkownika
- [ ] Notyfikacje email / webhooki
- [ ] Wersja mobilna (PWA)
- [ ] Backup MongoDB

---

## 🔚 Podsumowanie

Etap 2 (GPT) oraz logika zamykania zadań zostały ukończone w pełni. System obsługuje:

- ocenę jakości `summary`
- wygładzanie stylu
- świadome wymuszenie użycia opisu
- kopiowanie z innego zadania

Brak automatycznego tworzenia `summary` na podstawie `similarTasks` – użytkownik zawsze musi zadecydować.
