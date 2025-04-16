# 🏗️ Struktura aplikacji backendowej – AI Task App

Niniejszy dokument przedstawia szczegółową strukturę logiczną i katalogową backendu aplikacji **AI Task App**. Zawiera opis głównych warstw systemu, ich odpowiedzialności, powiązań między komponentami oraz zasad, które obowiązują podczas ich rozbudowy.

Celem dokumentu jest zapewnienie pełnej przejrzystości architektury backendu i umożliwienie każdemu członkowi zespołu szybkie zrozumienie działania oraz logiki przepływu danych.

---

## 📁 Główna struktura katalogowa

```
backend/
├── controllers/
├── middleware/
├── models/
├── routes/
├── services/
├── utils/
├── validators/
├── config/
└── server.js
```

---

## 🔀 Warstwowy model odpowiedzialności

### 1. `routes/` (warstwa zewnętrzna)

- Odpowiada za definiowanie tras REST API (np. `POST /api/tasks`)
- Nie zawiera żadnej logiki biznesowej
- Każda trasa:
  - przypisana jest do jednej funkcji kontrolera
  - używa walidatorów i middleware (`auth`, `validate`, `handleTryCatch`)

---

### 2. `controllers/` (warstwa logiki aplikacji)

- Funkcje wywoływane przez routery
- Odpowiadają za:
  - walidację żądań (jeśli nie wykonana wcześniej)
  - wywołanie odpowiedniego serwisu
  - obsługę odpowiedzi (`sendSuccess`, `sendError`)
- Nie powinny zawierać logiki AI, bazowej ani złożonych transformacji danych

---

### 3. `services/` (warstwa domeny biznesowej)

- Implementuje kluczową logikę związaną z AI, embeddingami, dostępem do API
- Moduły takie jak:
  - `gptService.js` – połączenie z GPT-4o
  - `aiSummaryService.js` – zamykanie zadania z AI
  - `embeddingService.js` – generowanie i porównywanie embeddingów
  - `openaiKeyManager.js` – bezpieczne zarządzanie kluczami

---

### 4. `models/` (warstwa danych)

- Definiuje schematy Mongoose
- Modele: `Task`, `User`, `ApiKey`
- Schematy zawierają typy pól, walidację i indeksy

---

### 5. `middleware/`

- Funkcje pośredniczące dla autoryzacji, walidacji, obsługi błędów
- Ważne middleware:
  - `auth.js` – weryfikacja tokena JWT
  - `validate.js` – integracja z `express-validator`
  - `errorHandler.js` – obsługa błędów globalnych

---

### 6. `validators/`

- Walidatory danych wejściowych z użyciem `express-validator`
- Podzielone na: `authValidator.js`, `taskValidator.js`

---

### 7. `utils/`

- Wspólne narzędzia pomocnicze (np. `responseHandler.js`)
- Obsługa:
  - formatowania odpowiedzi
  - przechwytywania błędów `async` (`handleTryCatch`)

---

## 🔁 Przepływ danych

### Przykład: `PATCH /api/tasks/:id`

1. Zapytanie trafia do `routes/taskRoutes.js`
2. Przechodzi przez:
   - `auth` → sprawdzenie JWT
   - `validateUpdateTaskInput` + `validate`
   - `handleTryCatch`
3. Trafia do `taskController.updateTask`
4. Wywołuje `Task.findById(...)`, modyfikuje dane, zapisuje
5. Zwraca pełen `task` poprzez `sendSuccess(...)`

---

## ✅ Konwencje i standardy

- Wszystkie kontrolery używają `sendSuccess` / `sendError`
- Wszystkie trasy opakowane są przez `handleTryCatch`
- Nazwy plików odpowiadają nazwom funkcji (`createTask → createTask.js` w serwisie)
- Komponenty nie powielają logiki (DRY)
- Tylko `services/` mają prawo rozmawiać z OpenAI
- Brak zagnieżdżonych `try/catch` – wszystko przez wrappery

---

## 🧩 Przykładowe zależności

```
systemRoutes.js → systemController.js → openaiKeyManager.js
taskRoutes.js → taskController.js → aiSummaryService.js → gptService.js
```

---

## 📄 Dokumenty powiązane

- `controllers.md` – funkcje i odpowiedzi kontrolerów
- `routes.md` – przypisanie tras i middleware
- `api_spec.md` – dane wejściowe/wyjściowe API
- `services.md` – definicje logiki AI
- `middleware.md`, `validators.md`, `utils.md`

