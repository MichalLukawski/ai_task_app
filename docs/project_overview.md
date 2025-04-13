# 📘 Przegląd projektu – AI Task App (wersja rozszerzona)

AI Task App to aplikacja webowa wspierana przez GPT-4o, która pełni rolę osobistego asystenta technicznego do zarządzania zadaniami. Łączy nowoczesne technologie frontendowe i backendowe, oferując funkcje tworzenia, przeglądania, zamykania oraz porównywania zadań z wykorzystaniem AI.

---

## 🎯 Cele systemu

- Ułatwienie dokumentowania problemów technicznych
- Wspomaganie opisu i analizy zadań przez GPT-4o
- Odnajdywanie podobnych przypadków z przeszłości dzięki embeddingom
- W przyszłości: osobista baza wiedzy, współpraca zespołowa
- Redukcja wysiłku poznawczego i lepsza organizacja pracy

---

## 🧱 Architektura aplikacji

### Backend

- Node.js + Express + MongoDB
- JWT do autoryzacji, bcrypt do haseł
- GPT-4o + text-embedding-3-small (OpenAI)
- Modularna struktura: controllers, middleware, services, routes
- AES-256-GCM do szyfrowania kluczy API
- Przechowywanie embeddingów i porównania cosine similarity
- REST API zgodne z zasadami HTTP (np. `PATCH` do zamykania)

### Frontend

- React + Vite + TailwindCSS v4
- `AuthContext` do zarządzania sesją
- `ProtectedRoute` do ochrony widoków
- `Header` z dynamiczną zawartością (login/logout/dashboard)
- `fetch()` do komunikacji z API
- Token JWT w `localStorage`
- Planowana rozbudowa: `services/`, `TaskFormPage`, panel admina

---

## 📦 Modele danych

### `Task`

- `title`, `description`, `difficulty`, `dueDate`
- `summary`, `status`, `closedAt`
- `embedding[]`, `similarTasks[]`
- `ownerId`, `createdAt`

### `User`

- `email`, `password`, `role`, `approvedByAdmin`, `emailVerified`
- Uwierzytelnianie JWT (token w nagłówku)

### `ApiKey`

- `scope`, `encryptedKey`, `iv`, `tag`
- Obsługa kluczy OpenAI z szyfrowaniem AES-256-GCM

---

## 🔁 Przepływ zadań

1. Użytkownik tworzy zadanie (manualnie lub z pomocą AI)
2. Backend generuje embedding (`text-embedding-3-small`)
3. Porównuje z istniejącymi zadaniami (`cosine similarity`)
4. Przypisuje do `similarTasks`
5. Zadanie może być zamknięte:
   - przez ocenę i wygładzenie `summary` (AI)
   - lub przez kopiowanie `summary` z innego zadania

---

## 🔐 System użytkowników

- Rejestracja: `POST /api/auth/register`
  - Konto wymaga zatwierdzenia przez admina
- Logowanie: `POST /api/auth/login` → JWT
- Role: `user`, `admin` (planowane)
- Frontend wykorzystuje `AuthContext` + `ProtectedRoute`

---

## 🧠 Wsparcie AI

- GPT-4o generuje:
  - `title`, `description`, `difficulty`, `dueDate`
  - ocenia `summary` i poprawia język
- AI dostępne przez `function calling` z OpenAI SDK
- Każda sesja AI ma kontekstowy prompt z datą i zadaniem

---

## 📁 Struktura monorepo

```
ai-task-app/
├── backend/              # API + AI + MongoDB
├── frontend/             # React + Vite + Tailwind
├── docs/                 # Pełna dokumentacja Markdown
├── .gitmodules
└── README.md             # Główne podsumowanie projektu
```

---

## ⚙️ Technologie

| Warstwa  | Technologie                            |
| -------- | -------------------------------------- |
| Backend  | Node.js, Express, MongoDB, OpenAI, AES |
| Frontend | React, TailwindCSS v4, Vite            |
| API      | REST, JWT, fetch, Authorization        |
| AI       | GPT-4o, text-embedding-3-small         |
| Inne     | bcrypt, dotenv, concurrently, prettier |

---

## 🧪 Testowanie

- `express-validator` – walidacja backend
- `validate.js` + `sendError()` – warstwa błędów
- (planowane) `Vitest`, `Cypress`, `Jest`, `mock fetch()`

---

## 🧩 Wnioski architektoniczne

- AI integruje się przez warstwę usług `services/`
- Embeddingi i `similarTasks` są automatyczne
- API rozdziela `ai-close` i `close` dla większej przejrzystości
- Frontend i backend działają razem dzięki `concurrently`
- Dokumentacja obsługuje obie warstwy i pozwala na rozbudowę systemu
