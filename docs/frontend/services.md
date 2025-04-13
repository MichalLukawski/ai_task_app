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

## 📄 Przykład refaktoryzacji (LoginPage)

Zamiast:

```js
const res = await fetch('/api/auth/login', ...);
```

Użyj:

```js
const token = await authService.login(email, password);
auth.login(token);
navigate("/tasks");
```

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
