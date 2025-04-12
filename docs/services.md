# 🧠 Dokumentacja – services/gptService.function.js

## 📁 Plik: `services/gptService.function.js`

Plik zawiera funkcje odpowiedzialne za obsługę GPT-4o z użyciem **function calling**: tworzenie zadań, ocenę i wygładzanie podsumowań.

---

## 🔧 Opis funkcji

### `getTaskStructureFromAI(description)`

- **Typ:** `async function`
- **Parametry:** `description` _(string)_ – opis zadania od użytkownika
- **Zwraca:** `Promise<object>` – `{ title, description, dueDate?, difficulty? }`
- **Mechanizm:** function calling (`create_task`)

---

### `getSummaryAssessment(taskDescription, userSummary)`

- **Typ:** `async function`
- **Parametry:**
  - `taskDescription` _(string)_ – oryginalny opis zadania
  - `userSummary` _(string)_ – wpisane przez użytkownika podsumowanie
- **Zwraca:** `"error"` lub poprawiony tekst (string)
- **Mechanizm:** function calling (`assess_summary`)
- **Cel:** Ocena, czy `summary` jest wystarczająco dobre do użycia

---

### `improveSummary(userSummary)`

- **Typ:** `async function`
- **Parametry:** `userSummary` _(string)_ – opis użytkownika
- **Zwraca:** `Promise<string>` – wygładzona wersja
- **Mechanizm:** function calling (`improve_summary`)
- **Cel:** Poprawa stylu i języka opisu, bez zmiany sensu

---

## ⚙️ Jak działa

Wszystkie funkcje korzystają z `tools` oraz `tool_choice`, zgodnie z zasadami OpenAI Function Calling.

- `getTaskStructureFromAI`: tool `create_task`
- `getSummaryAssessment`: tool `assess_summary`
- `improveSummary`: tool `improve_summary`

Każda z funkcji odbiera dane w postaci JSON, parsuje `tool_calls[0].function.arguments` i zwraca wynik strukturalnie.

---

## ✅ Uwagi

- Wszystkie odpowiedzi GPT są **parsowane jako JSON** – brak tekstu luzem
- AI nigdy nie tworzy podsumowań samodzielnie – tylko ocenia lub wygładza
- Jeśli AI zwraca `"error"`, użytkownik może wymusić użycie tekstu (`force: true`)

---

## 📥 Przykład użycia w kontrolerze

```js
const {
  getSummaryAssessment,
  improveSummary,
} = require("../services/gptService.function");
const { processTaskClosure } = require("../services/aiSummaryService");

exports.aiCloseTask = async (req, res) => {
  const summary = await processTaskClosure({
    task,
    userSummary: req.body.summary,
    sourceTaskId: req.body.sourceTaskId,
    force: req.body.force,
  });
  task.summary = summary;
  task.status = "closed";
  task.closedAt = new Date();
  await task.save();
};
```

---

## 📄 Powiązania

- `taskController.js`
- `aiSummaryService.js`
- `embeddingService.js`
