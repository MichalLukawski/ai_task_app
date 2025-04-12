# 🧠 Dokumentacja – services/gptService.function.js

## 📁 Plik: `services/gptService.function.js`

Plik zawiera funkcje odpowiedzialne za obsługę GPT-4o z użyciem **function calling**: tworzenie zadań, ocenę oraz wygładzanie podsumowań przy zamykaniu zadania.

Integracja oparta jest na modelu `gpt-4o` z wykorzystaniem `tools` (funkcji nazwanych) i `tool_choice`, co zapewnia pełną kontrolę nad strukturą odpowiedzi oraz przewidywalność działania. Wszystkie funkcje operują w trybie bezkontekstowym (stateless), co oznacza brak zapamiętywania historii rozmowy między żądaniami.

---

## 🔧 Opis funkcji

### `getTaskStructureFromAI(description)`

- **Typ:** `async function`
- **Parametry:** `description` _(string)_ – opis zadania od użytkownika
- **Zwraca:** `Promise<object>` – `{ title, description, dueDate?, difficulty }`
- **Mechanizm:** `function calling` → `create_task`
- **Opis:** GPT analizuje opis użytkownika i zwraca strukturę zadania zoptymalizowaną pod embeddingi. Dane są przetwarzane przez OpenAI i parsowane z `tool_calls`.

---

### `getSummaryAssessment(taskDescription, userSummary)`

- **Typ:** `async function`
- **Parametry:**
  - `taskDescription` _(string)_ – opis zadania
  - `userSummary` _(string)_ – wpisane przez użytkownika podsumowanie
- **Zwraca:** `"error"` lub poprawiony opis (string)
- **Mechanizm:** `function calling` → `assess_summary`
- **Opis:** GPT ocenia, czy `summary` spełnia wymagania jakościowe. Jeśli nie – zwraca `"error"`, co jest obsługiwane logicznie w backendzie (`aiSummaryService`).

---

### `improveSummary(userSummary)`

- **Typ:** `async function`
- **Parametry:** `userSummary` _(string)_ – tekst wpisany przez użytkownika
- **Zwraca:** `Promise<string>` – wygładzona wersja tekstu
- **Mechanizm:** `function calling` → `improve_summary`
- **Opis:** Poprawia język, styl i czytelność opisu – bez zmiany jego sensu. Wykorzystywana tylko, gdy `summary` zostało zaakceptowane lub użytkownik użył `force`.

---

## ⚙️ Szczegóły techniczne

- Wszystkie funkcje przekazują do OpenAI `messages`, `tools` i `tool_choice`
- Wymuszony `tool_choice` zapewnia, że model **zawsze zwraca JSON**
- Brak fallbacku do `content`: odpowiedzi są parsowane wyłącznie z `tool_calls[0].function.arguments`
- Zastosowany model: `"gpt-4o"` z temperaturą `0.2–0.3`
- W każdej odpowiedzi GPT musi używać języka użytkownika (`Always respond in the same language as the user's input`)

---

## ✅ Uwagi

- `summary` **nigdy nie jest generowane automatycznie**
- AI tylko ocenia (`assess_summary`) lub wygładza (`improve_summary`) istniejące teksty
- Backend kontroluje dopuszczalność `summary` – np. czy ma min. długość 40 znaków (chyba że `force`)
- Brak wsparcia dla `sourceTaskId` – kopiowanie `summary` odbywa się poza AI
- GPT działa tylko przy zamykaniu zadania z AI: `PATCH /api/tasks/:id/ai-close`

---

## 📥 Przykład użycia w kontrolerze

```js
const {
  getSummaryAssessment,
  improveSummary,
} = require("../services/gptService.function");
const { processTaskClosure } = require("../services/aiSummaryService");

exports.closeTaskWithAI = async (req, res) => {
  const task = await Task.findById(req.params.id);
  const summary = await processTaskClosure({
    task,
    userSummary: req.body.summary,
    force: req.body.force,
  });
  task.summary = summary;
  task.status = "closed";
  task.closedAt = new Date();
  await task.save();
  return sendSuccess(res, "Task closed successfully", task);
};
```

---

## 📄 Powiązania

- `taskController.js` – wywołanie endpointów `/ai-close`
- `aiSummaryService.js` – logika walidacji i decyzji `force`
- `embeddingService.js` – osobna warstwa generowania embeddingów przy tworzeniu zadania
- `routes/taskRoutes.js` – przypisanie walidatorów i kontrolerów do tras REST API
