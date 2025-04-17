# 🤖 AI Task App – Integracja z GPT (OpenAI)

## 🎯 Cel integracji

Sztuczna inteligencja (GPT-4o) wspiera użytkownika w zarządzaniu zadaniami i rozwiązywaniu problemów technicznych. GPT pełni rolę inteligentnego asystenta, który:

- tworzy strukturę zadania na podstawie opisu użytkownika,
- ocenia jakość podsumowania przy zamykaniu zadania,
- wygładza język zaakceptowanego rozwiązania.

Dzięki zastosowaniu mechanizmu **function calling**, model GPT działa w sposób przewidywalny i stateless (bez kontekstu historii), a jego odpowiedzi zawsze mają strukturę JSON.

---

## 🔐 Uwierzytelnianie do OpenAI

### 🔑 Warianty przechowywania klucza API:

1. **Zaszyfrowany w MongoDB**

   - Wysyłany raz na backend (np. przez panel administratora)
   - Szyfrowany algorytmem **AES-256-GCM**
   - Przechowywany z metadanymi: `iv`, `tag`, `rotatedAt`

2. **Fallback: plik `.env` (OPENAI_API_KEY)**
   - Używany tylko, gdy nie znaleziono klucza w bazie
   - Nie wymaga systemu szyfrowania

### 🔐 Bezpieczeństwo:

- Klucz OpenAI nigdy nie trafia do frontendu.
- Obsługiwany przez `services/openaiKeyManager.js`
- Możliwość rotacji klucza (`rotatedAt`)
- Użytkownik może podać własny klucz, jeśli system zostanie rozbudowany o profile

---

## 🔗 Komunikacja z API OpenAI – Function Calling

System wykorzystuje wyłącznie **`function_calling`** modelu `gpt-4o`.  
Odpowiedzi GPT **zawsze mają strukturę JSON**, parsowaną z `tool_calls[].function.arguments`.

### Obsługiwane funkcje:

| Nazwa funkcji     | Opis                                         |
| ----------------- | -------------------------------------------- |
| `create_task`     | Generowanie struktury zadania z opisu        |
| `assess_summary`  | Ocena jakości technicznej podsumowania       |
| `improve_summary` | Wygładzenie języka zaakceptowanego `summary` |

Model zawsze odpowiada w języku użytkownika (`Always respond in the same language as the user's input`).

---

## 🧠 Zastosowania GPT w aplikacji

### 1. Tworzenie zadania (AI)

- Endpoint: `POST /api/tasks/ai-create`
- Funkcja: `getTaskStructureFromAI(description)`
- GPT generuje:
  - `title` – krótki tytuł
  - `description` – zoptymalizowany opis techniczny
  - `difficulty` – liczba 1–5
  - `dueDate` – tylko jeśli występuje w opisie

Po zapisaniu zadania:

- Generowany jest embedding
- Zadanie zostaje porównane z zakończonymi i przypisywane są `similarTasks`

> 🔧 W wersji poprawionej logiki `getTaskStructureFromAI()` wynikowe argumenty `tool_calls[0].function.arguments` muszą być sparsowane za pomocą `JSON.parse(...)`, ponieważ API zwraca je jako ciąg tekstowy. Dodano też walidację (np. brak `title` powoduje wyjątek).

---

### 2. Zamykanie zadania (AI)

- Endpoint: `PATCH /api/tasks/:id/ai-close`
- Użytkownik podaje `summary`
- Funkcja `getSummaryAssessment()` ocenia jakość:
  - Jeśli wystarczająca → AI wygładza język (`improveSummary`)
  - Jeśli za słaba → błąd
  - Można wymusić (`force: true`) → AI wygładza mimo wszystko
- Jeśli brak `summary` – błąd
- Nie obsługuje `sourceTaskId`

---

### 3. Embedding i podobne zadania

- Model: `text-embedding-3-small` (OpenAI)
- Embedding generowany z `title + description`
- Porównywany z zakończonymi zadaniami (`cosine similarity`)
- Jeśli podobieństwo ≥ `0.75`, przypisywane do `similarTasks` (max 5)

Zadania podobne:

- Nie są wykorzystywane automatycznie przez AI
- Użytkownik może z nich skorzystać przy zamykaniu zadania ręcznie

---

## ⚙️ Obsługa backendowa

### 🧠 `gptService.js`

- `getTaskStructureFromAI(description)`

  - Dodano `JSON.parse()` na `tool_calls[0].function.arguments`
  - Walidacja: `title`, `description`, `difficulty`

- `getSummaryAssessment(taskDescription, userSummary)`

  - Zwraca: `{ summary, error }`

- `improveSummary(userSummary)`
  - Zwraca: `summary` po korekcie językowej

### 🧠 `aiSummaryService.js`

- `processTaskClosure({ task, summary, force })`
  - Odrzuca podsumowania zbyt słabe
  - Obsługuje wymuszenie (`force`)
  - Przekazuje do `improveSummary()`

### 🧠 `embeddingService.js`

- `generateEmbedding(text)` – generuje wektor dla `title + description`
- `findSimilarTasks(newEmbedding)` – zwraca max 5 zadań (`similarity ≥ 0.75`)
- `generateAndAttachEmbedding(taskId)` – zapisuje embedding i przypisuje `similarTasks`

---

## 📦 Przechowywanie i szyfrowanie klucza OpenAI

- Plik: `services/openaiKeyManager.js`
- Model: `models/ApiKey.js`
- AES-256-GCM (`crypto.createCipheriv`)
- Pola w bazie:
  - `scope` (np. "global", w przyszłości `userId`)
  - `encryptedKey`, `iv`, `tag`
- W przypadku braku klucza → fallback do `OPENAI_API_KEY` z `.env`

---

## 🔐 Bezpieczeństwo

- Klucz OpenAI nigdy nie trafia na frontend
- Obsługiwany wyłącznie backendowo
- Zaszyfrowany w bazie (zależnie od ustawienia)
- Kontekst GPT resetowany po każdym zapytaniu (stateless)

---

## 📌 Planowane rozszerzenia

- Endpoint: `POST /api/ai/similar-tasks` – ręczne wyszukiwanie przez embedding
- System feedbacku: „czy podobne zadanie pomogło?”
- Sugestie AI (najpilniejsze, najłatwiejsze, otwarte)
- Tryby AI: „debugger”, „mentor”, „ekspert”
- Anonimizacja danych do testów GPT
- Obsługa wielu kluczy (`per-user scope`)

---

## 📄 Dokumentacja powiązana

- `project_overview.md`
- `backend_overview.md`
- `project_roadmap.md`
- `services.md`
- `utils.md`
- `controllers.md`
- `api_spec.md`
- `db_schema.md`
- `middleware.md`
- `api-integration.md`
