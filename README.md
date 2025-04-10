# AI Task App

Aplikacja webowa, która pełni funkcję osobistego asystenta AI do zarządzania zadaniami. Aplikacja będzie hostowana lokalnie na moim serwerze domowym i dostępna z dowolnego urządzenia. Dane będą zapisywane w MongoDB (zarówno lokalnie, jak i w kopii chmurowej MongoDB Atlas). System wykorzystuje GPT (GPT-4o) do wspomagania użytkownika w tworzeniu, opisywaniu, przeszukiwaniu i zamykaniu zadań.

---

## 🎯 Cel systemu

Pomoc osobom wykonującym skomplikowane zadania (np. programistom, naukowcom, administratorom IT) w dokumentowaniu i przypominaniu sobie rozwiązań podobnych problemów w przeszłości. System pełni rolę wspierającego asystenta AI oraz wiedzy eksperckiej.

---

## 🔐 System kont i logowania

- Rejestracja użytkownika tylko z wykorzystaniem slotu wygenerowanego przez administratora
- Potwierdzenie e-maila po rejestracji
- Logowanie przez JWT
- Role: `admin` i `user`

---

## 📌 Zarządzanie zadaniami

- Użytkownik wpisuje opis problemu lub zadania
- GPT (GPT-4o) tworzy strukturę zadania w formacie JSON:
```json
{
  "title": "Skrypt do backupu",
  "description": "Zadanie polega na...",
  "dueDate": "2025-05-01",
  "notes": "Pilne"
}
```
- W przypadku niepoprawnej odpowiedzi GPT – fallback: dane zapisywane w `notes`
- Zadanie zapisywane jest do MongoDB i widoczne w interfejsie
- Użytkownik może edytować atrybuty zadania

---

## ✅ Zamykanie zadań

- Użytkownik informuje, że zadanie zostało wykonane
- Planowane: GPT generuje podsumowanie zakończenia
- Użytkownik zatwierdza lub edytuje podsumowanie

---

## 🔍 Przeszukiwanie historii zadań

- Planowane: generowanie embeddingów (`text-embedding-3-small`)
- Porównanie z poprzednimi zadaniami
- Wybrane zadania analizowane przez GPT jako podobne

---

## 💬 Inteligentne wsparcie AI (planowane)

GPT będzie wspierać użytkownika także poprzez:
- Odpowiadanie na pytania typu: „Jakie mam obecnie otwarte zadania?”
- Proponowanie zadań „na rozgrzewkę”
- Generowanie listy zadań wg trudności ocenionej na podstawie opisu (`difficulty`)
- Sortowanie zadań wg terminu realizacji
- Sugestie grupowania zadań wg typu lub obszaru tematycznego

---

## 🤖 Integracja z OpenAI

- Backend obsługuje model GPT-4o (OpenAI)
- Odpowiedzi GPT w formacie JSON
- W przypadku błędów: fallback + zapis do `logs/gpt_fallbacks.log`
- Użytkownik podaje swój klucz API OpenAI
- Planowane: szyfrowanie klucza (AES)

---

## 🧹 Czyszczenie historii GPT

- Po zakończeniu rozmowy z GPT lub zamknięciu zadania kontekst AI jest resetowany

---

## 🧠 Dane i baza danych

- MongoDB lokalnie + MongoDB Atlas
- Użytkownicy mają logicznie odseparowane dane (`ownerId`)
- Planowana kolekcja `taskEmbeddings` do semantycznych porównań zadań
- Planowane: organizacje, współdzielona wiedza zespołowa

---

## 💡 Przykładowy scenariusz

> Użytkownik wpisuje: "Nie mogę się połączyć z API, chyba chodzi o tokeny."

> GPT sugeruje podobny przypadek: "Integracja z API uczelni", gdzie brakowało nagłówka Authorization.

---

## 🧰 Technologie

- **Frontend:** React + TailwindCSS (planowany)
- **Backend:** Node.js (Express)
- **Baza danych:** MongoDB (lokalnie i Atlas)
- **Autoryzacja:** JWT, bcrypt
- **AI:** OpenAI GPT-4o (API)
- **Inne:** dotenv, Mongoose, Prettier, AES (planowane)

---

## 📁 Repozytoria

- [Backend (Node.js)](https://github.com/MichalLukawski/ai-task-app-backend)
- [Frontend (React)](https://github.com/MichalLukawski/ai-task-app-frontend)

---

## 🧩 Struktura projektu z submodułami Git

```
ai-task-app/
├── backend/     ← submoduł backendu
├── frontend/    ← submoduł frontendu
├── docs/        ← pełna dokumentacja projektu Markdown
├── .gitmodules
├── README.md
```

### ✅ Klonowanie z submodułami

```bash
git clone https://github.com/MichalLukawski/ai-task-app.git
cd ai-task-app
git submodule update --init --recursive
```

### 🔁 Aktualizacja submodułów

```bash
git submodule update --remote --merge
```
