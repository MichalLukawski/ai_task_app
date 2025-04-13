# 📄 Dokumentacja bazy danych – AI Task App (aktualna wersja)

---

## 🧩 Model: User (`models/User.js`)

| Pole              | Typ      | Wymagane | Domyślna wartość | Opis                                                                    |
| ----------------- | -------- | -------- | ---------------- | ----------------------------------------------------------------------- |
| `_id`             | ObjectId | ✅       | automatyczna     | Unikalny identyfikator użytkownika                                      |
| `email`           | String   | ✅       | -                | Adres e-mail użytkownika                                                |
| `password`        | String   | ✅       | -                | Haszowane hasło użytkownika                                             |
| `role`            | String   | ❌       | `"user"`         | Rola w systemie: `user` lub `admin`                                     |
| `emailVerified`   | Boolean  | ❌       | `false`          | Czy użytkownik potwierdził adres e-mail (planowane)                     |
| `approvedByAdmin` | Boolean  | ❌       | `false`          | Czy konto zostało zatwierdzone ręcznie przez administratora (planowane) |
| `createdAt`       | Date     | ❌       | teraz            | Data rejestracji użytkownika                                            |

> 🔐 Rejestracja wymaga weryfikacji e-mail (jeśli aktywna) i zatwierdzenia przez administratora (`approvedByAdmin = true`), zanim użytkownik będzie mógł się zalogować.

---

## ✅ Model: Task (`models/Task.js`)

| Pole           | Typ                  | Wymagane | Domyślna wartość | Opis                                                 |
| -------------- | -------------------- | -------- | ---------------- | ---------------------------------------------------- |
| `_id`          | ObjectId             | ✅       | automatyczna     | Unikalny identyfikator MongoDB                       |
| `ownerId`      | ObjectId (ref: User) | ✅       | -                | Właściciel zadania (powiązanie z użytkownikiem)      |
| `title`        | String               | ✅       | ""               | Tytuł zadania (generowany przez AI lub ręcznie)      |
| `description`  | String               | ✅       | -                | Opis zadania (manualnie lub z AI)                    |
| `dueDate`      | Date (ISO)           | ❌       | null             | Termin wykonania zadania (jeśli podany)              |
| `difficulty`   | Number (1–5)         | ❌       | -                | Ocena trudności (skala 1–5) generowana przez AI      |
| `summary`      | String               | ❌       | -                | Podsumowanie zadania po jego zamknięciu              |
| `notes`        | String               | ❌       | ""               | Dodatkowe notatki użytkownika                        |
| `similarTasks` | ObjectId[]           | ❌       | -                | Lista podobnych zadań (znalezionych przez embedding) |
| `embedding`    | Float[]              | ❌       | -                | Wektor semantyczny dla `title + description`         |
| `status`       | "open" / "closed"    | ✅       | "open"           | Aktualny status zadania                              |
| `createdAt`    | Date                 | ✅       | teraz            | Data utworzenia                                      |
| `closedAt`     | Date                 | ❌       | -                | Data zamknięcia (jeśli dotyczy)                      |

> Embedding i podobne zadania generowane są automatycznie po utworzeniu zadania. Nie są edytowane ręcznie.

---

## 🔐 Model: ApiKey (`models/ApiKey.js`)

| Pole           | Typ      | Wymagane | Opis                                                               |
| -------------- | -------- | -------- | ------------------------------------------------------------------ |
| `_id`          | ObjectId | ✅       | Identyfikator rekordu                                              |
| `scope`        | String   | ✅       | Zakres zastosowania klucza (`"global"` lub w przyszłości `userId`) |
| `encryptedKey` | String   | ✅       | Zaszyfrowany klucz OpenAI (base64)                                 |
| `iv`           | String   | ✅       | Wektor inicjalizacyjny (base64)                                    |
| `tag`          | String   | ✅       | Tag autoryzacyjny AES-256-GCM (base64)                             |
| `createdAt`    | Date     | ❌       | Data dodania rekordu                                               |
| `rotatedAt`    | Date     | ❌       | Data ostatniej rotacji klucza                                      |

> Klucz OpenAI może być przechowywany bezpiecznie w bazie MongoDB i odszyfrowywany tylko przez backend. W przypadku jego braku można użyć fallbacku z `.env`.

---

## 🧠 Relacje między modelami

- `User` → `Task` (jeden do wielu przez `ownerId`)
- `Task` → `Task` (wiele do wielu przez `similarTasks`)
- `ApiKey` – osobny model systemowy, niepowiązany z użytkownikami (jeszcze)

---

## 📄 Dokumentacja powiązana

- `controllers.md`
- `services.md`
- `validators.md`
- `middleware.md`
- `api_spec.md`
- `project_overview.md`
