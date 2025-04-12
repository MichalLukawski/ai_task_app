# AI Task App – Integracja z GPT (OpenAI)

## 🎯 Cel integracji

Sztuczna inteligencja (GPT-4o) wspiera użytkownika w zarządzaniu zadaniami i rozwiązywaniu problemów.
GPT pełni rolę inteligentnego asystenta, który rozumie intencję użytkownika i pomaga w tworzeniu, analizie i domykaniu zadań. Dzięki zastosowaniu mechanizmu function calling, AI jest w stanie strukturalnie przetwarzać dane i działać w sposób przewidywalny, w pełni zgodny z wymaganiami aplikacji.

---

## 🔐 Uwierzytelnianie do OpenAI

- Użytkownik podaje własny klucz API do OpenAI (przekazywany raz, np. z poziomu frontendu)
- Klucz przesyłany wyłącznie do backendu – frontend nie ma do niego dostępu
- Planowane: szyfrowanie klucza po stronie backendu (AES)
- Kontekst GPT jest resetowany po każdym zamknięciu zadania – model działa stateless

---

## 🔗 Komunikacja z API OpenAI – Function Calling

System wykorzystuje wyłącznie mechanizm `function_calling` GPT-4o.
Nie ma fallbacków do generowania tekstu – wszystkie odpowiedzi muszą mieć strukturę JSON.

### Obsługiwane funkcje:

- `create_task` – generowanie struktury zadania
- `assess_summary` – ocena jakości opisu rozwiązania
- `improve_summary` – wygładzenie zaakceptowanego lub wymuszonego `summary`

Wszystkie funkcje są wykonywane w tym samym języku, w jakim użytkownik przesłał input (`Always respond in the same language as the user's input`).

---

## 🧠 Zastosowania GPT w aplikacji

### 1. Tworzenie zadania (AI)

- Endpoint: `POST /api/tasks/ai-create`
- Użytkownik podaje `description`, a GPT generuje:
  - `title` (krótki, rzeczowy tytuł)
  - `description` (szczegółowy opis zoptymalizowany pod embeddingi)
  - `difficulty` (1–5)
  - `dueDate` (jeśli występuje)
- Funkcja: `getTaskStructureFromAI()`

### 2. Zamykanie zadania (AI)

- Endpoint: `PATCH /api/tasks/:id/ai-close`
- Użytkownik podaje `summary`
- AI ocenia jakość (`getSummaryAssessment`)
  - Jeśli wystarczające: wygładza i zapisuje (`improveSummary`)
  - Jeśli za krótkie lub nieprzydatne: zwraca błąd
  - Można wymusić użycie `summary`, nawet słabego (`force: true`)
- `sourceTaskId` nie jest dozwolone w tym endpointzie

### 3. Embedding i podobne zadania

- Po utworzeniu zadania (AI) backend generuje embedding (`text-embedding-3-small`)
- Embedding porównywany z zakończonymi zadaniami (`cosine similarity`)
- Przypisuje maksymalnie 5 zadań (`similarity ≥ 0.75`)
- Zadania trafiają do `similarTasks`, ale nie są używane automatycznie

---

## ⚙️ Obsługa backendowa

### gptService.function.js

- `getTaskStructureFromAI(description)`
- `getSummaryAssessment(taskDescription, userInput)`
- `improveSummary(userInput)`

### aiSummaryService.js

- `processTaskClosure({ task, summary, force })`
  - Odmowa przyjęcia zbyt słabego `summary`
  - Wymuszenie `force: true` – wtedy AI wygładza mimo ostrzeżenia
  - Brak danych → błąd

### embeddingService.js

- Tworzy embedding na podstawie `title + description`
- Porównuje z zakończonymi zadaniami
- Przypisuje `similarTasks` (max 5, threshold ≥ 0.75)

---

## 🔐 Bezpieczeństwo

- Klucz OpenAI dostępny tylko backendowi
- Planowane: szyfrowanie klucza (AES)
- Reset kontekstu po każdej operacji – brak zapamiętywania wcześniejszych promptów
- AI działa tylko przy `PATCH /ai-close` – nigdy przy `/close`

---

## 📌 Planowane rozszerzenia

- Wyszukiwanie podobnych zadań przez `POST /api/ai/similar-tasks`
- Sugestie AI: otwarte zadania, najłatwiejsze, najbardziej pilne
- Tryby eksperckie GPT: np. "debugger", "mentor", "projektant"
- Analiza jakości rozwiązań historycznych

---

## 📄 Dokumentacja powiązana

- `project_overview.md`
- `project_roadmap.md`
- `controllers.md`
- `validators.md`
- `backend_overview.md`
- `api_spec.md`
