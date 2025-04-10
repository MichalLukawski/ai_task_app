# 🧠 Dokumentacja – services/gptService.js

## 📁 Plik: `services/gptService.js`

Plik ten zawiera funkcje odpowiedzialne za komunikację z OpenAI API i generowanie treści zadania (notes, title, itp.) na podstawie opisu użytkownika.

---

## 🔧 Opis funkcji

### `getTaskStructureFromAI(description)`

- **Typ:** `async function`
- **Parametry:**
  - `description` *(string)* – opis zadania podany przez użytkownika
- **Zwraca:** `Promise<string>` – wygenerowana notatka zadania (markdown)

---

## 📦 Wykorzystane technologie

- SDK: `openai@4.x`
- Model: `gpt-4o` (domyślnie, fallback: `gpt-3.5-turbo` – opcjonalnie)
- API: `chat.completions.create(...)`
- Konfiguracja z pliku `.env`:
  ```env
  OPENAI_API_KEY=sk-...
  ```

---

## 🛠 Przykład użycia w kontrolerze

```js
const { getTaskStructureFromAI } = require("../services/gptService");

exports.createWithAI = async (req, res) => {
  const { description } = req.body;
  const aiNotes = await getTaskStructureFromAI(description);
  const task = new Task({ description, notes: aiNotes, ... });
  await task.save();
};
```

---

## 🔐 Bezpieczeństwo i limity

- Klucz API nie jest przesyłany do frontend
- Obsługiwane błędy:
  - Brak dostępu do modelu (`404 model not found`)
  - Przekroczony limit (`429`)
  - Brak tokenów (`quota exceeded`)
- W razie błędów: `try/catch`, logowanie błędu i `sendError(...)`

---

## 📄 Powiązania

- `controllers/taskController.js` → metoda `createWithAI`
- `routes/taskRoutes.js` → endpoint `POST /api/tasks/ai-create`
- `middleware/auth.js` → autoryzacja JWT
