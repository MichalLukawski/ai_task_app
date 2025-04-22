# 🔌 Dokumentacja – `services/` (Frontend AI Task App – planowana struktura)

📌 **Uwaga:** W aktualnym stanie projektu logika komunikacji z API (np. logowanie, rejestracja, tworzenie zadań) znajduje się bezpośrednio w komponentach (`LoginPage`, `TasksPage`, itd.).

Ten dokument przedstawia **rekomendowaną strukturę katalogu `services/`**, która powinna zostać wdrożona w kolejnych etapach rozwoju projektu. Ułatwi to modularność, testowanie i reużywalność kodu.

---

## 📁 Planowana struktura katalogu `services/`

```
services/
├── authService.js     # Obsługuje logowanie i rejestrację użytkownika
├── taskService.js     # Obsługuje operacje na zadaniach (GET, POST, PATCH)
├── api.js             # (opcjonalny) helper do ustawiania bazowego URL i fetch()
```

---

## 🔑 authService.js

### ✅ `login(email, password)`

- Wysyła `POST /api/auth/login`
- Zwraca `token` lub rzuca wyjątek

### ✅ `register(email, password)`

- Wysyła `POST /api/auth/register`
- Obsługuje walidację i błędy

---

## 📋 taskService.js

### ✅ `getTasks(token)`

- Wysyła `GET /api/tasks`
- Zwraca listę zadań użytkownika

### ✅ `createTaskWithAI(description, token)`

- Wysyła `POST /api/tasks/ai-create`
- Zwraca dane wygenerowane przez AI

### ✅ `closeTaskWithAI(taskId, summary, force, token)`

- Zamyka zadanie z pomocą AI (ocena i wygładzenie `summary`)

### ✅ `closeTaskFromOther(taskId, sourceTaskId, token)`

- Kopiuje `summary` z innego zadania

---

## 🌍 api.js (opcjonalny)

- Centralna konfiguracja `VITE_API_URL`
- Wrapper na `fetch` (opcjonalny)
- Może zawierać funkcje `get()`, `post()`, `patch()` z domyślnymi nagłówkami

---

## 🧠 Zasady projektowe

- Moduły eksportują funkcje async
- Używają `fetch` z nagłówkiem `Authorization` i `Content-Type: application/json`
- W przypadku błędów backendu → rzucają `throw new Error(message)`
- Komponenty (np. `LoginPage`) obsługują `.catch()`

---

## 📘 Przykładowe usługi

### 🔐 `authService.js`

```js
import axios from "../api/axios";

export const login = async (credentials) => {
  const response = await axios.post("/api/auth/login", credentials);
  return response.data;
};

export const register = async (user) => {
  const response = await axios.post("/api/auth/register", user);
  return response.data;
};
```

### ✅ `taskService.js`

```js
import axios from "../api/axios";

export const createTaskWithAI = async (description) => {
  const response = await axios.post("/api/tasks/ai-create", { description });
  return response.data;
};

export const updateTask = async (id, updates) => {
  return axios.patch(`/api/tasks/${id}`, updates);
};

export const closeTask = async (id, sourceTaskId) => {
  return axios.patch(`/api/tasks/${id}/close`, { sourceTaskId });
};

export const aiCloseTask = async (id, summary) => {
  return axios.patch(`/api/tasks/${id}/ai-close`, { summary });
};
```

---

## 🧪 Korzyści z podziału na usługi

| Korzyść                         | Opis                                                          |
| ------------------------------- | ------------------------------------------------------------- |
| 🔄 Reużywalność                 | Funkcje usług mogą być używane w wielu komponentach i hookach |
| 🔎 Testowalność                 | Logika biznesowa oddzielona od komponentów UI                 |
| 🔍 Czytelność                   | Komponenty stają się czystsze, skupione na interfejsie        |
| 📦 Gotowość na lazy loading     | Możliwość importu dynamicznego według potrzeb                 |
| 🔧 Możliwość refaktoryzacji API | Łatwa wymiana backendu bez ingerencji w warstwę prezentacji   |

---

## 🧩 Powiązania z innymi modułami

| Moduł          | Zależność                              |
| -------------- | -------------------------------------- |
| `AuthContext`  | używa `login()` z `authService.js`     |
| `LoginPage`    | wywołuje `login()`                     |
| `RegisterPage` | wywołuje `register()`                  |
| `TasksPage`    | używa `getTasks()`                     |
| `TaskFormPage` | (planowane) użyje `createTaskWithAI()` |

---

## 🚧 Aktualny status w kodzie

- ✅ `axios.js` skonfigurowany i używany w wielu komponentach
- ❌ Brak folderu `services/` – wywołania API znajdują się bezpośrednio w komponentach (`CreateTaskForm`, `TaskCardEdit`)
- 🛠️ Rekomendowane: przenieść logikę API z komponentów do usług (`taskService.js`, `authService.js`)
- 🔍 Komponenty takie jak `CreateTaskForm`, `TaskCardEdit` i `LoginPage` mogą zyskać na uproszczeniu po refaktoryzacji

---

## 🔜 Plan rozwoju

- [ ] Utworzenie folderu `services/`
- [ ] Przeniesienie wszystkich zapytań Axios do `taskService.js` i `authService.js`
- [ ] Stosowanie tych usług w komponentach i hookach
- [ ] Opcjonalnie: dodanie typów (JSDoc, TS) i testów jednostkowych

---

## 🚧 Możliwe rozszerzenia

- Automatyczne odświeżanie tokena (`refresh token`)
- Globalna obsługa błędów (`handleApiError`)
- Wrapper `ApiClient` z obsługą nagłówków, loadingu i retry

---

## 📄 Dokumentacja powiązana

- `context.md` – gdzie używane są metody z `authService`
- `pages.md` – które widoki wykorzystują jakie funkcje
- `routing.md` – dostępność tras dla różnych funkcji API
- `env.md` – zmienna `VITE_API_URL` jako źródło bazowego adresu

---

## 🧠 Aktualizacje usług (stan na 2025-04)

### 🔧 Nowe funkcje dla zadań (`taskService.js`)

- `deleteTask(id)`  
  Wysyła `DELETE /api/tasks/:id` i usuwa zadanie z bazy danych. Zwraca potwierdzenie z backendu.  
  **Użycie:** wykorzystywane przy kliknięciu `🗑️ Usuń` w komponencie `TaskCard`.

- `getTaskById(id)`  
  Wysyła `GET /api/tasks/:id` z nagłówkiem autoryzacji. Dane zwracane zawierają również `similarTasks[]`, `summary`, `closedAt`.  
  **Użycie:** automatyczne odświeżenie danych po zapisie AI lub edycji pól.

- `updateTaskField(id, field, value)`  
  Uniwersalna funkcja do aktualizacji pojedynczego pola (`dueDate`, `difficulty`, itp.)  
  Przykład: `updateTaskField(id, "difficulty", 3)`

- `aiCloseTask(id, summary, force = false)`  
  Rozszerzona wersja obsługi zamykania AI:

  - `force = false` → ocena AI
  - `force = true` → wymuszenie zapisu podsumowania mimo odrzucenia przez AI

- `manualCloseTask(id, summary)`  
  Wysyła `PATCH /api/tasks/:id/close` z polem `summary`.  
  Stosowane gdy użytkownik zatwierdza podsumowanie mimo ostrzeżenia AI.

---

### 📁 Propozycja aktualnej struktury `services/`

```
services/
├── api.js             # konfiguracja axios, nagłówki, token
├── authService.js     # logowanie, rejestracja
├── taskService.js     # zarządzanie zadaniami
```

---

## 📘 Przykłady aktualnych funkcji w `taskService.js`

```js
// taskService.js
import axios from "../api/axios";

export const deleteTask = async (id) => {
  return axios.delete(`/api/tasks/${id}`);
};

export const getTaskById = async (id) => {
  const res = await axios.get(`/api/tasks/${id}`);
  return res.data;
};

export const updateTaskField = async (id, field, value) => {
  return axios.patch(`/api/tasks/${id}`, { [field]: value });
};

export const aiCloseTask = async (id, summary, force = false) => {
  return axios.patch(`/api/tasks/${id}/ai-close`, { summary, force });
};

export const manualCloseTask = async (id, summary) => {
  return axios.patch(`/api/tasks/${id}/close`, { summary });
};
```

---

## 🔗 Aktualne użycie w aplikacji

| Funkcja           | Gdzie wykorzystywana                         |
| ----------------- | -------------------------------------------- |
| `deleteTask`      | `useTaskCardState.js` (hook), `TaskCardView` |
| `aiCloseTask`     | `useTaskCardState` → AI box                  |
| `manualCloseTask` | `AiSummaryRejectedModal`, `closeWithoutAI`   |
| `getTaskById`     | `refetchTask()` po zapisie                   |
| `updateTaskField` | `saveDueDate()`, `saveDifficulty()`          |

---

## 📦 Rekomendacje refaktoryzacyjne

- Stworzenie `useTaskService()` jako hooka opartego o `taskService.js`
- Wydzielenie stałych endpointów do osobnego pliku (np. `apiRoutes.js`)
- Globalna obsługa błędów (np. `useApiErrorBoundary`)

---
