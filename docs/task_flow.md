# 📊 Task Flow – AI Task App (zaktualizowana wersja)

Niniejszy dokument przedstawia pełny przepływ tworzenia, edytowania, zamykania i obsługi zadań (tasks) w aplikacji AI Task App. Obejmuje zarówno procesy wykonywane ręcznie przez użytkownika, jak i działania zautomatyzowane lub wspomagane przez sztuczną inteligencję (GPT-4o).

Celem dokumentu jest zbudowanie kompletnego obrazu sposobu działania warstwy zadań w kontekście danych, interakcji z API oraz komunikacji frontend ↔ backend.

---

## 1️⃣ Tworzenie zadania ręcznego (`POST /api/tasks`)

### Etapy:

1. Użytkownik wypełnia formularz `CreateTaskForm`
2. Pola obowiązkowe: `description` (min. 5 znaków)
3. Dodatkowe pola: `title`, `dueDate`, `difficulty`
4. Walidacja danych po stronie frontend i backend (`taskValidator.js`)
5. Wysłanie żądania POST
6. Backend zapisuje zadanie w MongoDB (`ownerId` = `req.user.id`)
7. Backend zwraca pełny obiekt `task`

### Efekt:

- Zadanie pojawia się w liście na dashboardzie
- Nie ma wygenerowanego `embedding` ani `similarTasks`

---

## 2️⃣ Tworzenie zadania z pomocą AI (`POST /api/tasks/ai-create`)

### Etapy:

1. Użytkownik wpisuje naturalny opis zadania (np. "Aplikacja mobilna zamyka się przy starcie")
2. Frontend wysyła POST do `/api/tasks/ai-create`
3. Backend uruchamia funkcję `getTaskStructureFromAI(description)`:
   - Wywołuje GPT-4o z `function_calling`
   - Otrzymuje: `title`, `description`, `difficulty`, `dueDate`
4. Tworzony jest task z tymi danymi
5. Backend wykonuje `generateAndAttachEmbedding(taskId)`:
   - Generuje embedding (OpenAI: `text-embedding-3-small`)
   - Porównuje z zamkniętymi zadaniami
   - Przypisuje `similarTasks` do obiektu `task`
6. Zadanie jest zapisywane i zwracane

---

## 3️⃣ Edycja zadania (`PATCH /api/tasks/:id`)

### Od strony frontend (kluczowe):

1. Komponent `TaskCard` zawiera `useTaskCardState(task, onTaskUpdated)`
2. Po kliknięciu w kartę aktywowany jest tryb edycji
3. Użytkownik może zmienić:
   - `difficulty` (komponent: `DifficultySelector`)
   - `dueDate` (komponent: `DueDateEditor`)
4. Wartości edytowane lokalnie w `editedTask` (`useState`)
5. Widok aktualizuje się od razu na podstawie `editedTask`
6. Zapis następuje **dopiero po:**
   - kliknięciu poza kartę (ale nie w pole edytowalne)
   - kliknięciu ponownym w kartę
   - naciśnięciu `Enter`
7. Funkcja `save()` wywołuje `PATCH /api/tasks/:id`
8. Odpowiedź zawiera pełny, zaktualizowany obiekt `task`
9. `onTaskUpdated(task)` aktualizuje listę zadań w `DashboardPage`

### Od strony backend:

- Dane są aktualizowane z użyciem `findOneAndUpdate`
- Walidacja przez `validateUpdateTaskInput`
- Odpowiedź standaryzowana: `sendSuccess(...)` z pełnym taskiem

---

## 4️⃣ Zamykanie zadania z pomocą AI (`PATCH /api/tasks/:id/ai-close`)

### Scenariusz:

- Użytkownik wpisuje podsumowanie (`summary`) wykonanych działań
- Może zaznaczyć `force`, jeśli chce wymusić zamknięcie mimo niedoskonałości tekstu

### Backend:

1. Wywołanie `processTaskClosure({ task, userSummary, force })`
2. Funkcja uruchamia:
   - `getSummaryAssessment(...)` – GPT-4o ocenia jakość
   - `improveSummary(...)` – wygładza styl
3. Jeśli tekst zaakceptowany → zapis jako `task.summary`, `status = closed`, `closedAt = now`
4. Zwracany pełen `task`

---

## 5️⃣ Zamykanie zadania przez kopiowanie podsumowania (`PATCH /api/tasks/:id/close`)

### Scenariusz:

- Użytkownik wybiera zakończone zadanie z `summary`
- Podaje `sourceTaskId`

### Backend:

1. Sprawdza, czy `sourceTaskId` istnieje i zawiera `summary`
2. Kopiuje `summary`, ustawia `status = closed`, `closedAt = now`
3. Zwraca pełny task

---

## 6️⃣ Odświeżanie listy zadań (`GET /api/tasks`)

- Zadania pobierane przy pierwszym załadowaniu dashboardu
- Sortowane malejąco po `createdAt`
- Frontend zapisuje wynik w stanie lokalnym
- `onTaskUpdated` aktualizuje konkretne zadanie po edycji

---

## 7️⃣ Walidacja i bezpieczeństwo

- Wszystkie trasy `/tasks/*` zabezpieczone przez `auth.js`
- Wszystkie dane wejściowe walidowane przez `taskValidator.js`
- W przypadku błędów zwracany `400` z `VALIDATION_ERROR`

---

## 🔄 Zależności komponentów frontend

- `TaskCard` → kontroluje tryb edycji i kliknięcia
- `TaskCardEdit` → pola: `dueDate`, `difficulty`
- `useTaskCardState` → zarządza `editedTask`, `save()`, `setIsEditing`
- `TaskCardView` → pokazuje wartości z `editedTask` nawet w trybie readonly

---

## 📄 Dokumenty powiązane

- `api_spec.md` – schematy danych i struktur odpowiedzi
- `controllers.md` – logika tworzenia i zamykania zadań
- `routes.md` – dostępność tras
- `validators.md` – reguły sprawdzające dane wejściowe
- `project_overview.md`, `backend_overview.md` – warstwa architektury
