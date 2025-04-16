# ✅ Dokumentacja walidatorów – AI Task App (zaktualizowana)

Pliki walidatorów znajdują się w katalogu `validators/` i wykorzystują bibliotekę `express-validator`, zapewniając walidację danych wejściowych przed dotarciem do logiki kontrolerów. Dzięki ich zastosowaniu możliwe jest wczesne odrzucanie niepoprawnych żądań, poprawa bezpieczeństwa API oraz zapewnienie spójności struktury danych.

---

## ✏️ validateTaskInput

**Do czego służy:**  
Tworzenie nowego zadania manualnie (`POST /api/tasks`).

**Walidacja:**

- `description` – **wymagane**, min. 5 znaków
- `title` – opcjonalne, 3–100 znaków
- `status` – opcjonalne, tylko `open` lub `closed`
- `dueDate` – opcjonalne, poprawna data ISO 8601
- `difficulty` – opcjonalne, liczba całkowita 1–5

---

## ✏️ validateUpdateTaskInput

**Do czego służy:**  
Aktualizacja istniejącego zadania (`PATCH /api/tasks/:id`).

**Walidacja:**

- Wszystkie pola opcjonalne, ale jeśli występują:
  - `description` – min. 5 znaków
  - `title` – 3–100 znaków
  - `status` – `open` lub `closed`
  - `dueDate` – poprawna data
  - `difficulty` – liczba całkowita od 1 do 5

---

## ✏️ validateCreateTaskWithAI

**Do czego służy:**  
Tworzenie zadania z pomocą AI (`POST /api/tasks/ai-create`).

**Walidacja:**

- `description` – **wymagane**, min. 5 znaków

---

## ✏️ validateCloseTaskWithAI

**Do czego służy:**  
Zamykanie zadania z pomocą AI (`PATCH /api/tasks/:id/ai-close`).

**Walidacja:**

- `summary` – **opcjonalne**, ale jeśli obecne, min. 40 znaków (chyba że `force = true`)
- `force` – opcjonalne, boolean
- `sourceTaskId` – opcjonalne, musi być poprawnym identyfikatorem MongoDB

---

## ✏️ validateCloseTaskFromOther

**Do czego służy:**  
Zamykanie zadania kopiując podsumowanie z innego (`PATCH /api/tasks/:id/close`).

**Walidacja:**

- `sourceTaskId` – **wymagane**, poprawny MongoID

---

## ✏️ validateRegisterInput

**Do czego służy:**  
Walidacja danych rejestracyjnych użytkownika (`POST /api/auth/register`).

**Walidacja:**

- `email` – **wymagane**, poprawny adres e-mail
- `password` – **wymagane**, min. 6 znaków

---

## ✏️ validateLoginInput

**Do czego służy:**  
Walidacja danych logowania (`POST /api/auth/login`).

**Walidacja:**

- `email` – **wymagane**, poprawny adres e-mail
- `password` – **wymagane**, min. 6 znaków

---

## 🧩 Obsługa błędów

Wszystkie walidatory współpracują z uniwersalnym middleware `validate.js`, który:

- Zbiera błędy przez `validationResult(...)`
- Mapuje błędy na pojedynczy komunikat
- Zwraca odpowiedź HTTP `400` z kodem `VALIDATION_ERROR`

**Przykład odpowiedzi błędnej:**

```json
{
  "status": "error",
  "message": "Email is required; Password must be at least 6 characters long",
  "code": "VALIDATION_ERROR"
}
```

> Każdy komunikat walidacyjny może być ustandaryzowany – zalecane jest dalsze rozszerzenie na `errors[]` z osobnymi polami.

---

## 🔁 Przykład użycia w trasie

```js
router.post("/auth/register", validateRegisterInput, validate, register);

router.patch(
  "/tasks/:id/ai-close",
  auth,
  validateCloseTaskWithAI,
  validate,
  handleTryCatch(closeTaskWithAI)
);
```

---

## 📦 Uwagi końcowe

- Walidacja odbywa się zawsze **przed** wykonaniem kontrolera
- Walidacja działa niezależnie od źródła żądania (frontend / Postman / AI)
- Pola generowane automatycznie (`embedding`, `similarTasks`, `summary`, `closedAt`) **nie są poddawane walidacji**
- Możliwe dalsze rozszerzenia:
  - `validateSystemInput` (np. konfiguracja OpenAI)
  - `validateRole()` – przyszła obsługa uprawnień

---

## 📄 Dokumentacja powiązana

- `api_spec.md` – definicje danych wejściowych i odpowiedzi
- `controllers.md` – logika wykonawcza tras
- `middleware.md` – obsługa `validate.js` i `auth.js`
- `routes.md` – przypisanie walidatorów do tras
