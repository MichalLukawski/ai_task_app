# ✅ Dokumentacja – Przepływ tworzenia i zarządzania zadaniami (AI Task App)

Ten dokument przedstawia pełny przebieg tworzenia, przeglądania, edycji i zamykania zadań w aplikacji AI Task App. Opisuje integrację pomiędzy komponentami frontendowymi, hookami, usługami API i backendem opartym o AI. Dokument służy jako przewodnik po całym cyklu życia zadania – od opisu użytkownika do zakończenia zadania i ewaluacji przez model GPT.

---

## 🔁 Przegląd cyklu życia zadania

1. **Użytkownik tworzy zadanie** przy użyciu formularza AI (`CreateTaskForm`)
2. **Frontend** wysyła opis zadania do endpointu AI (`/api/tasks/ai-create`)
3. **Backend** generuje `title`, `difficulty`, `dueDate` i zapisuje zadanie
4. **Frontend** aktualizuje listę – pojawia się nowa `TaskCard`
5. **Użytkownik edytuje zadanie** (np. termin, opis) – karta przechodzi w tryb `edit`
6. **Użytkownik zamyka zadanie**:
   - manualnie (z pomocą innego zadania)
   - lub z pomocą AI (na podstawie `summary`)
7. **AI ocenia podsumowanie** i zatwierdza zadanie jako zakończone
8. **Zadanie** trafia do statusu `isCompleted = true`

---

## 🧾 Tworzenie zadania z pomocą AI

### 💬 Formularz

- Komponent: `CreateTaskForm`
- Użytkownik podaje **opis** zadania (`description`)
- Nie wybiera tytułu, trudności ani terminu – są generowane

### 📡 Żądanie HTTP

```http
POST /api/tasks/ai-create
Content-Type: application/json
Authorization: Bearer <token>

{
  "description": "Napisz parser CSV w Node.js"
}
```

### 🧠 Backend

- Controller `createWithAI`:
  - wysyła prompt do GPT (modele OpenAI lub lokalne)
  - generuje `title`, `difficulty`, `dueDate`
  - tworzy nowy dokument `Task` w MongoDB

### ✅ Odpowiedź

```json
{
  "title": "Parser CSV w Node.js",
  "description": "Napisz parser CSV w Node.js",
  "difficulty": 3,
  "dueDate": "2025-04-16"
}
```

### 🔄 Frontend

- Komponent `CreateTaskForm` wywołuje `setTasks([...tasks, newTask])`
- Nowa karta pojawia się w `TaskList`

---

## 🗂️ Wyświetlanie zadania (`TaskCard`)

- Komponent `DashboardPage` renderuje `TaskList`
- `TaskList` renderuje wiele `TaskCard` (przekazując dane `task`)
- `TaskCard` pokazuje:
  - `title`, `description`
  - `difficulty` (gwiazdki: `DifficultyStars`)
  - `dueDate` (pasek: `DueDateProgress`)
  - status: otwarte/zamknięte
  - akcje: Edytuj / Zakończ

---

## ✏️ Edycja zadania

### 🔀 Mechanizm

- Po kliknięciu „Edytuj” → `TaskCard` przełącza się w tryb `edit`
- Widok zmienia się z `TaskCardView` na `TaskCardEdit`
- Hook `useTaskCardState` zarządza przełączaniem i lokalnym stanem edycji

### 🧩 Użytkownik może:

- zmienić `dueDate` przez `DueDateEditor`
- zmienić `difficulty` (select lub kliknięcie w gwiazdki)
- zapisać lub anulować zmiany

### 🔁 Zapis zmian

```http
PATCH /api/tasks/:id
Authorization: Bearer <token>

{
  "difficulty": 2,
  "dueDate": "2025-04-20"
}
```

---

## 🏁 Zamykanie zadania

### 🧠 Opcja 1: z pomocą AI

- W `TaskCardView` użytkownik klika „Zakończ z pomocą AI”
- Wpisuje `summary` – krótkie podsumowanie rozwiązania zadania

```http
PATCH /api/tasks/:id/ai-close

{
  "summary": "Zaimplementowano parser z walidacją pól CSV"
}
```

- Backend:
  - analizuje `summary` (funkcja `getSummaryAssessment`)
  - jeśli poprawne – ustawia `isCompleted = true`

### 📋 Opcja 2: manualnie (na podstawie innego zadania)

- Użytkownik wybiera inne zadanie jako źródło (`sourceTaskId`)
- System kopiuje `summary` z tego zadania

```http
PATCH /api/tasks/:id/close

{
  "sourceTaskId": "<taskId>"
}
```

---

## 🔍 Podobne zadania (`similarTasks`)

- W trakcie tworzenia zadania backend generuje embedding
- Pole `similarTasks` zawiera ID zadań podobnych tematycznie
- W przyszłości może być używane do:
  - podpowiedzi użytkownikowi
  - łatwiejszego zamykania zadań (duplikaty)

---

## 📌 Podsumowanie

| Etap                     | Komponenty frontend | Endpoint backend            | AI               |
|--------------------------|---------------------|------------------------------|------------------|
| Tworzenie                | `CreateTaskForm`    | `POST /tasks/ai-create`      | GPT generuje dane|
| Wyświetlanie             | `TaskCard`          | `GET /tasks`                 | ❌                |
| Edycja                   | `TaskCardEdit`      | `PATCH /tasks/:id`           | ❌                |
| Zamykanie z AI           | `TaskCardView`      | `PATCH /tasks/:id/ai-close`  | GPT ocenia        |
| Zamykanie manualne       | `TaskCardView`      | `PATCH /tasks/:id/close`     | ❌                |

---

## 📄 Dokumentacja powiązana

- `components.md` – komponenty UI zadań
- `hooks.md` – logika zarządzania stanem `TaskCard`
- `api_spec.md` – opis endpointów związanych z zadaniami
- `db_schema.md` – pola modelu `Task` (`difficulty`, `dueDate`, `summary`)