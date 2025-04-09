# 📄 Dokumentacja bazy danych – AI Task App

## 🧩 Model: User

| Pole       | Typ       | Wymagane | Domyślna wartość | Opis                                |
|------------|-----------|----------|------------------|-------------------------------------|
| `_id`      | ObjectId  | ✅        | automatyczna     | Unikalny identyfikator użytkownika |
| `email`    | String    | ✅        | -                | Adres e-mail użytkownika            |
| `password` | String    | ✅        | -                | Haszowane hasło użytkownika         |
| `role`     | String    | ❌        | "user"           | Rola w systemie (user/admin)        |
| `createdAt`| Date      | ❌        | teraz            | Data rejestracji użytkownika        |


## ✅ Model: Task

| Pole        | Typ           | Wymagane | Domyślna wartość | Opis                                  |
|-------------|----------------|----------|------------------|---------------------------------------|
| `_id`       | ObjectId       | ✅        | automatyczna     | Unikalny identyfikator MongoDB       |
| `ownerId`   | ObjectId (ref: User) | ✅    | -                | Właściciel zadania                   |
| `title`     | String         | ❌        | ""               | Tytuł (może być generowany przez AI) |
| `description` | String       | ✅        | -                | Opis zadania                         |
| `notes`     | String         | ❌        | ""               | Notatki wygenerowane przez AI        |
| `status`    | String         | ❌        | "open"           | Status: `open` lub `closed`          |
| `createdAt` | Date           | ❌        | teraz            | Data utworzenia zadania              |
| `closedAt`  | Date           | ❌        | -                | Data zamknięcia                      |

---

Plik ten powinien być aktualizowany przy każdej zmianie modeli danych w folderze `models/` backendu.
