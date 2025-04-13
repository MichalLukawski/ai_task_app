# 📘 Dokumentacja tras – AI Task App (aktualna wersja)

Dokument zawiera listę tras (endpointów) backendu AI Task App z podziałem na moduły routingu oraz zastosowane middleware.

---

## 🔐 Trasy: `authRoutes.js`

| Metoda | Ścieżka              | Opis                           | Dostęp |
| ------ | -------------------- | ------------------------------ | ------ |
| POST   | `/api/auth/register` | Rejestracja nowego użytkownika | public |
| POST   | `/api/auth/login`    | Logowanie użytkownika          | public |

> 🔒 Po rejestracji użytkownik nie może się zalogować, dopóki nie zostanie zatwierdzony (`approvedByAdmin: true`). Możliwe również dodanie potwierdzania e-mail.

---

## 🧠 Trasy: `taskRoutes.js`

Wszystkie trasy wymagają autoryzacji JWT (middleware `auth.js`).

| Metoda | Ścieżka                   | Opis                                                              | Middleware                                       |
| ------ | ------------------------- | ----------------------------------------------------------------- | ------------------------------------------------ |
| POST   | `/api/tasks`              | Tworzy nowe zadanie ręcznie                                       | `auth`, `validateTaskInput`, `validate`          |
| POST   | `/api/tasks/ai-create`    | Tworzy zadanie z pomocą GPT-4o                                    | `auth`, `validateCreateTaskWithAI`, `validate`   |
| GET    | `/api/tasks`              | Pobiera wszystkie zadania użytkownika                             | `auth`                                           |
| PATCH  | `/api/tasks/:id`          | Edytuje istniejące zadanie                                        | `auth`, `validateUpdateTaskInput`, `validate`    |
| PATCH  | `/api/tasks/:id/ai-close` | Zamyka zadanie z pomocą AI (ocena/wygładzenie `summary`, `force`) | `auth`, `validateCloseTaskWithAI`, `validate`    |
| PATCH  | `/api/tasks/:id/close`    | Zamyka zadanie kopiując `summary` z innego zadania                | `auth`, `validateCloseTaskFromOther`, `validate` |

---

## ⚙️ Trasy: `systemRoutes.js`

| Metoda | Ścieżka                  | Opis                                         | Middleware |
| ------ | ------------------------ | -------------------------------------------- | ---------- |
| POST   | `/api/system/openai-key` | Zapisuje zaszyfrowany klucz OpenAI (AES-256) | `auth`     |

> 🔐 Klucz OpenAI przechowywany jest zaszyfrowany w MongoDB (model `ApiKey`). Jeśli nie istnieje – możliwy fallback do `.env`.

---

## 🧩 Uwagi

- Endpoint `POST /api/tasks/ai-create` wywołuje GPT-4o przez function calling.
- Endpoint `PATCH /api/tasks/:id/ai-close` nie obsługuje `sourceTaskId`.
- Endpoint `PATCH /api/tasks/:id/close` służy **wyłącznie do kopiowania** podsumowania z innego zadania.
- Wszystkie endpointy `PATCH` aktualizują tylko część zasobu (`status`, `summary`, `similarTasks`).
