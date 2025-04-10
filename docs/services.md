# 🧠 Dokumentacja – services/gptService.js

## 📁 Plik: `services/gptService.js`

Plik ten zawiera funkcje odpowiedzialne za komunikację z OpenAI API i generowanie struktury zadania w formacie JSON, z wykorzystaniem modelu GPT-4o.

---

## 🔧 Opis funkcji

### `getTaskStructureFromAI(description)`

- **Typ:** `async function`
- **Parametry:**
  - `description` *(string)* – opis zadania podany przez użytkownika
- **Zwraca:** `Promise<object>` – struktura zadania: `{ title, description, dueDate?, notes? }`

---

## ⚙️ Jak działa

1. Generuje prompt systemowy z aktualną datą
2. Wysyła zapytanie do modelu `gpt-4o` (OpenAI)
3. Oczekuje odpowiedzi w formacie JSON
4. Czyści markdown (```json)
5. Próbuje sparsować JSON

---

## 🛡️ Fallback

- Jeśli `JSON.parse()` nie powiedzie się:
  - Treść odpowiedzi zostaje zapisana jako `notes`
  - Tworzony jest obiekt zadania z pustym `title`, oryginalnym `description` i notatką
  - Surowa odpowiedź GPT jest zapisywana do pliku `logs/gpt_fallbacks.log`

---

## 📥 Przykład użycia w kontrolerze

```js
const { getTaskStructureFromAI } = require("../services/gptService");

exports.createWithAI = async (req, res) => {
  const { description } = req.body;
  const taskData = await getTaskStructureFromAI(description);
  // tworzenie zadania w MongoDB
};
```

---

## 🧪 Logowanie błędów

- Funkcja pomocnicza: `logGPTFallback(raw, userDescription)`
- Zapisuje datę, opis użytkownika i nieparsowalną odpowiedź GPT
- Plik logu: `logs/gpt_fallbacks.log`

---

## 🔐 Bezpieczeństwo

- Bieżąca data w promptcie pomaga GPT rozpoznawać daty typu „do 15 maja”
- Brak przesyłania klucza OpenAI do frontend

---

## 🧩 Planowane rozszerzenia

- Dodanie `difficulty` (ocena trudności przez GPT)
- Obsługa promptów dla podsumowania zamknięcia (`/api/tasks/:id/close`)
- Funkcja pomocnicza `getSimilarTasksByEmbedding()` (osobny moduł)
