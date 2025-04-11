# 📄 Dokumentacja bazy danych – AI Task App

## 🧩 Model: User

| Pole        | Typ      | Wymagane | Domyślna wartość | Opis                               |
| ----------- | -------- | -------- | ---------------- | ---------------------------------- |
| `_id`       | ObjectId | ✅       | automatyczna     | Unikalny identyfikator użytkownika |
| `email`     | String   | ✅       | -                | Adres e-mail użytkownika           |
| `password`  | String   | ✅       | -                | Haszowane hasło użytkownika        |
| `role`      | String   | ❌       | "user"           | Rola w systemie (user/admin)       |
| `createdAt` | Date     | ❌       | teraz            | Data rejestracji użytkownika       |

## ✅ Model: Task

| Pole          | Typ                  | Wymagane | Domyślna wartość | Opis                                  |
| ------------- | -------------------- | -------- | ---------------- | ------------------------------------- |
| `_id`         | ObjectId             | ✅       | automatyczna     | Unikalny identyfikator MongoDB        |
| `ownerId`     | ObjectId (ref: User) | ✅       | -                | Właściciel zadania                    |
| `title`       | String               | ❌       | ""               | Tytuł (może być generowany przez AI)  |
| `description` | String               | ✅       | -                | Opis zadania                          |
| `notes`       | String               | ❌       | ""               | Notatki wygenerowane przez AI         |
| `status`      | String               | ❌       | "open"           | Status: `open` lub `closed`           |
| `dueDate`     | Date                 | ❌       | null             | Termin wykonania zadania (opcjonalny) |
| `createdAt`   | Date                 | ❌       | teraz            | Data utworzenia zadania               |
| `closedAt`    | Date                 | ❌       | -                | Data zamknięcia                       |

---

Plik ten powinien być aktualizowany przy każdej zmianie modeli danych w folderze `models/` backendu.

---

## ✅ Aktualizacja modelu Task (2025-04-11)

Zgodnie z najnowszymi zmianami w kodzie aplikacji i strukturze danych:

### Nowy model `Task`

| Nazwa          | Typ            | Wymagane  | Opis                                                   |
| -------------- | -------------- | --------- | ------------------------------------------------------ | -------------- |
| `title`        | `string`       | ✅        | Tytuł zadania, generowany przez GPT                    |
| `description`  | `string`       | ✅        | Opis zadania (główny input)                            |
| `dueDate`      | `Date` (ISO)   | ❌        | Termin wykonania                                       |
| `difficulty`   | `number (1–5)` | ❌        | Trudność zadania – oceniana przez AI                   |
| `summary`      | `string`       | ❌        | Podsumowanie zadania po zakończeniu                    |
| `notes`        | `string`       | ❌        | Pole opcjonalne, uzupełniane ręcznie przez użytkownika |
| `similarTasks` | `ObjectId[]`   | ❌        | ID zadań podobnych (znalezione przez embedding)        |
| `embedding`    | `float[]`      | ❌        | Wektor embedding wygenerowany z `title + description`  |
| `status`       | `"open"        | "closed"` | ✅                                                     | Status zadania |
| `createdAt`    | `Date`         | ✅ (auto) | Data utworzenia                                        |
| `closedAt`     | `Date`         | ❌        | Data zamknięcia                                        |
| `ownerId`      | `ObjectId`     | ✅        | Użytkownik przypisany                                  |
