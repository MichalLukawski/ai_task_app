# AI Task App – Dokumentacja techniczna (backend + rozwój)

## 📘 Wstęp

AI Task App to osobista aplikacja webowa wspierana przez sztuczną inteligencję (GPT), która wspomaga użytkowników technicznych (np. programistów, administratorów IT) w zarządzaniu zadaniami, dokumentowaniu wiedzy oraz wyszukiwaniu podobnych problemów z przeszłości.

System opiera się na architekturze klient-serwer, z backendem zrealizowanym w Node.js (Express) i bazą danych MongoDB. Celem projektu jest stworzenie narzędzia, które może służyć nie tylko indywidualnym użytkownikom, ale również zespołom i firmom.

---

## 🎯 Cel dokumentacji

Dokumentacja ma służyć jako:

- Kompendium wiedzy o strukturze backendu,
- Podstawa do współpracy z GPT (prompt inżynieryjny),
- Pomoc przy rozwoju i utrzymaniu aplikacji w przyszłości.

---

## 🔧 1. Konfiguracja środowiska backendu

Projekt został zainicjalizowany za pomocą `npm init -y`. Następnie zainstalowano:

```
express, mongoose, dotenv, bcrypt, jsonwebtoken, cors
```

### Utworzone pliki:

- `server.js` – punkt wejścia, tworzy serwer Express,
- `config/db.js` – połączenie z MongoDB,
- `.env` i `.env.example` – konfiguracja środowiskowa.

---

## 🗃️ 2. Połączenie z MongoDB

W `config/db.js` użyto `mongoose.connect()` do połączenia z bazą danych. W przypadku błędu:

- logowany jest komunikat,
- aplikacja nie uruchamia się (`process.exit(1)`).

Po poprawnym połączeniu uruchamiany jest serwer.

---

## 📂 3. Modularna struktura katalogów

```
backend/
├── config/         # Połączenie z MongoDB
├── controllers/    # Logika endpointów
├── models/         # Schematy danych (np. User)
├── routes/         # Endpointy Express (np. authRoutes.js)
├── utils/          # Narzędzia pomocnicze (np. responseHandler)
```

---

## 🔐 4. Rejestracja użytkownika (API)

### Endpoint:

```
POST /api/auth/register
```

### Proces:

1. Sprawdzenie, czy użytkownik istnieje,
2. Jeśli nie: tworzenie nowego użytkownika,
3. Hashowanie hasła (`bcrypt`) w hooku `pre('save')`,
4. Zapis do kolekcji `users`,
5. Odpowiedź JSON (`success` / `error`).

---

## 📈 5. Planowane funkcjonalności backendu

- **Logowanie i JWT** (`/api/auth/login`)
- **Middleware autoryzacyjny** (`requireAuth`, `requireRole`)
- **CRUD dla zadań** (`/api/tasks`)
- **Model GPT jako asystent**:
  - generowanie struktury zadania,
  - podsumowanie rozwiązania,
  - sortowanie po trudności, terminie, pilności,
  - proponowanie kolejności realizacji,
  - wyszukiwanie zadań podobnych do aktualnego opisu
- **Endpointy pomocnicze** (`/api/health`)
- **Wsparcie dla organizacji i zespołów** (`organizationId`, `teamId`)
- **Statystyki kliknięć (dashboard + hasło)**

---

## 🌐 6. Frontend – założenia

Frontend rozwijany osobno (repozytorium: `ai-task-app-frontend`) – React + TailwindCSS.

### Planowane funkcje:

- Rejestracja i logowanie,
- Widok zadań i szczegółów,
- Formularz do tworzenia zadań wspomagany GPT,
- Przegląd historii i statystyk,
- Panel administratora,
- Ukryty dostęp do dashboardu przez ikonę + hasło.

---

## 🤖 7. Integracja z GPT

- Użytkownik podaje własny klucz OpenAI (zapisany zaszyfrowany),
- GPT generuje: strukturę zadania, podsumowanie wykonania, ocenę trudności,
- Możliwe integracje: klasyczny prompt, embeddingi (do semantycznego przeszukiwania).

---

## 🔁 8. Repozytoria i architektura submodułów

- `ai-task-app` – główne repo z dokumentacją i submodułami,
- `ai-task-app-backend` – backend Node.js (Express + MongoDB),
- `ai-task-app-frontend` – frontend React.

Submoduły dodane przez `.gitmodules`, obsługiwane przez:

```bash
git submodule update --init --recursive
```

---

## 📄 9. Dokumentacja i prompt GPT

Wszystkie dokumenty `.md` i `.docx` znajdują się w folderze `docs/`.

Służą jako:
- pomoc programistyczna,
- podstawa do budowania promptów dla ChatGPT (np. “Jak działa auth?”),
- baza wiedzy o projekcie.

---

## 🧠 10. Perspektywa rozwoju (skala korporacyjna)

Stack technologiczny (Node.js + MongoDB) jest wystarczający dla aplikacji obsługującej do 1000 pracowników, pod warunkiem:

- zastosowania dobrego indeksowania w Mongo,
- rozdzielenia zadań na organizacje / zespoły,
- użycia kolejek (np. do GPT),
- modularności kodu,
- możliwej przyszłej migracji do TypeScript lub ASP.NET Core + PostgreSQL (jeśli wymagane relacje, raportowanie, audyt).

---

