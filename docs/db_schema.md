# 📄 Dokumentacja bazy danych – AI Task App

## 🧩 Model: User

| Pole        | Typ      | Wymagane | Domyślna wartość | Opis                               |
| ----------- | -------- | -------- | ---------------- | ---------------------------------- |
| `_id`       | ObjectId | ✅       | automatyczna     | Unikalny identyfikator użytkownika |
| `email`     | String   | ✅       | -                | Adres e-mail użytkownika           |
| `password`  | String   | ✅       | -                | Haszowane hasło użytkownika        |
| `role`      | String   | ❌       | "user"           | Rola w systemie (user/admin)       |
| `createdAt` | Date     | ❌       | teraz            | Data rejestracji użytkownika       |

---

## ✅ Model: Task

| Pole           | Typ                  | Wymagane | Domyślna wartość | Opis                                                  |
| -------------- | -------------------- | -------- | ---------------- | ----------------------------------------------------- |
| `_id`          | ObjectId             | ✅       | automatyczna     | Unikalny identyfikator MongoDB                        |
| `ownerId`      | ObjectId (ref: User) | ✅       | -                | Właściciel zadania                                    |
| `title`        | String               | ✅       | ""               | Tytuł zadania, generowany przez GPT                   |
| `description`  | String               | ✅       | -                | Opis zadania (główny input)                           |
| `dueDate`      | Date (ISO)           | ❌       | null             | Termin wykonania                                      |
| `difficulty`   | Number (1–5)         | ❌       | -                | Trudność zadania – oceniana przez AI                  |
| `summary`      | String               | ❌       | -                | Podsumowanie zadania po zakończeniu                   |
| `notes`        | String               | ❌       | ""               | Notatki – uzupełniane ręcznie                         |
| `similarTasks` | ObjectId[]           | ❌       | -                | ID zadań podobnych (znalezione przez embedding)       |
| `embedding`    | Float[]              | ❌       | -                | Wektor embedding wygenerowany z `title + description` |
| `status`       | "open" / "closed"    | ✅       | "open"           | Status zadania                                        |
| `createdAt`    | Date                 | ✅       | teraz            | Data utworzenia                                       |
| `closedAt`     | Date                 | ❌       | -                | Data zamknięcia                                       |
