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

System korzysta z mechanizmu `function_calling` w GPT-4o do:

- generowania struktury nowego zadania (`create_task`),
- oceny jakości podsumowania (`assess_summary`),
- wygładzenia podsumowania na życzenie użytkownika (`improve_summary`).

---

## 🧠 Zastosowania GPT w aplikacji

### ✅ Wspierane funkcje:

1. **Tworzenie zadania**

   - Generowane pola: `title`, `description`, `dueDate?`, `difficulty?`
   - Funkcja: `getTaskStructureFromAI(description)`

2. **Ocena trudności (`difficulty`)**

   - GPT ocenia trudność na podstawie opisu użytkownika (skala 1–5)

3. **Zamykanie zadania (AI jako wsparcie)**

   - AI ocenia jakość podsumowania użytkownika (`getSummaryAssessment`)
   - Jeśli opis jest zbyt słaby – użytkownik może go świadomie wymusić
   - W takim przypadku AI tylko wygładza tekst (`improveSummary`)
   - Jeśli użytkownik nie poda `summary`, może wskazać `sourceTaskId` – kopiujemy opis z innego zadania (bez udziału AI)

4. **Semantyczne porównywanie zadań**

   - Wykorzystanie modelu `text-embedding-3-small`
   - Porównywanie z embeddingami zadań `status: closed`
   - Top 5 z `similarity >= 0.75` przypisywane do `similarTasks`

5. **Tworzenie zadań przez AI**
   - Endpoint: `POST /api/tasks/ai-create`
   - Po zapisaniu: automatyczna analiza embedding i przypisanie `similarTasks`

---

## ⚙️ Obsługa backendowa

- `gptService.function.js`:

  - `getTaskStructureFromAI(description)` – function calling `create_task`
  - `getSummaryAssessment(description, userInput)` – function calling `assess_summary`
  - `improveSummary(userInput)` – function calling `improve_summary`

- `aiSummaryService.js`

  - Obsługuje wszystkie ścieżki logiczne dla zamykania zadania:
    - własny opis,
    - wymuszenie krótkiego opisu,
    - kopiowanie `summary` z innego zadania,
    - brak danych → błąd

- `services/embeddingService.js`
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
- Sugestie AI (otwarte zadania, najłatwiejsze, najpilniejsze)
- Eksperckie profile AI (tryb techniczny, menedżerski, itd.)
- Endpoint `POST /api/ai/similar-tasks` – wyszukiwanie podobnych przypadków

---

## 📄 Dokumentacja powiązana

- `project_roadmap.md`
- `services.md`
- `utils.md`
- `validators.md`
- `backend_overview.md`
