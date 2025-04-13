# 🗺️ Roadmap projektu – AI Task App (wersja rozszerzona)

Ten dokument przedstawia harmonogram i kolejne etapy rozwoju aplikacji AI Task App, opisując realizację funkcji backendowych, AI, embeddingów, sesji użytkownika oraz rozwoju interfejsu frontendowego. Ujęto zarówno zrealizowane funkcje, jak i planowane rozbudowy.

---

## 🔹 Etap 0 – Fundament backendu (✅ zrealizowano)

- Inicjalizacja projektu Node.js + Express
- Konfiguracja MongoDB (lokalnie i w chmurze)
- Autoryzacja użytkownika (JWT)
- Middleware: autoryzacja, walidacja, obsługa błędów
- Model `User`, operacje: rejestracja, logowanie
- Struktura: `controllers`, `routes`, `services`, `validators`, `utils`
- Obsługa tokena `JWT_SECRET` i szyfrowania `AES-256-GCM`

---

## 🔹 Etap 1 – System zarządzania zadaniami (✅ zrealizowano)

- Model `Task` zawierający: `description`, `title`, `dueDate`, `difficulty`, `summary`, `status`, `ownerId`, `similarTasks`, `embedding`
- Endpointy:
  - `POST /api/tasks` – ręczne tworzenie
  - `PATCH /api/tasks/:id` – edycja
  - `GET /api/tasks` – lista użytkownika
- Walidatory z `express-validator`
- Obsługa błędów przez `validate.js` + `sendError`

---

## 🔹 Etap 2 – Integracja AI (✅ zrealizowano)

- Połączenie z GPT-4o (function calling, `gptService.function.js`)
- Endpoint `POST /api/tasks/ai-create`:
  - tylko `description` jako input
  - AI generuje: `title`, `description`, `difficulty`, `dueDate`
- Embeddingi (`text-embedding-3-small`) generowane automatycznie
- Przypisywanie `similarTasks` (cosine similarity ≥ 0.75)

---

## 🔹 Etap 3 – Zamykanie zadań (✅ zrealizowano)

- Endpoint `PATCH /api/tasks/:id/ai-close`:
  - `summary` oceniany przez AI
  - Jeśli zbyt krótki → AI odrzuca, chyba że `force = true`
  - AI wygładza styl
- Endpoint `PATCH /api/tasks/:id/close`:
  - Kopiowanie `summary` z innego zadania (`sourceTaskId`)
  - Brak użycia AI, brak własnego `summary`
- Separacja logiki manualnej vs AI (różne endpointy)

---

## 🔹 Etap 4 – Frontend (✅ częściowo zrealizowano)

- React + Vite + TailwindCSS v4
- Widoki: `WelcomePage`, `LoginPage`, `RegisterPage`, `TasksPage`
- `AuthContext` do sesji JWT
- `ProtectedRoute`, `Header`, dynamiczne linki
- Wstępna integracja z API (`fetch`)
- Planowane:
  - `TaskFormPage` – tworzenie zadania z AI
  - `taskService.js`, `authService.js`

---

## 🔹 Etap 5 – Historia i semantyczne wyszukiwanie (⏳ planowane)

- Endpoint `POST /api/ai/similar-tasks`
- Możliwość oceniania: "czy podobne było pomocne"
- Budowa osobistej bazy wiedzy użytkownika
- Wizualizacja podobieństw w UI

---

## 🔹 Etap 6 – Rozszerzenia i wersja produkcyjna (⏳ planowane)

- Role: `admin`, `readonly`, `organization`
- Potwierdzenie e-mail (`emailVerified`) – 🔄 planowane, niezaimplementowane
- Zatwierdzanie rejestracji przez admina (`approvedByAdmin`) – 🔄 planowane, niezaimplementowane
- Eksport danych (CSV/JSON)
- Powiadomienia email, webhooki
- Panel statystyk użytkownika
- Backup MongoDB
- Wersja mobilna (PWA)

---

## 📌 Stan na dziś

- Etapy 0–3: backend w pełni gotowy (AI, embeddingi, zamykanie)
- Etap 4: frontend gotowy do pracy z sesją JWT
- Dokumentacja projektowa i architektura ujednolicone
- Trwa integracja interfejsu z backendem oraz refaktoryzacja usług
