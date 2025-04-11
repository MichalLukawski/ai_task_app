# AI Task App – Integracja z GPT (OpenAI)

## 🎯 Cel integracji

Sztuczna inteligencja (GPT-4o) wspiera użytkownika w zarządzaniu zadaniami i rozwiązywaniu problemów.
GPT pełni rolę inteligentnego asystenta, który rozumie intencję użytkownika i pomaga w tworzeniu, analizie i domykaniu zadań.

---

## 🔐 Uwierzytelnianie do OpenAI

- Użytkownik podaje własny klucz API do OpenAI
- Klucz przesyłany jest tylko do backendu (planowane: szyfrowanie AES)
- Frontend nie ma dostępu do klucza

---

## 🔗 Komunikacja z API OpenAI – Function Calling

System korzysta z mechanizmu `function_calling` w GPT-4o do wygenerowania pełnej struktury zadania.

Przykład zapytania:

```js
const response = await openai.chat.completions.create({
  model: "gpt-4o",
  messages: [...],
  tools: [{ type: "function", function: { name: "create_task", parameters: {...} } }],
  tool_choice: { type: "function", function: { name: "create_task" } }
});
```

---

## 🧠 Zastosowania GPT w aplikacji

### ✅ Wspierane funkcje:

1. **Tworzenie zadania**

   - Generowane pola: `title`, `description`, `dueDate` (opcjonalnie), `difficulty` (opcjonalnie)
   - Funkcja `getTaskStructureFromAI(description)` zwraca strukturę danych z GPT

2. **Ocena trudności (`difficulty`)**

   - GPT ocenia trudność na podstawie opisu użytkownika (skala 1–5)

3. **Podsumowanie wykonania (planowane)**

   - GPT wygeneruje `summary` przy zamykaniu zadania (`/tasks/:id/close`)

4. **Semantyczne porównywanie zadań**

   - Wykorzystanie modelu `text-embedding-3-small`
   - Porównywanie z embeddingami zadań `status: closed`
   - Top 5 z `similarity >= 0.75` przypisywane do `similarTasks`

5. **Tworzenie tasków tylko z pomocą GPT – opcja `ai-create`**
   - Zadanie trafia do Mongo
   - Następnie backend generuje `embedding` i przypisuje `similarTasks` (opcja C – hybrydowa)

---

## ⚙️ Obsługa backendowa

- Plik: `services/gptService.function.js`
  - Wykorzystuje `function calling`, bez fallbacków
  - Wymusza strukturę danych zgodną ze schematem
- Plik: `services/embeddingService.js`
  - Generuje embeddingi
  - Porównuje z zadaniami z bazy
  - Aktualizuje `embedding`, `similarTasks` nowego zadania

---

## 🔐 Bezpieczeństwo

- Klucz OpenAI nie trafia do frontendu
- Planowane: szyfrowanie klucza użytkownika (AES)
- Resetowanie kontekstu po zakończeniu zadania

---

## 📌 Planowane rozszerzenia

- Uczenie się na podstawie zadań podobnych (zatwierdzanych ręcznie)
- Zamknięcie zadania z pomocą AI na podstawie `similarTasks`
- Ręczne szukanie podobnych zadań (`POST /api/ai/similar-tasks`)
- Sugestie AI (otwarte zadania, najłatwiejsze, najpilniejsze)
- Eksperckie profile AI (tryb techniczny, menedżerski, itd.)

---

## 📄 Dokumentacja powiązana

- `project_roadmap.md`
- `services.md`
- `utils.md`
- `validators.md`
- `backend_overview.md`
