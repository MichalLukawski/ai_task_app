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
- [x] Endpoint `POST /api/tasks/:id/close` – zamknięcie z AI
- [x] Endpoint `GET /api/tasks` – lista zadań użytkownika
- [x] Endpoint `PUT /api/tasks/:id` – edycja
- [x] Autoryzacja i filtracja po `ownerId`
- [x] Walidacja pól (`express-validator`)
- [x] Middleware `validate.js` i `taskValidator.js`

---

## 🤖 Etap 2: Integracja GPT (**W TRAKCIE**)
- [x] Połączenie z OpenAI API (model GPT-4o)
- [x] Endpoint `POST /api/tasks/ai-create` – generowanie zadania
- [x] Moduł `gptService.js` – nowa wersja: odpowiedź w JSON
- [x] Uwzględnienie bieżącej daty w promcie
- [x] Parsowanie odpowiedzi GPT (`JSON.parse`)
- [x] Fallback: jeśli odpowiedź nieparsowalna → zapis do `notes`
- [x] Logowanie błędnych odpowiedzi do `logs/gpt_fallbacks.log`
- [ ] Generowanie podsumowania przy zamykaniu zadania
- [ ] Przechowywanie i szyfrowanie klucza API
- [ ] Ocena trudności zadania przez GPT (`difficulty`: 1–5) – **PLANOWANE**

---

## 🧠 Etap 3: Semantyczna historia (**PLANOWANE / ROZPISANE**)
- [ ] Tworzenie embeddingów z opisów zadań (model: `text-embedding-3-small`)
- [ ] Porównanie nowego zadania z historią przez cosine similarity
- [ ] Dopiero top 3 przypadki analizowane przez GPT-4o (opcjonalnie)
- [ ] Endpoint `POST /api/ai/similar-tasks`
- [ ] Wizualizacja podobieństw / linki do historii

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

Etap 2 został znacznie rozszerzony w wersji `0.0.7` (2025-04-10):
- Zmieniono sposób integracji GPT: zwracany jest JSON, nie markdown
- Dodano mechanizm fallbacku i logowania błędnych odpowiedzi AI
- Zdefiniowano plan wdrożenia embeddingów i porównań semantycznych
- Zaplanowano ocenę trudności (`difficulty`) przy użyciu GPT
