# AI Task App

Aplikacja webowa, która pełni funkcję osobistego asystenta AI do zarządzania zadaniami. Aplikacja będzie hostowana lokalnie na moim serwerze domowym i dostępna z dowolnego urządzenia. Dane będą zapisywane w MongoDB (zarówno lokalnie, jak i w kopii chmurowej MongoDB Atlas). System wykorzystuje GPT (GPT-4o) do wspomagania użytkownika w tworzeniu, opisywaniu, przeszukiwaniu i zamykaniu zadań.

## 🎯 Cel systemu

Pomoc osobom wykonującym skomplikowane zadania (np. programistom, naukowcom, administratorom IT) w dokumentowaniu i przypominaniu sobie rozwiązań podobnych problemów w przeszłości. System pełni rolę wspierającego asystenta AI oraz wiedzy eksperckiej.

## 🔐 System kont i logowania

- Rejestracja użytkownika tylko z wykorzystaniem slotu wygenerowanego przez administratora
- Potwierdzenie e-maila po rejestracji
- Logowanie przez JWT
- Role: `admin` i `user`

## 📌 Zarządzanie zadaniami

- Użytkownik wpisuje opis problemu lub zadania
- GPT (GPT-4o) tworzy strukturę zadania: tytuł, opis, termin, notatki
- Zadanie zapisywane jest do MongoDB i widoczne w interfejsie
- Użytkownik może edytować atrybuty zadania (np. termin)

## ✅ Zamykanie zadań

- Użytkownik informuje, że zadanie zostało wykonane
- GPT odnajduje najbardziej pasujące zadanie na podstawie opisu
- Generuje podsumowanie zakończenia
- Użytkownik zatwierdza lub prosi o korektę opisu

## 🔍 Przeszukiwanie historii zadań

- Użytkownik wpisuje opis problemu
- GPT przeszukuje semantycznie poprzednie zadania
- Zwraca najbardziej podobne przypadki wraz z notatkami i metodami rozwiązania

## 💬 Inteligentne wsparcie AI (planowane)

GPT będzie wspierać użytkownika w czasie rzeczywistym także poprzez:

- Odpowiadanie na pytania typu: „Jakie mam obecnie otwarte zadania?”
- Proponowanie zadań „na rozgrzewkę”
- Generowanie listy zadań wg trudności ocenionej na podstawie opisu
- Sortowanie zadań wg terminu realizacji
- Sugestie grupowania zadań wg typu lub obszaru tematycznego
- Pomoc w planowaniu dnia/tygodnia

## 🤖 Integracja z OpenAI

- Wersja 0.0.6: działa integracja z GPT-4o w backendzie
- Endpoint `/api/tasks/ai-create` generuje zadania z opisu użytkownika
- Użytkownik podaje swój klucz API OpenAI (np. GPT-4o)
- Klucz API przechowywany lokalnie (planowane: szyfrowanie AES)

## 🧹 Czyszczenie historii GPT

- Po zakończeniu rozmowy z GPT lub zamknięciu zadania kontekst AI jest resetowany

## 🧠 Dane i baza danych

- MongoDB lokalnie + MongoDB Atlas
- Użytkownicy mają logicznie odseparowane dane (`ownerId`)
- Planowane: organizacje, współdzielona wiedza zespołowa

## 💡 Przykładowy scenariusz

> Użytkownik wpisuje: "Nie mogę się połączyć z API, chyba chodzi o tokeny."

> GPT sugeruje podobny przypadek: "Integracja z API uczelni", gdzie brakowało nagłówka Authorization.

## 🧰 Technologie

- **Frontend:** React + TailwindCSS (planowany)
- **Backend:** Node.js (Express)
- **Baza danych:** MongoDB (lokalnie i Atlas)
- **Autoryzacja:** JWT, bcrypt
- **AI:** OpenAI GPT-4o (API)
- **Inne:** dotenv, Mongoose, Prettier, AES (planowane)

## 📁 Repozytoria

- [Backend (Node.js)](https://github.com/MichalLukawski/ai-task-app-backend)
- [Frontend (React)](https://github.com/MichalLukawski/ai-task-app-frontend)

## 🤖 Cel promptu

Ten prompt służy do współpracy z ChatGPT w trakcie rozwoju projektu.

## 🧩 Struktura projektu z submodułami Git

```
ai-task-app/
├── backend/     ← submoduł: https://github.com/MichalLukawski/ai-task-app-backend
├── frontend/    ← submoduł: https://github.com/MichalLukawski/ai-task-app-frontend
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
