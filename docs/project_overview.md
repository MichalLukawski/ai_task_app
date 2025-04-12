# 📘 Przegląd projektu – AI Task App

AI Task App to aplikacja webowa wspierana przez GPT-4o, która pełni rolę osobistego asystenta technicznego do zarządzania zadaniami. Głównym celem projektu jest umożliwienie użytkownikom (głównie technicznym: programistom, specjalistom IT, naukowcom) szybkiego dokumentowania problemów, porządkowania wiedzy oraz odnajdywania rozwiązań na podstawie przeszłych przypadków.

System wspiera zarówno tworzenie zadań z pomocą AI, jak i manualne, oraz umożliwia ich zamykanie z inteligentnym podsumowaniem lub przez kopiowanie rozwiązania z innego zadania.

---

## 🎯 Cele systemu

- Ułatwienie dokumentowania pracy i problemów technicznych
- Możliwość ponownego wykorzystania wiedzy (poprzez podobne zadania)
- Wspomaganie analizy i opisu zadań przy pomocy GPT-4o
- Zwiększenie produktywności i zmniejszenie wysiłku poznawczego
- W przyszłości – zbudowanie osobistej bazy wiedzy i rozwiązanych przypadków

---

## 🧱 Architektura aplikacji

### Backend:

- Node.js + Express
- MongoDB + Mongoose
- OpenAI API (GPT-4o + text-embedding-3-small)
- JWT (uwierzytelnianie), bcrypt (haszowanie haseł)
- Express-validator (walidacja pól), middleware, modularny podział

### Frontend:

- Planowany: React + Tailwind CSS
- Interfejs do przeglądania i tworzenia zadań
- Obsługa zamykania zadania z AI i kopiowania `summary`
- Obsługa JWT i sesji użytkownika

---

## 📦 Model danych `Task`

Zadanie zawiera:

- `title` – krótki tytuł (może być wygenerowany przez AI)
- `description` – pełny opis (wymagany)
- `difficulty` – trudność (1–5, opcjonalna, generowana przez AI)
- `dueDate` – termin wykonania (opcjonalny)
- `summary` – podsumowanie po zakończeniu (ręczne lub przez AI)
- `status` – `"open"` lub `"closed"`
- `closedAt` – data zamknięcia
- `embedding` – wektor wygenerowany na potrzeby porównań
- `similarTasks` – lista ID podobnych zadań
- `ownerId`, `createdAt` – standardowe metadane

---

## 🔁 Obsługa zadań

### Tworzenie zadania:

- `POST /api/tasks` – ręczne
- `POST /api/tasks/ai-create` – z pomocą AI (function calling)
  - AI analizuje `description`, generuje tytuł, szczegółowy opis, trudność i opcjonalnie termin
  - Backend generuje embedding i przypisuje podobne zadania (`similarTasks`)

### Edycja zadania:

- `PATCH /api/tasks/:id`
  - Można zmieniać: `title`, `description`, `dueDate`, `status`
  - Walidacja pól jest opcjonalna – tylko jeśli występują (`PATCH`, nie `PUT`)

### Zamykanie zadania:

- `PATCH /api/tasks/:id/ai-close`

  - Wymagane pole: `summary`
  - AI ocenia jego jakość
  - Jeśli słabe – zwraca błąd, chyba że `force: true`
  - Wygładzenie opisu odbywa się przez `function calling`

- `PATCH /api/tasks/:id/close`
  - Kopiuje `summary` z innego zadania (`sourceTaskId`)
  - AI nie bierze udziału
  - `summary` nie może być przesyłane – tylko ID

---

## 🧠 Porównywanie podobnych zadań (embeddingi)

- Przy tworzeniu zadania (AI) backend tworzy embedding (OpenAI `text-embedding-3-small`)
- Porównanie z embeddingami zamkniętych zadań (`cosine similarity`)
- Maksymalnie 5 zadań z podobieństwem ≥ 0.75 trafia do `similarTasks`
- Użytkownik może później wskazać, które były naprawdę pomocne

---

## 🔐 System kont

- Rejestracja użytkownika tylko przez zaproszenie
- JWT + middleware autoryzujące trasy
- Role: `user`, `admin` (planowane)

---

## ⚙️ Technologie

- OpenAI API (GPT-4o, embeddings)
- Express, Mongoose, JWT
- MongoDB lokalnie i MongoDB Atlas (backup)
- Prettier (formatowanie kodu)
- Planowany frontend: React + TailwindCSS

---

## 📄 Dokumentacja i struktura repozytorium

```
ai-task-app/
├── backend/     ← podrepozytorium backendu (Express)
├── frontend/    ← (planowany interfejs React)
├── docs/        ← pełna dokumentacja markdown
├── .gitmodules
└── README.md
```

---

## 🧪 Testowanie i jakość

- Walidatory opierają się na `express-validator` i są dopasowane do REST (`POST`, `PATCH`)
- Obsługa błędów z warstwą `validate.js` i `responseHandler.js`
- W logice zamykania zadania AI decyduje o przyjęciu podsumowania

---

## 📌 Wnioski architektoniczne

- Wszystkie operacje zamykające to `PATCH`, zgodnie z REST
- `summary` może być przesyłane tylko w `ai-close`, a jego jakość musi zostać oceniona
- Rozdział AI i działań manualnych jest przejrzysty i zabezpieczony zarówno w backendzie, jak i UI
