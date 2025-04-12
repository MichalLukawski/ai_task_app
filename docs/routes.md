# 📘 Dokumentacja tras – AI Task App

## 🔐 Trasy: `authRoutes.js`

| Metoda | Ścieżka              | Opis                           | Dostęp |
| ------ | -------------------- | ------------------------------ | ------ |
| POST   | `/api/auth/register` | Rejestracja nowego użytkownika | public |
| POST   | `/api/auth/login`    | Logowanie użytkownika          | public |

---

## 🗂️ Trasy: `taskRoutes.js`

Wszystkie poniższe trasy **wymagają autoryzacji JWT** (middleware `auth.js`).

| Metoda | Ścieżka                   | Opis                                                              | Middleware                                     |
| ------ | ------------------------- | ----------------------------------------------------------------- | ---------------------------------------------- |
| POST   | `/api/tasks`              | Tworzy nowe zadanie ręcznie                                       | `auth`, `validateTaskInput`, `validate`        |
| GET    | `/api/tasks`              | Pobiera wszystkie zadania użytkownika                             | `auth`                                         |
| PUT    | `/api/tasks/:id`          | Edytuje dane istniejącego zadania                                 | `auth`, `validateTaskInput`, `validate`        |
| PATCH  | `/api/tasks/:id/close`    | Zamyka zadanie kopiując `summary` z innego zadania                | `auth`                                         |
| PATCH  | `/api/tasks/:id/ai-close` | Zamyka zadanie z pomocą AI (ocena/wygładzenie `summary`, `force`) | `auth`, `validateCloseTaskInput`, `validate`   |
| POST   | `/api/tasks/ai-create`    | Tworzy zadanie z pomocą GPT-4o                                    | `auth`, `validateCreateTaskWithAI`, `validate` |

---

## 🧩 Uwagi

- Endpoint `/api/tasks/:id/ai-close` nie obsługuje `sourceTaskId`.
- Endpoint `/api/tasks/:id/close` służy **wyłącznie do kopiowania** `summary` z innego zadania.
- Oba endpointy wykorzystują `PATCH`, ponieważ aktualizują tylko część zasobu (`status`, `summary`).
