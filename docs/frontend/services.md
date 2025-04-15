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
