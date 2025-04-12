# ✅ Dokumentacja walidatorów – AI Task App

## 📄 Plik: `validators/taskValidator.js`

### Opis:

Walidatory dla tworzenia i edycji zadań oraz zamykania zadania (AI). Oparte o `express-validator`.

---

### `validateTaskInput`

- **description** – wymagane, min. 5 znaków
- **title** – opcjonalne, 3–100 znaków
- **status** – opcjonalne, `"open"` lub `"closed"`
- **dueDate** – opcjonalne, ale musi być w formacie ISO (`YYYY-MM-DD` lub pełna data/czas)

```js
exports.validateTaskInput = [
  body("description")
    .trim()
    .notEmpty()
    .withMessage("Description is required")
    .isLength({ min: 5 })
    .withMessage("Description must be at least 5 characters long"),

  body("title")
    .optional()
    .isLength({ min: 3, max: 100 })
    .withMessage("Title must be between 3 and 100 characters"),

  body("status")
    .optional()
    .isIn(["open", "closed"])
    .withMessage("Status must be either 'open' or 'closed'"),

  body("dueDate")
    .optional()
    .isISO8601()
    .withMessage("Due date must be a valid ISO 8601 date (e.g., YYYY-MM-DD)"),
];
```

---

### `validateCloseTaskInput`

Walidator dla `POST /api/tasks/:id/ai-close`

- **summary** – opcjonalne, string
- **force** – opcjonalne, boolean
- **sourceTaskId** – opcjonalne, `ObjectId`

```js
exports.validateCloseTaskInput = [
  body("summary").optional().isString().withMessage("Summary must be a string"),

  body("force")
    .optional()
    .isBoolean()
    .withMessage("Force must be a boolean value"),

  body("sourceTaskId")
    .optional()
    .isMongoId()
    .withMessage("sourceTaskId must be a valid MongoDB ObjectId"),
];
```

---

### Powiązania:

- `validateTaskInput`: trasy `POST /api/tasks`, `PUT /api/tasks/:id`
- `validateCloseTaskInput`: trasa `POST /api/tasks/:id/ai-close`
- Współpraca z middleware `validate.js`
- Zgodność z modelem `Task.js`
