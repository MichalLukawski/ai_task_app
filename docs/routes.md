# 📘 Dokumentacja tras – AI Task App

## 🔐 Trasy: `authRoutes.js`

| Metoda | Ścieżka             | Opis                            | Dostęp |
|--------|---------------------|----------------------------------|--------|
| POST   | `/api/auth/register` | Rejestracja nowego użytkownika  | public |
| POST   | `/api/auth/login`    | Logowanie użytkownika           | public |

---

## 🗂️ Trasy: `taskRoutes.js`

Wszystkie poniższe trasy **wymagają autoryzacji JWT** (middleware `auth.js`).

| Metoda | Ścieżka                | Opis                                         | Middleware                             |
|--------|------------------------|----------------------------------------------|----------------------------------------|
| POST   | `/api/tasks`           | Tworzy nowe zadanie                          | `auth.js`, `validateTaskInput`, `validate.js` |
| GET    | `/api/tasks`           | Pobiera wszystkie zadania użytkownika       | `auth.js`                              |
| PUT    | `/api/tasks/:id`       | Edytuje dane istniejącego zadania           | `auth.js`, `validateTaskInput`, `validate.js` |
| POST   | `/api/tasks/:id/close` | Zamyka zadanie (ustawia status + closedAt)  | `auth.js`                              |

---

Każda trasa dla zadań przekazuje kontrolę do funkcji z `taskController.js`, a wybrane trasy (`POST`, `PUT`) są dodatkowo chronione przez walidatory `express-validator`.

