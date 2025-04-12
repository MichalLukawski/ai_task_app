# AI Task App

AI Task App to webowa aplikacja wspierana przez GPT-4o, która pełni funkcję osobistego asystenta technicznego do zarządzania zadaniami. Aplikacja działa lokalnie (z możliwością wdrożenia w chmurze) i zapisuje dane w MongoDB. System umożliwia tworzenie zadań z pomocą AI, przypisywanie podobnych przypadków oraz inteligentne zamykanie zadań na podstawie oceny podsumowania.

---

## 🎯 Cel systemu

Pomoc osobom realizującym złożone, techniczne zadania – takim jak programiści, administratorzy, analitycy czy naukowcy – w dokumentowaniu pracy, analizowaniu podobnych przypadków i odzyskiwaniu wiedzy w oparciu o historię rozwiązań. System działa jako:

- osobisty asystent AI (GPT-4o)
- baza wiedzy eksperckiej (embeddingi)
- narzędzie produktywności (planowanie, przypomnienia)

---

## 🔐 System kont i logowania

- Rejestracja wyłącznie przez link od administratora
- Weryfikacja e-mail
- JWT jako token autoryzacyjny
- Role: `user`, `admin` (rozszerzenia planowane)

---

## 📌 Zarządzanie zadaniami

- Użytkownik wpisuje `description`, reszta może być wygenerowana
- AI uzupełnia: `title`, `difficulty`, `dueDate` (jeśli wynika z treści)
- Zadanie zapisywane do MongoDB
- Generowany jest `embedding` i przypisywane `similarTasks`
- Zadanie można edytować częściowo (`PATCH`)

Przykład wygenerowanego zadania:

```json
{
  "title": "Skrypt do backupu",
  "description": "Zadanie polega na utworzeniu skryptu do wykonywania backupów...",
  "dueDate": "2025-05-01",
  "difficulty": 3
}
```

---

## ✅ Zamykanie zadań

- `PATCH /api/tasks/:id/ai-close` – AI ocenia i wygładza `summary`
  - jeśli za krótkie → AI odrzuca, użytkownik może wymusić (`force: true`)
  - `summary` musi mieć sens i zawierać informacje techniczne
- `PATCH /api/tasks/:id/close` – kopiowanie `summary` z innego zadania (`sourceTaskId`)
  - bez AI
  - `summary` nie może być przesyłane ręcznie

---

## 🔍 Porównywanie zadań

- Generowanie embeddingu (`text-embedding-3-small`) z `title + description`
- Cosine similarity z zamkniętymi zadaniami
- Przypisywane max 5 zadań o podobieństwie ≥ 0.75
- Użytkownik sam decyduje, które podobne zadania były pomocne
- AI nie korzysta automatycznie z `similarTasks`

---

## 💬 Inteligentne wsparcie AI (planowane)

- Odpowiedzi na pytania:
  - „Jakie mam obecnie otwarte zadania?”
  - „Co powinienem zrobić najpierw?”
  - „Które zadania są najtrudniejsze?”
- Sugestie kolejności, grupowania, tygodniowego planowania

---

## 🤖 Integracja z OpenAI

- GPT-4o z `function calling` (OpenAI SDK)
- Obsługiwane funkcje:
  - `create_task`
  - `assess_summary`
  - `improve_summary`
- `summary` **nigdy nie jest generowane automatycznie**
- AI działa tylko przy zamykaniu przez `/ai-close`
- Klucz OpenAI przechowywany lokalnie (planowane szyfrowanie AES)
- Wszystkie odpowiedzi mają format JSON (brak fallbacków)

---

## 🧹 Czyszczenie kontekstu

- Kontekst GPT nie jest utrzymywany między zadaniami
- AI zawsze działa jednorazowo i w izolacji (stateless)

---

## 🧠 Dane i baza danych

- MongoDB lokalnie oraz w MongoDB Atlas (z backupem)
- Użytkownik ma własną przestrzeń (`ownerId`)
- Zadania zawierają:
  - `summary`, `similarTasks`, `embedding`
- Planowane: organizacje i współdzielona wiedza

---

## 💡 Przykładowy scenariusz

> Użytkownik wpisuje: "Nie działa API uczelni, chyba chodzi o tokeny"

> GPT-4o tworzy zadanie: "Naprawa API uczelni"

> Backend wykrywa podobne zadanie z przeszłości: "Brak nagłówka Authorization"

> Użytkownik wskazuje to zadanie jako `sourceTaskId`  
> System kopiuje `summary` z tamtego zadania do nowego

---

## 🧰 Technologie

- **Frontend:** React + TailwindCSS (planowany)
- **Backend:** Node.js + Express
- **Baza danych:** MongoDB lokalnie i w chmurze
- **Autoryzacja:** JWT + bcrypt
- **AI:** OpenAI GPT-4o + `text-embedding-3-small`
- **Walidacja:** express-validator
- **Inne:** dotenv, AES (planowane), Prettier

---

## 📁 Repozytoria

- [Backend (Node.js)](https://github.com/MichalLukawski/ai-task-app-backend)
- [Frontend (React)](https://github.com/MichalLukawski/ai-task-app-frontend)

---

## 🧩 Struktura projektu

```
ai-task-app/
├── backend/     ← podrepozytorium Express (API, AI)
├── frontend/    ← (planowany interfejs React)
├── docs/        ← dokumentacja Markdown
├── .gitmodules
└── README.md
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
