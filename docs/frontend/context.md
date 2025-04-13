# 🔐 Dokumentacja – `AuthContext` (Frontend AI Task App)

Ten dokument szczegółowo opisuje logikę globalnego kontekstu autoryzacji (`AuthContext`) w aplikacji frontendowej AI Task App. Kontekst ten pełni kluczową rolę w zarządzaniu sesją użytkownika, uwierzytelnianiem, przechowywaniem tokena JWT oraz zabezpieczaniem widoków i tras.

---

## 🎯 Cel `AuthContext`

`AuthContext` został stworzony w celu:

- globalnego zarządzania stanem logowania użytkownika,
- bezpiecznego przechowywania tokena JWT w `localStorage`,
- umożliwienia automatycznego przywrócenia sesji po odświeżeniu strony,
- zapewnienia łatwego dostępu do metod `login()` i `logout()`,
- udostępniania flag `isAuthenticated`, `isLoading` w całej aplikacji.

---

## 📁 Lokalizacja i struktura

Plik: `src/context/AuthContext.jsx`

Eksportuje:

- `AuthProvider` – opakowanie aplikacji (provider kontekstu)
- `useAuth()` – hook pozwalający komponentom na dostęp do kontekstu

---

## 🧠 Struktura kontekstu

```jsx
{
  token: string | null,
  user: object | null,
  isAuthenticated: boolean,
  isLoading: boolean,
  login: (token: string, user?: object) => void,
  logout: () => void
}
```

---

## 🔄 Inicjalizacja i przywracanie sesji

Po załadowaniu aplikacji (`main.jsx`), `AuthProvider`:

- odczytuje token z `localStorage`,
- jeśli istnieje → ustawia `token`, `isAuthenticated = true`,
- ustawia `isLoading = false` po zakończeniu inicjalizacji.

Dzięki temu komponenty chronione przez `ProtectedRoute` mogą czekać na zakończenie inicjalizacji zanim podejmą decyzję o przekierowaniu.

---

## 🔐 Logowanie (`login(token, user?)`)

Metoda `login()`:

- zapisuje token do `localStorage`
- aktualizuje `token`, `isAuthenticated = true`, `user`
- może być wywołana w `LoginPage` po otrzymaniu tokena z backendu

---

## 🚪 Wylogowanie (`logout()`)

Metoda `logout()`:

- usuwa token z `localStorage`
- czyści `token`, `user`, `isAuthenticated = false`
- przekierowanie do `/` realizowane zwykle w `Header`

---

## 🔗 Użycie `useAuth()`

W dowolnym komponencie (np. `Header`, `LoginPage`, `ProtectedRoute`):

```jsx
import { useAuth } from "../context/AuthContext";

const { isAuthenticated, login, logout } = useAuth();
```

---

## 🔁 Przykład zastosowania w `main.jsx`

```jsx
import { AuthProvider } from "./context/AuthContext";

<React.StrictMode>
  <AuthProvider>
    <App />
  </AuthProvider>
</React.StrictMode>;
```

---

## ✅ Wsparcie dla `ProtectedRoute`

`ProtectedRoute` używa:

```jsx
const { isAuthenticated, isLoading } = useAuth();
```

- jeśli `isLoading` → zwraca `null`
- jeśli `!isAuthenticated` → `<Navigate to="/login" />`
- jeśli `isAuthenticated` → renderuje dzieci

---

## 🧩 Relacje i zależności

| Plik / komponent     | Powiązanie                                                                |
| -------------------- | ------------------------------------------------------------------------- |
| `Header.jsx`         | renderuje `Logout` lub `Login/Register` w zależności od `isAuthenticated` |
| `LoginPage.jsx`      | po zalogowaniu wywołuje `login(token)`                                    |
| `ProtectedRoute.jsx` | chroni dostęp do tras z użyciem `useAuth()`                               |
| `App.jsx`            | zawiera `<Routes>`, opakowane w `<AuthProvider>`                          |
| `localStorage`       | miejsce trwałego przechowywania tokena (client-only)                      |

---

## 🧠 Uwagi bezpieczeństwa

- `AuthContext` nie weryfikuje ważności tokena – zakłada, że backend go sprawdza przy każdym żądaniu
- Token JWT **nigdy nie jest przesyłany do komponentów UI**
- W przyszłości możliwe: dekodowanie tokena po stronie klienta (rola, expiry, claims)

---

## 🧭 Możliwe rozszerzenia

- Obsługa `user` z dodatkowymi danymi (email, rola)
- Dekodowanie i walidacja tokena po stronie klienta (`jwt-decode`)
- Wygaszanie sesji (np. po 15 min braku aktywności)
- Obsługa `refresh token`

---

## 📄 Dokumentacja powiązana

- `components.md` – `Header`, `ProtectedRoute` korzystają z kontekstu
- `pages/LoginPage.jsx` – implementuje logikę logowania
- `routing.md` – chronione trasy (`/tasks`)
- `src.md` – lokalizacja pliku i integracja z `main.jsx`
