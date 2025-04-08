# AI Task App – Integracja z GPT (OpenAI)

## 🎯 Cel integracji

Sztuczna inteligencja (GPT-4) ma wspierać użytkownika w zarządzaniu zadaniami i rozwiązywaniu problemów.  
GPT pełni funkcję asystenta, który rozumie intencję użytkownika i pomaga w tworzeniu, analizie i zamykaniu zadań.

---

## 🔐 Uwierzytelnianie do OpenAI

- Użytkownik podaje własny klucz API do OpenAI
- Klucz jest przesyłany do backendu i **przechowywany zaszyfrowany** (np. AES)
- Backend komunikuje się z OpenAI, nigdy frontend

---

## 🔗 Komunikacja z API OpenAI

Typowe zapytanie:

```js
const response = await openai.createChatCompletion({
  model: "gpt-4",
  messages: [
    { role: "system", content: "Jesteś pomocnym asystentem do zarządzania zadaniami." },
    { role: "user", content: "Stwórz strukturę zadania na podstawie: 'Nie działa integracja z API uczelni'" }
  ],
  temperature: 0.7
});
```

---

## 🧠 Zastosowania GPT w aplikacji

### ✅ Wspierane funkcje:

1. **Tworzenie zadania**  
   GPT generuje: tytuł, opis, notatki, deadline (opcjonalnie)  
   → np. z 1 zdania użytkownika tworzy pełny szkielet zadania.

2. **Podsumowanie wykonania**  
   Użytkownik wpisuje „zrobione”, GPT generuje krótkie podsumowanie działania.

3. **Semantyczne wyszukiwanie**  
   GPT porównuje obecne zapytanie z historią zadań i znajduje podobne przypadki.

4. **Ocena trudności**  
   GPT ocenia na podstawie opisu, czy zadanie jest proste, średnie czy trudne.

5. **Priorytetyzacja**  
   Na podstawie opisu, terminu i historii GPT proponuje kolejność realizacji zadań.

6. **Sugestie AI**  
   - „Jakie mam teraz otwarte zadania?”
   - „Co jest najprostsze do zrobienia?”
   - „Od czego powinienem zacząć?”

---

## ⚙️ Jak to działa w backendzie

- Moduł `aiService.js` lub `gptController.js`
- Komunikacja z API OpenAI
- Obsługa błędów (`try/catch`)
- Kontekst użytkownika (prompt + historia)
- Resetowanie kontekstu po zamknięciu zadania

---

## 🔐 Bezpieczeństwo

- Klucz OpenAI zapisywany zaszyfrowany (`AES` lub `crypto`)
- Nieprzechowywanie historii rozmów GPT na zewnętrznym koncie
- Czyszczenie kontekstu po zakończeniu operacji

---

## 🔄 Rozszerzenia w przyszłości

- Obsługa modeli embeddingów (`text-embedding-ada-002`) i wektorowego wyszukiwania
- Integracja z Pinecone, Qdrant lub Weaviate
- „Tryb eksperta” – GPT uczy się na podstawie zadań użytkownika
- System punktacji lub trudności AI dla gamifikacji
- Wsparcie dla promptów systemowych definiowanych przez administratora

---

## 📄 Dokumentacja powiązana

- `backend_overview.md` – jak AI jest używane przez API
- `frontend_overview.md` – jak frontend wysyła żądania do GPT
- `project_overview.md` – pełna architektura i wizja systemu
