# AI Task App

Aplikacja webowa, która pełni funkcję osobistego asystenta AI do zarządzania zadaniami. Aplikacja będzie hostowana lokalnie na moim serwerze domowym i dostępna z dowolnego urządzenia. Dane będą zapisywane w MongoDB (zarówno lokalnie, jak i w kopii chmurowej MongoDB Atlas). System wykorzystuje GPT-4o oraz embeddingi do wspomagania użytkownika w tworzeniu, przeszukiwaniu i zamykaniu zadań.

---

## 🎯 Cel systemu

Pomoc osobom wykonującym złożone zadania (np. programistom, naukowcom, administratorom IT) w dokumentowaniu problemów i odzyskiwaniu rozwiązań na podstawie przeszłych przypadków. System pełni rolę asystenta AI oraz bazy wiedzy eksperckiej.

---

## 🔐 System kont i logowania

- Rejestracja użytkownika tylko przez link od administratora
- Potwierdzenie e-maila po rejestracji
- Logowanie przez JWT
- Role: `admin` i `user`

---

## 📌 Zarządzanie zadaniami

- Użytkownik wpisuje opis zadania (problem, plan, cel)
- GPT-4o (via `function calling`) generuje strukturę zadania:

```json
{
  "title": "Skrypt do backupu",
  "description": "Zadanie polega na utworzeniu skryptu do wykonywania backupów...",
  "dueDate": "2025-05-01",
  "difficulty": 3
}
```

- Zadanie zapisywane jest do MongoDB
- System generuje `embedding` i przypisuje podobne zadania (`similarTasks`)
- Użytkownik może edytować dane zadania

---

## ✅ Zamykanie zadań

- Użytkownik zamyka zadanie przez endpoint `/api/tasks/:id/ai-close`
- Możliwości:
  - Podaje własne `summary` → AI ocenia i wygładza
  - Opis zbyt krótki → może wymusić (`force: true`) → AI wygładza
  - Wskazuje `sourceTaskId` → kopiujemy `summary` z innego zadania (bez AI)
  - Brak `summary` i `sourceTaskId` → system odrzuca (wymagana decyzja użytkownika)

---

## 🔍 Porównywanie zadań

- Generowanie embeddingu (`text-embedding-3-small`) na podstawie `title + description`
- Porównanie z embeddingami zamkniętych zadań (cosine similarity)
- Tylko zadania z similarity >= 0.75 trafiają do `similarTasks`
- Maksymalnie 5 wyników
- Podobne zadania mogą być wykorzystane tylko ręcznie przez użytkownika (nie automatycznie przez AI)

---

## 💬 Inteligentne wsparcie AI (planowane)

GPT będzie wspierać użytkownika także poprzez:

- „Jakie mam obecnie otwarte zadania?”
- „Co jest najłatwiejsze do zrobienia?”
- Generowanie listy zadań wg trudności (`difficulty`)
- Sugestie priorytetów, grupowania zadań

---

## 🤖 Integracja z OpenAI

- Backend używa GPT-4o przez `openai` SDK (function calling)
- Obsługiwane funkcje: `create_task`, `assess_summary`, `improve_summary`
- AI **nigdy nie tworzy summary automatycznie** – użytkownik musi podać dane
- Użytkownik podaje swój klucz OpenAI (lokalnie)
- Klucz nie trafia do frontend – planowane szyfrowanie AES
- Brak fallbacków – struktura zwracana zawsze jako JSON

---

## 🧹 Czyszczenie historii AI

- Kontekst GPT nie jest przechowywany – reset po zamknięciu zadania
- Wszystkie odpowiedzi AI są jednorazowe i kontekstowe

---

## 🧠 Dane i baza danych

- MongoDB lokalnie + MongoDB Atlas (backup)
- Użytkownicy mają osobne przestrzenie (`ownerId`)
- Embedding i podobne zadania (`similarTasks`) w tym samym modelu danych
- Planowane: organizacje i współdzielona wiedza

---

## 💡 Przykładowy scenariusz

> Użytkownik wpisuje: "Nie działa API uczelni, chyba chodzi o tokeny"

> GPT-4o tworzy zadanie "Naprawa API uczelni"

> Backend wykrywa podobne zadanie z przeszłości: "Brak nagłówka Authorization"

> Użytkownik wskazuje to zadanie jako `sourceTaskId`, a system kopiuje opis rozwiązania

---

## 🧰 Technologie

- **Frontend:** React + TailwindCSS (planowany)
- **Backend:** Node.js + Express
- **Baza danych:** MongoDB (lokalnie i w chmurze)
- **Autoryzacja:** JWT, bcrypt
- **AI:** GPT-4o + text-embedding-3-small (function calling)
- **Inne:** dotenv, Mongoose, Prettier, AES (planowane)

---

## 📁 Repozytoria

- [Backend (Node.js)](https://github.com/MichalLukawski/ai-task-app-backend)
- [Frontend (React)](https://github.com/MichalLukawski/ai-task-app-frontend)

---

## 🧩 Struktura projektu (monorepo z submodułami)

```
ai-task-app/
├── backend/     ← submoduł backendu
├── frontend/    ← submoduł frontendu
├── docs/        ← dokumentacja markdown
├── .gitmodules
├── README.md
```

### ✅ Klonowanie z submodułami

```bash
git clone https://github.com/MichalLukawski/ai-task-app.git
cd ai-task-app
git submodule update --init --recursive
```

### 🔁 Aktualizacja submodułów

```bash
git submodule update --remote --merge
```
