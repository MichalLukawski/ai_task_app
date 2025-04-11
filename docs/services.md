# 🧠 Dokumentacja – services/gptService.function.js

## 📁 Plik: `services/gptService.function.js`

Plik zawiera funkcję odpowiedzialną za generowanie struktury zadania z wykorzystaniem mechanizmu **function calling** w modelu `gpt-4o` (OpenAI API).

---

## 🔧 Opis funkcji

### `getTaskStructureFromAI(description)`

- **Typ:** `async function`
- **Parametry:**
  - `description` _(string)_ – opis zadania podany przez użytkownika
- **Zwraca:** `Promise<object>` – struktura zadania `{ title, description, dueDate?, difficulty? }`
- **Model:** `gpt-4o`
- **Mechanizm:** `function_call` z `tools` (OpenAI)

---

## ⚙️ Jak działa

1. Tworzy wiadomości `system` i `user` z aktualną datą i opisem zadania
2. Określa `tools.function` (nazwa: `create_task`) z polami:
   - `title` (string, required)
   - `description` (string, required)
   - `dueDate` (string, opcjonalnie, format ISO)
   - `notes` (opcjonalnie, jeśli GPT uzna to za potrzebne)
   - `difficulty` (opcjonalnie, skala 1–5)
3. Wywołuje model `gpt-4o` z parametrem `tool_choice`
4. Parsuje `tool_calls[0].function.arguments` jako JSON

---

## ✅ Uwagi

- Brak fallbacku tekstowego – wymuszony jest poprawny JSON przez OpenAI function calling
- `notes` może być zwrócone, ale nie jest już zapisywane domyślnie
- `difficulty` jest opcjonalne, ale AI powinno je uzupełnić w większości przypadków
- Funkcja nie zapisuje do Mongo – tylko zwraca strukturę do kontrolera

---

## 📥 Przykład użycia w kontrolerze

```js
const { getTaskStructureFromAI } = require("../services/gptService.function");

exports.createWithAI = async (req, res) => {
  const structure = await getTaskStructureFromAI(req.body.description);
  const task = new Task({ ...structure, ownerId: req.user.id });
  await task.save();
};
```

---

## 🔐 Bezpieczeństwo i jakość danych

- Aktualna data pomaga w kontekście "do 15 maja"
- Zwięzły prompt systemowy oszczędza tokeny
- Function calling zapewnia pełną kontrolę nad strukturą danych
