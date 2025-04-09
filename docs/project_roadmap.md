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
- [x] Endpoint `POST /api/tasks` – tworzenie zadania
- [x] Endpoint `POST /api/tasks/:id/close` – zamknięcie z AI
- [x] Endpoint `GET /api/tasks` – lista zadań użytkownika
- [x] Endpoint `PUT /api/tasks/:id` – edycja
- [x] Autoryzacja i filtracja po `ownerId`
- [x] Middleware autoryzacyjny (`auth.js`)

---

## 🤖 Etap 2: Integracja GPT (**DO ZROBIENIA**)

- [ ] Przechowywanie i szyfrowanie klucza API
- [ ] Połączenie z OpenAI API (model GPT-4)
- [ ] Tworzenie tytułu/opisu zadania przez AI
- [ ] Generowanie podsumowania przy zamykaniu zadania
- [ ] Endpoint `POST /api/ai/similar-tasks`

---

## 🧠 Etap 3: Semantyczna historia (**DO ZROBIENIA**)

- [ ] Zapytanie do AI z aktualnym opisem
- [ ] Porównanie z istniejącymi zadaniami
- [ ] Ranking trafności + linki do historii
- [ ] Wizualizacja podobieństw

---

## 🎨 Etap 4: Frontend (**DO ZROBIENIA**)

- [ ] Inicjalizacja projektu React + Tailwind
- [ ] Formularz logowania i rejestracji
- [ ] Dashboard z listą zadań
- [ ] Formularz tworzenia zadania
- [ ] Podgląd szczegółów i zamykanie
- [ ] Widok wyników porównań AI

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

Projekt wyszedł z fazy fundamentu backendu – zrealizowano pełny system uwierzytelniania, modelowanie i obsługę zadań oraz odpowiedzi API.  
Kolejny etap to integracja z GPT (etap 2) i implementacja frontendu (etap 4).

