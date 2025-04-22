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

Model `Task` reprezentuje centralny byt domenowy aplikacji. Opisuje pojedyncze zadanie przypisane do konkretnego użytkownika, może być edytowane, zamykane ręcznie lub przez AI, zawiera pola analityczne (embedding), a także powiązania do podobnych zadań.

W modelu zastosowano szeroki zakres typów i relacji, aby umożliwić rozbudowaną analizę semantyczną, wnioskowanie przez GPT oraz dalszy rozwój kierunku „asystenta projektowego”.

| Pole           | Typ                    | Wymagane | Domyślna wartość | Opis                                                                                                                                    |
| -------------- | ---------------------- | -------- | ---------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| `_id`          | ObjectId               | ✅       | automatyczna     | Unikalny identyfikator MongoDB                                                                                                          |
| `ownerId`      | ObjectId (ref: User)   | ✅       | -                | Właściciel zadania (powiązanie z użytkownikiem)                                                                                         |
| `title`        | String                 | ✅       | ""               | Tytuł zadania (generowany przez AI lub ręcznie)                                                                                         |
| `description`  | String                 | ✅       | -                | Opis zadania (manualnie lub z AI)                                                                                                       |
| `dueDate`      | Date (ISO)             | ❌       | null             | Termin wykonania zadania (jeśli podany)                                                                                                 |
| `difficulty`   | Number (1–5)           | ❌       | -                | Ocena trudności (skala 1–5) generowana przez AI                                                                                         |
| `summary`      | String                 | ❌       | -                | Podsumowanie zadania po jego zamknięciu (wprowadzone przez użytkownika lub AI; może być przetworzone i ocenione semantycznie przez GPT) |
| `notes`        | String                 | ❌       | ""               | Dodatkowe notatki użytkownika                                                                                                           |
| `similarTasks` | ObjectId[] (ref: Task) | ❌       | -                | Lista podobnych zadań (znalezionych przez embedding i `generateSimilarTasks` lub przypisanych ręcznie)                                  |
| `embedding`    | Float[]                | ❌       | -                | Wektor semantyczny dla `title + description`                                                                                            |
| `status`       | "open" / "closed"      | ✅       | "open"           | Aktualny status zadania. Zadania zamykane są poprzez AI (`/ai-close`), ręcznie (`/close`) lub przez skopiowanie (`/close-copy`)         |
| `createdAt`    | Date                   | ✅       | teraz            | Data utworzenia                                                                                                                         |
| `closedAt`     | Date                   | ❌       | -                | Data zamknięcia (jeśli dotyczy)                                                                                                         |

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

---

## 🔍 Uwagi na temat zachowania pól

- Pole `summary` jest wymagane przy zamykaniu zadania (zarówno ręcznie jak i przez AI). Jego treść może zostać automatycznie poprawiona przez `processTaskClosure`.
- Pole `closedAt` jest ustawiane automatycznie przez backend w momencie zakończenia zadania.
- Zadania z `status: "closed"` nie mogą być ponownie edytowane ani zamykane.
- W przypadku usunięcia zadania (`DELETE /tasks/:id`) rekord znika trwale z bazy (brak logiki kosza na ten moment).

---
