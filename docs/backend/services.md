# 🧠 Dokumentacja – AI Services (zaktualizowana)

Dokument opisuje wszystkie moduły znajdujące się w katalogu `services/`, które odpowiadają za integrację z OpenAI, przetwarzanie embeddingów, zamykanie zadań przy wsparciu AI oraz bezpieczne zarządzanie kluczem API. Każdy z modułów ma wyraźnie wydzieloną odpowiedzialność i spełnia jedną główną funkcję domenową.

---

## 📁 `services/gptService.js`

Moduł odpowiedzialny za interakcję z modelem OpenAI GPT-4o. Zawiera niskopoziomowe funkcje wykorzystujące mechanizm **function calling**.

### 🔧 Eksportowane funkcje:

#### `getTaskStructureFromAI(description)`

- Generuje strukturę nowego zadania na podstawie opisu użytkownika
- Wykorzystuje `function_calling` z nazwą `create_task`
- Zwracane dane:
  - `title`: tytuł zadania
  - `description`: zoptymalizowany opis
  - `difficulty`: liczba 1–5
  - `dueDate` (opcjonalnie): jeśli rozpoznany z kontekstu

#### `getSummaryAssessment(taskDescription, userSummary)`

- Ocenia, czy podsumowanie użytkownika spełnia standardy jakości
- Jeśli nie – zwraca `"error"`
- Jeśli tak – zwraca oceniony tekst

#### `improveSummary(userSummary)`

- Wygładza stylistycznie zaakceptowane podsumowanie
- Przygotowuje je do trwałego zapisania w bazie

### ⚙️ Szczegóły techniczne

- Model: `gpt-4o`, temperatura 0.2–0.3
- Odpowiedzi parsowane z `tool_calls[0].function.arguments`
- Brak historii – każde zapytanie stateless
- Obsługa błędów przez wyjątki
- Nie zwraca fallbackowych `choices[].message.content`

---

## 📁 `services/aiSummaryService.js`

Moduł wysokopoziomowy odpowiedzialny za zamykanie zadań przy pomocy AI.

### 🔧 Funkcja główna:

#### `processTaskClosure({ task, userSummary, force })`

- Wywołuje `getSummaryAssessment(...)`
- W razie potrzeby – `improveSummary(...)`
- Jeśli `force = true`, zawsze akceptuje
- W przeciwnym razie – wymaga poprawnego `summary`
- Waliduje długość `summary` (min. 40 znaków)

Wynik funkcji może być bezpośrednio zapisany jako `task.summary`.

---

## 📁 `services/embeddingService.js`

Moduł generujący embeddingi z `text-embedding-3-small` i porównujący je do istniejących zadań.

### 🔧 Funkcje:

#### `generateEmbedding(text)`

- Łączy `title + description` i generuje embedding (array floatów)
- Model: `text-embedding-3-small`

#### `findSimilarTasks(newEmbedding)`

- Porównuje z zakończonymi zadaniami (`status = closed`)
- Oblicza `cosine similarity`
- Zwraca max 5 zadań powyżej progu 0.75

#### `generateAndAttachEmbedding(taskId)`

- Pobiera task, generuje embedding
- Wykonuje porównanie z `findSimilarTasks`
- Zapisuje wynik do `task.embedding` i `task.similarTasks`

---

## 📁 `services/openaiKeyManager.js`

Moduł zarządzania kluczem OpenAI – pozwala na zapisany, szyfrowany i rotowany dostęp do API.

### 🔐 Funkcje:

#### `setOpenAIKey({ apiKeyPlaintext, scope })`

- Domyślnie `scope = global`
- Szyfruje klucz AES-256-GCM (`crypto`)
- Zapisuje do MongoDB w kolekcji `ApiKey`
- Zaktualizowany `rotatedAt` na każdą zmianę

#### `getOpenAIKey(scope = 'global')`

- Jeśli klucz zaszyfrowany w bazie → deszyfruje
- Jeśli brak – fallback do `OPENAI_API_KEY` z `.env`
- Wynik cache’owany per `scope`

#### `encryptKey(...)`, `decryptKey(...)`

- Funkcje pomocnicze korzystające z `crypto.createCipheriv`, `createDecipheriv`
- Klucz AES musi być w `.env` jako `SECRET_ENCRYPTION_KEY`

---

## 🔁 Zmiany względem poprzedniej wersji

- Usunięto `gptService.function.js` – logika zintegrowana w `gptService.js`
- `aiSummaryService.js` nie korzysta bezpośrednio z modeli – tylko z `gptService`
- Dodano pełne wsparcie dla modelu `gpt-4o` z `tool_choice = required`
- Endpoint `/api/system/openai-key` wykorzystuje `setOpenAIKey` (wcześniej logika inline)
- Wprowadzono walidację długości `summary` i parametr `force`

---

## 📦 Powiązania z kontrolerami

| Kontroler             | Powiązane funkcje                                 |
| --------------------- | ------------------------------------------------- |
| `taskController.js`   | `createTaskWithAI`, `closeTaskWithAI`             |
| `systemController.js` | `setOpenAIKey()`                                  |
| `middleware/auth.js`  | wymagany JWT przed użyciem któregokolwiek serwisu |

---

## 📘 Dokumentacja powiązana

- `controllers.md` – definicje endpointów AI
- `routes.md` – dostępność tras
- `api_spec.md` – definicje danych wejściowych i wyjściowych
- `middleware.md` – walidacja i auth
- `utils/responseHandler.js` – obsługa błędów z poziomu `service` przez `try/catch`
