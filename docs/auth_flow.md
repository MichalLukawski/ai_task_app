# 🔐 Dokumentacja – Autoryzacja i przepływ sesji (Auth Flow) w AI Task App

Niniejszy dokument opisuje pełen przepływ autoryzacji użytkownika w aplikacji AI Task App – od rejestracji po zakończenie sesji. Zawiera opis logiki działania logowania, rejestracji, przechowywania tokena, zabezpieczania tras oraz integracji z `AuthContext`.

---

## 🔁 Przegląd ogólny

1. Użytkownik rejestruje się przez formularz (`/register`)
2. Backend tworzy konto, ale:
   - Użytkownik wymaga **zatwierdzenia przez administratora**
   - (Opcjonalnie) także potwierdzenia e-mail
3. Po zatwierdzeniu – użytkownik loguje się przez `/login`
4. Backend zwraca token JWT
5. Token zapisywany jest w `localStorage`
6. `AuthContext` przywraca sesję przy każdym uruchomieniu aplikacji
7. Zalogowany użytkownik uzyskuje dostęp do tras chronionych (`/tasks`)
8. `Logout` czyści token i przekierowuje do `/`

---

## ✅ Rejestracja

### 🔹 Formularz – `RegisterPage.jsx`

- Pola: `email`, `password`, `confirmPassword`
- Wysyła `POST /api/auth/register`

### 🔹 Backend

- Tworzy użytkownika w bazie
- Ustawia `approvedByAdmin: false`, `emailVerified: false`
- Użytkownik nie może się zalogować do czasu zatwierdzenia (etap manualny)

### 🔹 Planowane

- Weryfikacja e-mail poprzez token (`/verify-email/:token`)
- Interfejs administratora do akceptowania kont

---

## 🔐 Logowanie

### 🔹 Formularz – `LoginPage.jsx`

- Pola: `email`, `password`
- Wysyła `POST /api/auth/login`

### 🔹 Backend

- Sprawdza `email` i `password`
- Sprawdza `approvedByAdmin === true`
- Zwraca token JWT

### 🔹 Frontend

- `authService.login()` zapisuje token
- `AuthContext.login(token)` aktualizuje stan globalny
- Przekierowuje do `/tasks`

---

## 🧠 Przechowywanie sesji

### 🔹 Token

- Zapisywany w `localStorage` jako `"token"`

### 🔹 `AuthContext.jsx`

- W `useEffect()` przy uruchomieniu odczytuje token
- Jeśli istnieje → ustawia `token`, `isAuthenticated = true`
- Jeśli nie istnieje → `isAuthenticated = false`

### 🔹 Flaga `isLoading`

- Zapobiega wyświetlaniu komponentów chronionych, zanim sesja zostanie zainicjalizowana
- W `ProtectedRoute`: jeśli `isLoading`, zwraca `null`

---

## 🔐 Ochrona tras

### 🔹 `ProtectedRoute.jsx`

- Jeśli `!isAuthenticated` → `<Navigate to="/login" />`
- W przeciwnym razie renderuje dzieci (`children`)
- Chroni m.in. trasę `/tasks`

---

## 🚪 Wylogowanie

### 🔹 `logout()` w `AuthContext`

- Usuwa token z `localStorage`
- Resetuje stan `token`, `user`, `isAuthenticated`
- Zwykle połączone z przekierowaniem na `/`

---

## 🧩 Współpraca między warstwami

| Element              | Rola                                                 |
| -------------------- | ---------------------------------------------------- |
| `AuthContext`        | zarządza tokenem, sesją                              |
| `authService.js`     | wykonuje żądania logowania/rejestracji               |
| `Header.jsx`         | pokazuje `Login`/`Register` lub `Logout`/`Dashboard` |
| `ProtectedRoute.jsx` | chroni dostęp do tras                                |

---

## 🧠 Token JWT

- Generowany przez backend (`jsonwebtoken`)
- Zawiera `userId`
- Przechowywany po stronie klienta
- Przesyłany do backendu w nagłówku:
  ```
  Authorization: Bearer <JWT>
  ```

---

## 🔧 Planowane rozszerzenia

- Automatyczne wygaszanie sesji (timeout)
- Obsługa `refresh token`
- Role użytkowników (`admin`, `user`)
- Dekodowanie JWT po stronie klienta (`jwt-decode`)
- Zmiana hasła, reset hasła (z tokenem)

---

## 📄 Dokumentacja powiązana

- `context.md` – struktura i logika `AuthContext`
- `components.md` – `Header`, `ProtectedRoute`
- `pages.md` – `LoginPage`, `RegisterPage`
- `services_PLANNED.md` – metody `login()`, `register()`
- `routing.md` – dostępność tras i ochrona
