# 🔐 Autoryzacja i uwierzytelnianie – AI Task App (zaktualizowana wersja)

Dokument ten przedstawia pełny przepływ rejestracji, logowania i uwierzytelniania użytkownika w aplikacji AI Task App. Opisuje architekturę backendu i frontendowego zarządzania sesją, wykorzystanie tokenów JWT, rolę middleware oraz planowane rozszerzenia bezpieczeństwa.

---

## 🧾 Rejestracja (`POST /api/auth/register`)

### Proces:

1. Użytkownik wypełnia formularz rejestracyjny z polami:
   - `email`
   - `password` (min. 6 znaków)
2. Frontend przesyła dane do endpointa `POST /api/auth/register`
3. Backend:
   - Waliduje dane (`authValidator.js → validateRegisterInput`)
   - Sprawdza, czy użytkownik o takim e-mailu już istnieje
   - Haszuje hasło (`bcrypt`)
   - Tworzy nowy dokument `User`
4. Odpowiedź `201 Created` + komunikat `User registered successfully`

### Uwaga:

- Obecna wersja nie zawiera systemu aktywacji konta ani potwierdzania adresu e-mail – są one zaplanowane jako rozszerzenie.
- Konta są od razu aktywne po rejestracji.

---

## 🔑 Logowanie (`POST /api/auth/login`)

### Proces:

1. Użytkownik wprowadza `email` i `password`
2. Frontend wysyła żądanie `POST /api/auth/login`
3. Backend:
   - Weryfikuje dane (`authValidator.js → validateLoginInput`)
   - Szuka użytkownika w bazie (`User.findOne`)
   - Sprawdza hasło (`bcrypt.compare(...)`)
   - Generuje token JWT:
     - Payload: `{ id: user._id }`
     - Ważność: 7 dni
   - Zwraca `token` w polu `data`

**Odpowiedź:**

```json
{
  "status": "success",
  "message": "Login successful",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIs..."
  }
}
```

---

## 🧠 Uwierzytelnianie – middleware `auth.js`

### Działanie:

1. Sprawdza obecność nagłówka `Authorization: Bearer <token>`
2. Weryfikuje JWT (secret z `.env`)
3. Pobiera użytkownika z bazy (`User.findById`)
4. Jeśli użytkownik istnieje, przypisuje do `req.user`:

   - `id`
   - `email`
   - `role` (w przyszłości)

5. Jeśli token jest nieprawidłowy lub użytkownik nie istnieje:
   - Zwraca błąd `401 Unauthorized`

> Middleware `auth` używany jest w każdej trasie chronionej (np. `/api/tasks`, `/api/system/openai-key`)

---

## 🧩 Zarządzanie sesją – frontend (`AuthContext`)

### Przechowywanie tokena:

- Token zapisywany jest w `localStorage` po udanym logowaniu
- `AuthContext` udostępnia funkcje:
  - `login(token)` – zapis tokena
  - `logout()` – usuwa token
  - `isAuthenticated` – bool na podstawie obecności tokena
  - `user`, `setUser()` – miejsce na przyszłą obsługę danych użytkownika

### Automatyczne uwierzytelnienie:

- `AuthProvider` przy starcie sprawdza `localStorage.getItem("token")`
- Jeśli token istnieje – ustawia `isAuthenticated = true`
- Możliwość walidacji tokena przez `GET /api/auth/me` (do rozważenia)

---

## 📦 Walidacja

### `authValidator.js` zawiera:

- `validateRegisterInput`

  - `email` – wymagany, poprawny format
  - `password` – min. 6 znaków

- `validateLoginInput`
  - `email` – wymagany, poprawny format
  - `password` – wymagany

Walidacja wspierana przez middleware `validate.js`, który agreguje błędy i zwraca je w jednolitym formacie:

```json
{
  "status": "error",
  "message": "Email is required; Password must be at least 6 characters",
  "code": "VALIDATION_ERROR"
}
```

---

## 🛡️ Planowane rozszerzenia autoryzacji

- Role użytkowników (`admin`, `user`)
- Middleware `requireRole("admin")`
- Aktywacja konta przez e-mail
- Limit logowań / captcha przy błędach
- Możliwość zmiany hasła
- Endpoint `GET /api/auth/me` do walidacji tokena
- Przechowywanie sesji w cookies `HttpOnly` (dla bezpieczeństwa)

---

## 📄 Dokumentacja powiązana

- `routes.md` – `/api/auth/*`
- `controllers.md` – `authController.js`
- `validators.md` – `authValidator.js`
- `middleware.md` – `auth.js`, `validate.js`
- `project_overview.md`, `project_roadmap.md`
