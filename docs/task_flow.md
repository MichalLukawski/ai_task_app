# 📊 Task Flow – AI Task App (zaktualizowana i rozszerzona wersja)

Niniejszy dokument przedstawia pełny, szczegółowy przepływ tworzenia, edytowania, zamykania i obsługi zadań (`tasks`) w aplikacji AI Task App. Obejmuje zarówno procesy wykonywane ręcznie przez użytkownika, jak i działania zautomatyzowane lub wspomagane przez sztuczną inteligencję (GPT-4o). Uwzględniono tu również komunikację pomiędzy frontendem a backendem, sposób synchronizacji stanu oraz interakcje użytkownika z komponentami UI.

Celem dokumentu jest zbudowanie kompletnego obrazu sposobu działania warstwy zadań w kontekście danych, interakcji z API oraz logiki aplikacji.

---

## 1️⃣ Tworzenie zadania ręcznego (`POST /api/tasks`)

### Etapy:

1. Użytkownik wypełnia formularz `CreateTaskForm`
2. Pola obowiązkowe: `description` (min. 5 znaków)
3. Dodatkowe pola: `title`, `dueDate`, `difficulty`
4. Walidacja danych po stronie frontend (podstawowa) i backend (`taskValidator.js`)
5. Wysłanie żądania `POST` na endpoint
6. Backend zapisuje zadanie w MongoDB (`ownerId` = `req.user.id`)
7. Backend zwraca pełny obiekt `task` (z ID, timestampami itp.)

### Efekt:

- Zadanie pojawia się natychmiast w liście zadań (`DashboardPage`)
- Brak `embedding` i `similarTasks` (nie są generowane dla ręcznych zadań)

---

## 2️⃣ Tworzenie zadania z pomocą AI (`POST /api/tasks/ai-create`)

### Etapy:

1. Użytkownik wpisuje naturalny opis zadania (np. "Strona ładuje się zbyt wolno na urządzeniach mobilnych")
2. Frontend wysyła `POST` do `/api/tasks/ai-create`
3. Backend uruchamia funkcję `getTaskStructureFromAI(description)`:
   - Wywołuje GPT-4o z użyciem `function_calling`
   - Otrzymuje strukturę: `title`, `description`, `difficulty`, `dueDate`
   - Wynik `function.arguments` jest parsowany przez `JSON.parse(...)`
   - Przeprowadzana jest walidacja zwróconych danych
4. Tworzone jest nowe zadanie (`Task.create(...)`)
5. Backend wykonuje `generateAndAttachEmbedding(taskId)`:
   - Generuje embedding przez OpenAI (model `text-embedding-3-small`)
   - Porównuje embedding z zakończonymi zadaniami
   - Przypisuje `similarTasks` (jeśli podobieństwo ≥ 0.75, max 5)
6. Zapis zadania i zwrot pełnego obiektu `task` do frontendu

---

## 3️⃣ Edycja zadania (`PATCH /api/tasks/:id`)

### Od strony frontend:

1. Komponent `TaskCard` uruchamia hook `useTaskCardState(task, onTaskUpdated)`
2. Po kliknięciu w kartę następuje fokus (`isFocused = true`)
3. Użytkownik może edytować:
   - `dueDate` (komponent: `DueDateEditor`, `input[type=date]`)
   - `difficulty` (komponent: `DifficultySelector`, `select`)
4. Edytowane wartości przechowywane są lokalnie w `editedTask`
5. Zmiany są natychmiast widoczne w UI, ale **nie zapisywane**
6. Zapis danych (`PATCH`) następuje tylko gdy:
   - użytkownik kliknie poza pole edycji (ale wewnątrz karty),
   - kliknie ponownie w kartę (jeśli była fokusowana),
   - naciśnie `Enter` w trybie edycji

### Mechanizm zapisu:

- Wywoływana jest funkcja `save()`, która:
  - wykonuje `PATCH /api/tasks/:id`
  - po sukcesie wywołuje `GET /api/tasks/:id` w celu pobrania aktualnych danych z backendu (synchronizacja)
  - ustawia `showSaved = true` na 1.5 sekundy
  - ustawia `isSaving = false` po zakończeniu operacji

### Od strony backend:

- Aktualizacja danych przy pomocy `findOneAndUpdate`
- Walidacja wejściowa z `validateUpdateTaskInput`
- Zwracany jest pełny, zaktualizowany obiekt `task`
- Obsługa błędów (np. pusty `title`) zakończona statusem 400

---

## 4️⃣ Zamykanie zadania z pomocą AI (`PATCH /api/tasks/:id/ai-close`)

### Scenariusz:

- Użytkownik wpisuje podsumowanie (`summary`) wykonanych działań
- Opcjonalnie może zaznaczyć `force`, aby wymusić zaakceptowanie

### Backend:

1. Wywoływana funkcja `processTaskClosure({ task, userSummary, force })`
2. Uruchamiane są:
   - `getSummaryAssessment(...)` – GPT ocenia jakość opisu
   - `improveSummary(...)` – wygładza język podsumowania
3. Jeśli wynik jest zaakceptowany:
   - zapis `task.summary`, `status = closed`, `closedAt = now`
4. Zwracany pełen obiekt `task`

---

## 5️⃣ Zamykanie zadania przez kopiowanie (`PATCH /api/tasks/:id/close`)

### Scenariusz:

- Użytkownik wybiera inne zakończone zadanie (`sourceTaskId`)
- Chce skopiować `summary`

### Backend:

1. Sprawdzana jest obecność `sourceTaskId` oraz `summary` w źródle
2. Wartość `summary` jest kopiowana do obecnego zadania
3. Zadanie jest zamykane (`status = closed`, `closedAt = now`)
4. Zwracany pełny obiekt `task`

---

## 6️⃣ Odświeżanie listy zadań (`GET /api/tasks`)

- Zadania pobierane przy pierwszym załadowaniu `DashboardPage`
- Endpoint: `GET /api/tasks`
- Dane sortowane po `createdAt` malejąco
- Aktualizacja listy odbywa się również po:
  - `onTaskUpdated(task)` – pojedyncze zadanie zaktualizowane
  - zamknięciu zadania
  - utworzeniu nowego

---

## 7️⃣ Walidacja i bezpieczeństwo

- Wszystkie endpointy `/tasks/*` zabezpieczone przez `auth.js` (middleware JWT)
- Wszystkie dane walidowane w `taskValidator.js`
- W przypadku błędu:
  - status `400 Bad Request`
  - `errorCode: VALIDATION_ERROR` (np. brak `title`, zła data)

---

## 🔄 Zależności komponentów frontend

- `TaskCard.jsx` – logika fokusowania, zapisu, blur, enter, zamykania
- `TaskCardView.jsx` – renderuje widok edytowalny/pasywny
- `useTaskCardState.jsx` – przechowuje `editedTask`, obsługuje `save()`
- `DueDateEditor.jsx`, `DifficultySelector.jsx` – edytory inline
- `DashboardPage.jsx` – przechowuje listę zadań, reaguje na `onTaskUpdated`

---

## 📄 Dokumenty powiązane

- `api_spec.md` – schematy danych i struktur odpowiedzi
- `controllers.md` – logika tworzenia i zamykania zadań
- `routes.md` – dostępność tras
- `validators.md` – reguły sprawdzające dane wejściowe
- `project_overview.md`, `backend_overview.md` – warstwa architektury
