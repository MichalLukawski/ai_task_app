# ✅ Dokumentacja walidatorów – AI Task App

## 📄 Plik: `validators/taskValidator.js`

### Opis:
Walidatory dla tworzenia i edycji zadań. Oparte o `express-validator`.

---

### `validateTaskInput`

- **description** – wymagane, min. 5 znaków
- **title** – opcjonalne, 3–100 znaków
- **status** – opcjonalne, `"open"` lub `"closed"`
- **dueDate** – opcjonalne, ale musi być w formacie ISO (`YYYY-MM-DD` lub pełna data/czas)

### Kod:
```js
exports.validateTaskInput = [
  body("description")
    .trim()
    .notEmpty().withMessage("Description is required")
    .isLength({ min: 5 }).withMessage("Description must be at least 5 characters long"),

  body("title")
    .optional()
    .isLength({ min: 3, max: 100 }).withMessage("Title must be between 3 and 100 characters"),

  body("status")
    .optional()
    .isIn(["open", "closed"]).withMessage("Status must be either 'open' or 'closed'"),

  body("dueDate")
    .optional()
    .isISO8601().withMessage("Due date must be a valid ISO 8601 date (e.g., YYYY-MM-DD)")
];
```

---

### Powiązania:

- W trasach `POST` i `PUT /api/tasks/:id`
- Razem z middleware `validate.js`
- Zgodność z modelem `Task.js`
