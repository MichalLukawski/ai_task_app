# 🧠 Dokumentacja – AI Services

Dokument opisuje najważniejsze moduły znajdujące się w katalogu `services/`, które odpowiadają za integrację AI z backendem aplikacji AI Task App.

---

## 📁 `services/gptService.function.js`

Plik zawiera funkcje odpowiedzialne za komunikację z OpenAI GPT-4o z użyciem **function calling**.

### 🔧 Funkcje:

#### `getTaskStructureFromAI(description)`

- **Zadanie:** generuje strukturę nowego zadania na podstawie opisu użytkownika
- **Model:** `gpt-4o`
- **Funkcja GPT:** `create_task`
- **Zwraca:**
  - `title` – krótki tytuł
  - `description` – zoptymalizowany opis
  - `dueDate?` – tylko jeśli wykryty w opisie
  - `difficulty` – liczba od 1 do 5

#### `getSummaryAssessment(taskDescription, userSummary)`

- **Zadanie:** ocenia jakość podsumowania wpisanego przez użytkownika
- **Model:** `gpt-4o`
- **Funkcja GPT:** `assess_summary`
- **Zwraca:** `"error"` jeśli podsumowanie jest za słabe, lub wygładzony tekst

#### `improveSummary(userSummary)`

- **Zadanie:** wygładza zaakceptowane podsumowanie (językowo/stylistycznie)
- **Model:** `gpt-4o`
- **Funkcja GPT:** `improve_summary`
- **Zwraca:** poprawiony tekst

### ⚙️ Szczegóły techniczne

- Wymuszony `tool_choice` → zawsze JSON w `tool_calls[].function.arguments`
- Stateless – brak historii
- Odpowiedzi zawsze w języku użytkownika
- Temperatura `0.2–0.3` w zależności od funkcji

---

## 📁 `services/embeddingService.js`

Obsługuje generowanie i porównywanie embeddingów OpenAI.

### 🔧 Funkcje:

#### `generateEmbedding(text)`

- **Model:** `text-embedding-3-small`
- **Zwraca:** tablicę liczb – embedding dla tekstu `title + description`

#### `findSimilarTasks(newEmbedding)`

- **Zadanie:** znajduje zakończone zadania podobne semantycznie
- **Algorytm:** cosine similarity
- **Próg podobieństwa:** `≥ 0.75`
- **Zwraca:** max 5 najbardziej podobnych zadań (`taskId`, `similarity`)

#### `generateAndAttachEmbedding(taskId)`

- Generuje embedding dla zadania
- Znajduje podobne zakończone zadania
- Zapisuje embedding i `similarTasks[]` do dokumentu zadania

---

## 📁 `services/aiSummaryService.js`

Logika oceny i przetwarzania `summary` przy zamykaniu zadania.

### 🔧 Główna funkcja:

#### `processTaskClosure({ task, userSummary, force })`

- **Zadanie:** sprawdza poprawność `summary`, wygładza jeśli poprawne lub `force`
- **Zwraca:** finalne `summary`, gotowe do zapisu
- Obsługuje minimalną długość (40 znaków)
- Używa `getSummaryAssessment` i `improveSummary`

---

## 📁 `services/openaiKeyManager.js`

Obsługuje bezpieczne zarządzanie kluczem API do OpenAI.

### 🔐 Mechanizm:

- Klucz może być przechowywany zaszyfrowany w MongoDB (`ApiKey`)
- Algorytm szyfrowania: `AES-256-GCM`
- `SECRET_ENCRYPTION_KEY` trzymany w `.env` (64 znaki HEX)
- Możliwość fallbacku do `OPENAI_API_KEY` z `.env`

### 🔧 Funkcje:

#### `getOpenAIKey(scope = 'global')`

- Pobiera klucz z bazy lub z `.env`
- Cache’uje `scope = 'global'` w pamięci

#### `setOpenAIKey({ apiKeyPlaintext, scope })`

- Szyfruje i zapisuje klucz do bazy (`ApiKey`)
- Obsługuje rotację (`rotatedAt`)

#### `encryptKey(...)`, `decryptKey(...)`

- Wewnętrzne funkcje szyfrujące / deszyfrujące z użyciem `crypto` (Node.js)

---

## 📄 Powiązania z kontrolerami

| Plik                              | Funkcje AI                                          |
| --------------------------------- | --------------------------------------------------- |
| `controllers/taskController.js`   | `createWithAI`, `closeWithAI`, `getSimilarTasks`    |
| `controllers/systemController.js` | `POST /api/system/openai-key` (dodanie klucza)      |
| `middleware/auth.js`              | wymagane dla działań AI powiązanych z użytkownikiem |

---

## 📦 Obsługa modeli AI

| Model                    | Użycie                                                |
| ------------------------ | ----------------------------------------------------- |
| `gpt-4o`                 | function calling, stateless (create, assess, improve) |
| `text-embedding-3-small` | generowanie wektorów semantycznych                    |

---

## 🧪 Testowanie i kontrola

- Wszystkie błędy związane z OpenAI są obsługiwane w `try/catch`
- Odpowiedzi AI są zawsze parsowane z `tool_calls[0].function.arguments`
- W `gptService` nie ma fallbacku do `content` – błędy są rzucane jawnie
- AI nigdy nie działa samodzielnie – decyzję o `summary` podejmuje użytkownik

---

## 📄 Dokumentacja powiązana

- `ai_integration.md` – ogólny przegląd integracji z AI
- `controllers.md` – endpointy `/ai-create`, `/ai-close`, `/openai-key`
- `db_schema.md` – struktura `Task`, `ApiKey`
- `project_overview.md`, `backend_overview.md` – kontekst działania AI
