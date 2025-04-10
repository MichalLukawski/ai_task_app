# AI Task App – Integracja z GPT (OpenAI)

## 🎯 Cel integracji

Sztuczna inteligencja (GPT-4) ma wspierać użytkownika w zarządzaniu zadaniami i rozwiązywaniu problemów.
GPT pełni funkcję asystenta, który rozumie intencję użytkownika i pomaga w tworzeniu, analizie i zamykaniu zadań.

---

## 🔐 Uwierzytelnianie do OpenAI

- Użytkownik podaje własny klucz API do OpenAI
- Klucz jest przesyłany do backendu i (planowane) przechowywany zaszyfrowany (AES)
- Backend komunikuje się z OpenAI – frontend nie ma dostępu do klucza

---

## 🔗 Komunikacja z API OpenAI

Typowe zapytanie:

```js
const response = await openai.createChatCompletion({
  model: "gpt-4o",
  messages: [
    { role: "system", content: "Dziś jest 2025-04-10. Jesteś pomocnym asystentem do zarządzania zadaniami..." },
    { role: "user", content: "Do 20 maja mam napisać parser XML." }
  ],
  temperature: 0.3
});
```

---

## 🧠 Zastosowania GPT w aplikacji

### ✅ Wspierane funkcje:

1. **Tworzenie zadania**
   - GPT generuje: `title`, `description`, `dueDate` (jeśli występuje), `notes`
   - Odpowiedź oczekiwana jest w formacie **czystego JSON**

2. **Fallback przy błędnym JSON**
   - Jeśli GPT nie zwróci poprawnego JSON → odpowiedź zostaje zapisana jako `notes`
   - Fallback logowany do `logs/gpt_fallbacks.log` przez `logGPTFallback()`

3. **Podsumowanie wykonania (planowane)**
   - GPT generuje podsumowanie działania przy zamykaniu zadania (`/close`)

4. **Semantyczne wyszukiwanie (planowane)**
   - Embeddingi generowane lokalnie (lub przez `text-embedding-3-small`)
   - Porównanie z poprzednimi zadaniami
   - Dopiero top 3 analizowane przez GPT

5. **Ocena trudności zadania (planowane)**
   - `difficulty: 1–5` na podstawie złożoności opisu użytkownika

6. **Sugestie AI (planowane)**
   - Jakie mam teraz otwarte zadania?
   - Co jest najłatwiejsze do zrobienia?
   - Od czego zacząć?

---

## ⚙️ Obsługa backendowa

- Plik: `services/gptService.js`
- Oczyszcza odpowiedź GPT z bloków markdown (```json)
- Parsuje do obiektu JSON
- W przypadku błędu → fallback + log do `logs/gpt_fallbacks.log`
- Użycie bieżącej daty w promptcie do rozpoznawania terminów

---

## 🔐 Bezpieczeństwo

- Klucz OpenAI nie trafia do frontend
- Planowane szyfrowanie klucza użytkownika (AES lub `crypto`)
- Resetowanie kontekstu po zakończeniu zadania

---

## 📌 Planowane rozszerzenia

- Embeddingi (OpenAI lub lokalnie np. `all-MiniLM`)
- Silnik wektorowy (Qdrant / Weaviate / FAISS) do szybkiego porównywania
- Panel "podobne zadania" po utworzeniu nowego
- Punktacja zadań (`difficulty`)
- Możliwość „uczenia AI” na własnych zadaniach (tryb eksperta)

---

## 📄 Dokumentacja powiązana

- `project_roadmap.md`
- `services.md`
- `utils.md`
- `validators.md`
- `backend_overview.md`
