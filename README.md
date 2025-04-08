# AI Task App

Aplikacja webowa, która pełni funkcję osobistego asystenta AI do zarządzania zadaniami. Aplikacja będzie hostowana lokalnie na moim serwerze domowym i dostępna z dowolnego urządzenia. Dane będą zapisywane w MongoDB (zarówno lokalnie, jak i w kopii chmurowej MongoDB Atlas). System będzie wykorzystywać model GPT (np. GPT-4) do wsparcia użytkownika w tworzeniu, opisywaniu, przeszukiwaniu i zamykaniu zadań.

## 🎯 Cel systemu

Pomoc osobom wykonującym skomplikowane zadania (np. programistom, naukowcom, administratorom IT) w dokumentowaniu i przypominaniu sobie rozwiązań podobnych problemów w przeszłości. System pełni rolę wspierającego asystenta AI oraz wiedzy eksperckiej.

## 🔐 System kont i logowania

- Rejestracja użytkownika tylko z wykorzystaniem slotu wygenerowanego przez administratora
- Potwierdzenie e-maila po rejestracji
- Logowanie przez JWT
- Role: `admin` i `user`

## 📌 Zarządzanie zadaniami

- Użytkownik wpisuje opis problemu lub zadania
- GPT tworzy strukturę zadania: tytuł, opis, termin, notatki itp.
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
- Proponowanie zadań „na rozgrzewkę” — łatwych, które można szybko zamknąć
- Generowanie listy zadań wg trudności ocenionej na podstawie opisu
- Sortowanie zadań wg terminu realizacji („Co jest najpilniejsze?”)
- Sugestie grupowania zadań wg typu lub obszaru tematycznego
- Wyszukiwanie zadań podobnych do aktualnego problemu
- Pomoc w planowaniu dnia/tygodnia na podstawie aktualnych zadań i czasu trwania

## 🤖 Integracja z OpenAI

- Użytkownik podaje swój własny klucz API OpenAI
- Obsługiwane są tylko konta z dostępem do GPT-4
- Klucz API przechowywany zaszyfrowany (np. AES)

## 🧹 Czyszczenie historii GPT

- Po zakończeniu rozmowy z GPT lub zamknięciu zadania, kontekst jest czyszczony
- Aplikacja nie zostawia historii rozmów na koncie OpenAI

## 🧠 Dane i baza danych

- MongoDB lokalnie + MongoDB Atlas (kopiowanie, backup)
- Użytkownicy mają logicznie odseparowane dane (`ownerId`)
- W przyszłości: możliwość tworzenia organizacji i wspólnej bazy wiedzy wewnątrz firmy

## 💡 Przykładowy scenariusz użycia

> Użytkownik wpisuje: "Nie mogę się połączyć z zewnętrznym API, chyba chodzi o tokeny."

> GPT wyszukuje w bazie podobne zadanie sprzed 2 miesięcy.

> Odpowiada: "Wygląda na podobny przypadek: 'Integracja z API uczelni', gdzie trzeba było dodać nagłówek Authorization z JWT."

> Użytkownik wykorzystuje znalezione informacje do szybszego rozwiązania problemu.

## 🧰 Technologie

- **Frontend:** React + TailwindCSS
- **Backend:** Node.js (Express)
- **Baza danych:** MongoDB (lokalnie i Atlas)
- **Autoryzacja:** JWT, bcrypt
- **AI:** OpenAI GPT-4 (z kluczem użytkownika)
- **Inne:** dotenv, Mongoose, AES do szyfrowania, middleware do ról

## 📁 Repozytoria

- [Backend (Node.js)](https://github.com/MichalLukawski/ai-task-app-backend)
- [Frontend (React)](https://github.com/MichalLukawski/ai-task-app-frontend)

## 🤖 Cel promptu

Ten prompt służy do współpracy z ChatGPT w trakcie tworzenia aplikacji. Chat powinien:

- pomagać pisać kod (frontend/backend/API/modele)
- sugerować architekturę folderów
- doradzać w wyborze struktur danych
- podpowiadać dobre praktyki zabezpieczeń i architektury
- wspierać w integracji GPT z danymi użytkownika
- pomagać debugować błędy i rozwijać system

System może w przyszłości stać się wewnętrznym narzędziem wiedzy i produktywności dla zespołów lub firm.


## 🧩 Struktura projektu z submodułami Git

Repozytorium `ai-task-app` pełni funkcję repo głównego, zarządzającego projektem.  
Zawiera dwa submoduły Git, które wskazują na osobne repozytoria:

```
ai-task-app/
├── backend/     ← submoduł: https://github.com/MichalLukawski/ai-task-app-backend
├── frontend/    ← submoduł: https://github.com/MichalLukawski/ai-task-app-frontend
├── .gitmodules
├── README.md
```

### ✅ Klonowanie z submodułami

Aby poprawnie sklonować całe repo wraz z podmodułami:

```bash
git clone https://github.com/MichalLukawski/ai-task-app.git
cd ai-task-app
git submodule update --init --recursive
```

### 🔁 Aktualizacja submodułów

Jeśli submoduły zostały zaktualizowane (np. zmiany w backendzie):

```bash
git submodule update --remote --merge
```

### ⚠️ Uwaga

Podmoduły są zarządzane niezależnie. Wchodząc do folderu `backend/` lub `frontend/`, można normalnie pracować z Git:

```bash
cd backend
git pull origin main
```

Dzięki takiej architekturze zachowana jest modularność, historia commitów w każdym komponencie i możliwość niezależnego rozwoju backendu i frontendu.
