## API Integration – dokumentacja integracji frontendu z backendem

### Przegląd

Ten dokument opisuje szczegółowo sposób, w jaki frontend aplikacji AI Task App komunikuje się z backendem za pomocą zapytań HTTP. Obejmuje on zarówno ogólną strukturę integracji API, jak i konkretne przypadki zastosowania związane z zarządzaniem zadaniami, w tym: tworzenie, edycję, zapisywanie i synchronizację danych. Dokument obejmuje również mechanizm komunikacji z OpenAI (w backendzie) i synchronizację danych z wykorzystaniem nowoczesnych wzorców REST.

Wszystkie operacje API wykonywane są z użyciem asynchronicznych zapytań przez niestandardowy hook `useApi`, który jest wrapperem dla `fetch`, z obsługą tokenów autoryzacyjnych oraz błędów HTTP.

---

### Ogólna architektura komunikacji

#### `useApi`
Hook `useApi` jest dostępny w całej aplikacji React. Odpowiada za:
- dołączanie nagłówków autoryzacyjnych (token JWT),
- automatyczne parsowanie odpowiedzi (`res.json()`),
- przechwytywanie i logowanie błędów,
- uproszczony interfejs `api.get`, `api.post`, `api.patch`, `api.delete`.

Przykład użycia:
```js
const api = useApi();
const data = await api.get('/tasks');
```

---

### Operacje na zadaniach (Tasks)

#### Pobieranie zadań (GET /tasks)
- Endpoint: `GET /tasks`
- Zwraca tablicę zadań przypisanych do użytkownika.
- Wykonywane przy ładowaniu `DashboardPage`.

#### Tworzenie zadania (POST /tasks)
- Endpoint: `POST /tasks`
- Wysyła dane zadania otrzymane od użytkownika lub wygenerowane przez GPT.
- Wymagane pola: `title`, `description`, `difficulty`
- Opcjonalne pola: `dueDate`
- Backend waliduje obecność i poprawność danych zgodnie ze schematem Mongoose.

#### Aktualizacja zadania (PATCH /tasks/:id)
- Endpoint: `PATCH /tasks/:id`
- Służy do aktualizacji pojedynczego pola lub zestawu pól zadania (np. `dueDate`, `difficulty`).
- Po zakończeniu `PATCH`, frontend wykonuje dodatkowe zapytanie `GET /tasks/:id`, aby pobrać zaktualizowaną wersję zadania z bazy danych.
- Dzięki temu zapobiega niespójnościom między stanem lokalnym a rzeczywistym stanem danych w backendzie.

Schemat:
```js
await api.patch(`/tasks/${id}`, { difficulty: 3 });
await api.get(`/tasks/${id}`); // odświeżenie danych
```

#### Synchronizacja z komponentem nadrzędnym
- Po otrzymaniu świeżych danych, komponent `TaskCard` przekazuje je do komponentu nadrzędnego (`DashboardPage`) przez `onTaskUpdated(task)`.
- `DashboardPage` aktualizuje listę zadań w swoim stanie.

#### Usuwanie zadania (DELETE /tasks/:id)
- Endpoint: `DELETE /tasks/:id`
- Usuwa wybrane zadanie z bazy danych.
- Aktualizacja listy po stronie klienta wykonywana jest ręcznie (usunięcie z listy zadań w stanie komponentu).

---

### Obsługa zapisu i feedbacku użytkownika

#### Zapis z informacją zwrotną
- Frontend pokazuje `🔄 Zapisuję...` podczas trwania `PATCH` + `GET`.
- Po otrzymaniu odpowiedzi i uaktualnieniu danych pokazuje się `✔ Zapisano`.
- Wykorzystano lokalne stany `isSaving`, `showSaved`.

#### Błędy zapisu
- Błędy pochodzące z backendu (np. walidacja Mongoose) są przechwytywane i mogą być prezentowane użytkownikowi w formie alertu.
- W przyszłości można rozbudować o mechanizm `toast` lub system powiadomień.

---

### Integracja z OpenAI API (backend)

#### Function calling (tworzenie zadań przez GPT)
- Backend wykorzystuje `OpenAI.chat.completions.create` z `tools` i `tool_choice`, aby uzyskać dane strukturalne dla nowego zadania.
- Funkcja `getTaskStructureFromAI(description)` zwraca:
  - `title` (string, wymagany)
  - `description` (string, wymagany)
  - `difficulty` (number, wymagany)
  - `dueDate` (string ISO, opcjonalnie)

#### Parsowanie danych z GPT
- Zwrócone `function.arguments` są stringiem JSON i muszą być sparsowane przez `JSON.parse()`.
- Dodano walidację wynikową po stronie backendu (np. sprawdzenie `title?.trim()`).

---

### Dobre praktyki i zalecenia

- Po każdej modyfikacji danych zaleca się wykonanie `GET` w celu ich synchronizacji.
- Nigdy nie należy polegać wyłącznie na stanie lokalnym (`useState`) jako źródle prawdy.
- Przy integracji z GPT należy zawsze parsować dane i walidować je po stronie backendu.
- W przypadku wystąpienia błędów `PATCH` lub `POST`, nie należy aktualizować stanu klienta bez walidacji odpowiedzi.

---

### Podsumowanie

Integracja API w AI Task App oparta jest o solidne fundamenty REST i wspiera nowoczesne podejście do synchronizacji frontend-backend. Dzięki refetchowaniu po każdej modyfikacji, system zapewnia spójność danych oraz pozytywne doświadczenie użytkownika. Wdrożenie komunikacji z GPT przez function calling zwiększa inteligencję i automatyzację tworzenia zadań. Dokument ten powinien być aktualizowany wraz z rozwojem endpointów i modeli danych.