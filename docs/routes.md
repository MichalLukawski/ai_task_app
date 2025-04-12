# 📘 Dokumentacja tras – AI Task App

## 🔐 Trasy: `authRoutes.js`

| Metoda | Ścieżka              | Opis                           | Dostęp |
| ------ | -------------------- | ------------------------------ | ------ |
| POST   | `/api/auth/register` | Rejestracja nowego użytkownika | public |
| POST   | `/api/auth/login`    | Logowanie użytkownika          | public |

---

## 🗂️ Trasy: `taskRoutes.js`

Wszystkie poniższe trasy **wymagają autoryzacji JWT** (middleware `auth.js`).

| Metoda | Ścieżka                   | Opis                                                               | Middleware                                         |
| ------ | ------------------------- | ------------------------------------------------------------------ | -------------------------------------------------- |
| POST   | `/api/tasks`              | Tworzy nowe zadanie                                                | `auth.js`, `validateTaskInput`, `validate.js`      |
| GET    | `/api/tasks`              | Pobiera wszystkie zadania użytkownika                              | `auth.js`                                          |
| PUT    | `/api/tasks/:id`          | Edytuje dane istniejącego zadania                                  | `auth.js`, `validateTaskInput`, `validate.js`      |
| POST   | `/api/tasks/:id/close`    | [NIEUŻYWANE] Stara wersja zamykania zadania                        | `auth.js`                                          |
| POST   | `/api/tasks/:id/ai-close` | Zamyka zadanie z pomocą AI lub kopiując `summary` z innego zadania | `auth.js`, `validateCloseTaskInput`, `validate.js` |
| POST   | `/api/tasks/ai-create`    | Tworzy zadanie z pomocą GPT-4o                                     | `auth.js`                                          |

---

### Uwaga:

Trasa `/api/tasks/:id/ai-close` obsługuje wszystkie aktualne scenariusze zamykania zadania:

- użytkownik wpisuje opis,
- użytkownik wymusza opis (`force`),
- użytkownik wybiera `sourceTaskId`,
- brak danych → błąd.

Trasa `/api/tasks/:id/close` została zachowana tymczasowo dla zgodności, ale nie jest już aktywnie używana.
