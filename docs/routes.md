# 📘 Dokumentacja tras – AI Task App

## 🔐 Trasy: `authRoutes.js`

| Metoda | Ścieżka             | Opis                            | Dostęp |
|--------|---------------------|----------------------------------|--------|
| POST   | `/api/auth/register` | Rejestracja nowego użytkownika  | public |
| POST   | `/api/auth/login`    | Logowanie użytkownika           | public |

---

## 🗂️ Trasy: `taskRoutes.js`

Wszystkie poniższe trasy **wymagają autoryzacji JWT** (middleware `auth.js`).

| Metoda | Ścieżka                | Opis                                         |
|--------|------------------------|----------------------------------------------|
| POST   | `/api/tasks`           | Tworzy nowe zadanie                          |
| GET    | `/api/tasks`           | Pobiera wszystkie zadania użytkownika       |
| PUT    | `/api/tasks/:id`       | Edytuje dane istniejącego zadania           |
| POST   | `/api/tasks/:id/close` | Zamyka zadanie (ustawia status + closedAt)  |

---

Każda trasa dla zadań przekazuje kontrolę do funkcji z `taskController.js` i jest chroniona przez `authMiddleware`.

