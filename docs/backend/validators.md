# ✅ Dokumentacja walidatorów – AI Task App

Plik `validators/taskValidator.js` zawiera zestaw walidatorów opartych na `express-validator`. Każdy walidator odpowiada konkretnej akcji (tworzenie zadania, zamykanie, aktualizacja itp.). Ich zadaniem jest zapewnienie spójności i bezpieczeństwa danych wejściowych w żądaniach HTTP.

---

## ✏️ validateTaskInput

**Do czego służy:**  
Tworzenie nowego zadania manualnie (`POST /api/tasks`).

**Walidacja:**

- `description` – wymagane, min. 5 znaków
- `title` – opcjonalne, 3–100 znaków
- `status` – opcjonalne, `open` lub `closed`
- `dueDate` – opcjonalne, musi być poprawnym ISO 8601
- `difficulty` – opcjonalne, liczba całkowita od 1 do 5

---

## ✏️ validateUpdateTaskInput

**Do czego służy:**  
Aktualizacja istniejącego zadania (`PATCH /api/tasks/:id`).

**Walidacja:**

- `description`, `title`, `status`, `dueDate`, `difficulty` – wszystkie opcjonalne, ale jeśli obecne, muszą być poprawne jak powyżej

---

## ✏️ validateCreateTaskWithAI

**Do czego służy:**  
Tworzenie zadania z pomocą AI (`POST /api/tasks/ai-create`).

**Walidacja:**

- `description` – wymagane, min. 5 znaków

---

## ✏️ validateCloseTaskWithAI

**Do czego służy:**  
Zamykanie zadania z pomocą AI (`PATCH /api/tasks/:id/ai-close`).

**Walidacja:**

- `summary` – opcjonalne, jeśli obecne musi mieć min. 40 znaków (chyba że `force` = true)
- `force` – opcjonalne, boolean
- `sourceTaskId` – opcjonalne, typ MongoID

---

## ✏️ validateCloseTaskFromOther

**Do czego służy:**  
Zamykanie zadania poprzez skopiowanie podsumowania z innego (`PATCH /api/tasks/:id/close`).

**Walidacja:**

- `sourceTaskId` – wymagane, typ MongoID

---

## 🧩 Obsługa błędów

Wszystkie walidatory współpracują z uniwersalnym middleware `validate.js`, który:

- zbiera błędy z `express-validator`
- zwraca odpowiedź `400 Bad Request` z listą pól i opisem błędu

**Przykład odpowiedzi:**

```json
{
  "status": "error",
  "message": "Validation failed",
  "errors": [
    {
      "field": "summary",
      "message": "Summary must be at least 40 characters long"
    }
  ]
}
```

---

## 🔁 Przykład użycia w trasie

```js
router.patch(
  "/tasks/:id/ai-close",
  auth,
  validateCloseTaskWithAI,
  validate,
  closeTaskWithAI
);
```

---

## 📦 Uwagi końcowe

- Pola `embedding`, `similarTasks` są generowane automatycznie – nie są walidowane przez użytkownika
- W przyszłości możliwa rozbudowa walidatorów o sprawdzanie właściciela zadania, ról, stanu sesji
- Wszystkie walidacje są niezależne od źródła danych (frontend / AI)

---

## 📄 Dokumentacja powiązana

- `api_spec.md`
- `controllers.md`
- `middleware.md`
- `services.md`
- `db_schema.md`
