# 📘 Dokumentacja tras – AI Task App (zaktualizowana)

Dokument zawiera pełną listę tras (endpointów) backendu AI Task App z podziałem na moduły routingu oraz zastosowane middleware. Opisane zostały zarówno trasy publiczne (dostępne bez uwierzytelnienia), jak i chronione (wymagające tokena JWT).

Wszystkie odpowiedzi API zwracają jednolity format JSON (zdefiniowany w `utils/responseHandler.js`).

---

## 🔐 Trasy: `authRoutes.js`

| Metoda | Ścieżka              | Opis                           | Dostęp | Middleware                          |
| ------ | -------------------- | ------------------------------ | ------ | ----------------------------------- |
| POST   | `/api/auth/register` | Rejestracja nowego użytkownika | public | `validateRegisterInput`, `validate` |
| POST   | `/api/auth/login`    | Logowanie użytkownika          | public | `validateLoginInput`, `validate`    |

> 🛡️ Pola walidowane przez `validators/authValidator.js`.
> 🔐 Dane logowania są haszowane (`bcrypt`) i autoryzowane przez token JWT (7 dni).

---

## 🧠 Trasy: `taskRoutes.js`

Wszystkie poniższe trasy są chronione i wymagają tokena JWT (`auth.js`).

| Metoda | Ścieżka                   | Opis                                                          | Middleware                                       |
| ------ | ------------------------- | ------------------------------------------------------------- | ------------------------------------------------ |
| POST   | `/api/tasks`              | Tworzy nowe zadanie ręcznie                                   | `auth`, `validateTaskInput`, `validate`          |
| POST   | `/api/tasks/ai-create`    | Tworzy zadanie z pomocą GPT-4o                                | `auth`, `validateCreateTaskWithAI`, `validate`   |
| GET    | `/api/tasks`              | Pobiera wszystkie zadania użytkownika                         | `auth`                                           |
| PATCH  | `/api/tasks/:id`          | Częściowo aktualizuje dane zadania                            | `auth`, `validateUpdateTaskInput`, `validate`    |
| PATCH  | `/api/tasks/:id/ai-close` | Zamyka zadanie na podstawie oceny AI podsumowania (`summary`) | `auth`, `validateCloseTaskWithAI`, `validate`    |
| PATCH  | `/api/tasks/:id/close`    | Zamyka zadanie kopiując `summary` z innego zadania            | `auth`, `validateCloseTaskFromOther`, `validate` |

> 🧠 Trasy z `/ai-...` wykorzystują `aiSummaryService.js` i `gptService.js` do obsługi GPT (ocena, poprawa, wygładzanie).
> 📦 Zadania zapisywane są w kolekcji `Task`, a embeddingi w `embeddingService.js`.

---

## ⚙️ Trasy: `systemRoutes.js`

| Metoda | Ścieżka                  | Opis                                         | Middleware |
| ------ | ------------------------ | -------------------------------------------- | ---------- |
| POST   | `/api/system/openai-key` | Zapisuje zaszyfrowany klucz OpenAI (AES-256) | `auth`     |

> 🔐 Wartość klucza szyfrowana jest po stronie backendu (`AES-256-GCM`) i zapisywana w bazie w modelu `ApiKey`.
> ✅ Jeśli rekord istnieje — jest aktualizowany. W przeciwnym razie tworzony od nowa.

---

## 🧩 Uwagi i notatki techniczne

- Wszystkie trasy PATCH zwracają pełen obiekt `task` po aktualizacji.
- Obsługa błędów (np. brak podsumowania, zły `taskId`, złe dane wejściowe) odbywa się poprzez `sendError(...)` z `responseHandler.js`.
- Wszystkie trasy korzystają z wrappera `handleTryCatch(...)` w `routes/*.js` — dzięki temu `try/catch` znajduje się wyłącznie w jednym miejscu.
- Walidacja danych wejściowych realizowana jest przez `express-validator` i middleware `validate.js`.
- Middleware `auth.js` oprócz `id` przypisuje także `email` i `role` do `req.user`.
- Wszystkie dane wejściowe są przycinane (`.trim()`), a ewentualne błędy walidacji zwracają kod `400` z komunikatem zbiorczym.

---

## ✅ Do rozważenia (kolejne wersje)

- Dodanie paginacji i filtrowania do `GET /api/tasks`
- Endpoint `GET /api/system/openai-key` do odczytu aktualnego klucza
- Endpoint `DELETE /api/tasks/:id` (brak w obecnej wersji)
- Endpoint `POST /api/tasks/:id/similar` do generowania podobnych zadań

---
