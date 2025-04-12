# ✅ Dokumentacja walidatorów – AI Task App (szczegółowa)

Plik `validators/taskValidator.js` zawiera zestaw walidatorów opartych na `express-validator`, wykorzystywanych do sprawdzania poprawności danych wejściowych w żądaniach HTTP kierowanych do backendu aplikacji. Każdy walidator dopasowany jest do konkretnego typu operacji (tworzenie, edycja, zamykanie zadania itp.), a ich rolą jest zapewnienie spójności, poprawności i bezpieczeństwa danych.

---

## ✏️ validateTaskInput

**Przeznaczenie:**  
Używany przy tworzeniu nowego zadania przez użytkownika ręcznie (`POST /api/tasks`).

**Opis działania:**  
Walidator ten wymaga obecności opisu (`description`) oraz pozwala na przesłanie kilku opcjonalnych pól. Weryfikuje długości tekstów, dozwolone wartości oraz formaty dat.

**Zasady walidacji:**

- `description` – wymagane, string, min. 5 znaków.
- `title` – opcjonalne, min. 3, max. 100 znaków.
- `status` – opcjonalne, musi być jedną z wartości: `"open"` lub `"closed"`.
- `dueDate` – opcjonalne, musi być w formacie ISO 8601.
- `difficulty` – opcjonalne, liczba całkowita z przedziału 1–5.

---

## ✏️ validateUpdateTaskInput

**Przeznaczenie:**  
Używany przy edycji istniejącego zadania (`PATCH /api/tasks/:id`).

**Opis działania:**  
Każde z pól objętych walidacją jest opcjonalne. Jeśli zostanie przesłane – musi spełniać określone warunki. Pozwala na częściową aktualizację rekordu (zgodnie z semantyką `PATCH`).

**Zasady walidacji:**

- `description` – opcjonalne, min. 5 znaków.
- `title` – opcjonalne, 3–100 znaków.
- `status` – opcjonalne, `"open"` lub `"closed"`.
- `dueDate` – opcjonalne, format ISO 8601 (np. `"2025-05-01"`).
- `difficulty` – opcjonalne, liczba całkowita z zakresu 1–5.

---

## ✏️ validateCreateTaskWithAI

**Przeznaczenie:**  
Weryfikuje dane przesyłane do endpointu tworzącego zadanie przy pomocy AI (`POST /api/tasks/ai-create`).

**Opis działania:**  
AI generuje większość danych zadania (`title`, `description`, `difficulty`, `dueDate`), więc użytkownik podaje wyłącznie `description`. Jakość tego opisu wpływa na efektywność działania GPT.

**Zasady walidacji:**

- `description` – wymagane, string, min. 5 znaków (krótsze są odrzucane).

---

## 📌 Obsługa błędów walidacji

Wszystkie powyższe walidatory są podpinane do tras jako middleware i współpracują z funkcją `validate.js`, która:

- przerywa dalsze przetwarzanie żądania, jeśli wystąpi błąd,
- zwraca status HTTP `400`,
- dostarcza precyzyjny komunikat, które pole zawiodło i dlaczego.

Przykładowa odpowiedź:

```json
{
  "status": "error",
  "message": "Validation failed",
  "errors": [
    {
      "field": "description",
      "message": "Description must be at least 5 characters long"
    }
  ]
}
```

Dzięki temu warstwa walidacji stanowi pierwszą linię obrony przed błędnymi lub niekompletnymi danymi, zanim trafią one do logiki aplikacyjnej lub do modeli baz danych.
