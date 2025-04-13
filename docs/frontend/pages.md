# 📄 Dokumentacja – `pages/` (Frontend AI Task App)

Folder `src/pages/` zawiera główne widoki (strony) aplikacji. Każdy plik odpowiada jednemu z komponentów React reprezentujących trasę w aplikacji (zgodnie z konfiguracją `react-router-dom`). Widoki są spójnie stylizowane przy użyciu TailwindCSS i podzielone logicznie według funkcjonalności.

---

## 📦 Struktura katalogu `pages/`

```
pages/
├── WelcomePage.jsx       # Ekran powitalny aplikacji
├── LoginPage.jsx         # Formularz logowania
├── RegisterPage.jsx      # Formularz rejestracji
├── TasksPage.jsx         # Panel użytkownika z zadaniami (dawniej Dashboard)
├── TaskFormPage.jsx      # (Planowane) formularz tworzenia zadania
```

---

## 🧭 Zasady ogólne

- Każda strona jest komponentem funkcjonalnym (`function ComponentName`)
- Komponenty te są montowane przez router w `App.jsx` w zależności od ścieżki
- Stylowanie odbywa się wyłącznie przez TailwindCSS (`className`)
- Nie zawierają wewnętrznych zależności typu `useAuth()` (z wyjątkiem `LoginPage`)
- Layout (`Header`) znajduje się **poza** widokiem i ładowany globalnie

---

## 🌐 WelcomePage.jsx

### 📌 Opis

- Pierwszy widok po wejściu na aplikację (`/`)
- Zawiera nazwę systemu (`AI Task App`) oraz krótki opis jego roli
- Widoczny zarówno dla zalogowanych, jak i niezalogowanych użytkowników

### 📌 Funkcje

- Dynamicznie pokazuje linki: `Login` i `Register` (po prawej w nagłówku)
- Nie wymaga dostępu do API ani tokenu

---

## 🔐 LoginPage.jsx

### 📌 Opis

- Trasa: `/login`
- Pozwala użytkownikowi zalogować się do systemu

### 📌 Logika

- Obsługuje `email` i `password`
- Wysyła żądanie `POST /api/auth/login`
- Po sukcesie:
  - zapisuje token do `localStorage`
  - wywołuje `login(token)` z `useAuth()`
  - przekierowuje na `/tasks`

### 📌 Walidacja

- Email musi mieć poprawny format
- Hasło wymagane

---

## 🧾 RegisterPage.jsx

### 📌 Opis

- Trasa: `/register`
- Umożliwia nowemu użytkownikowi utworzenie konta

### 📌 Logika

- Obsługuje `email`, `password`, `confirmPassword`
- Wysyła żądanie `POST /api/auth/register`
- Po sukcesie pokazuje komunikat o oczekiwaniu na zatwierdzenie przez admina

### 📌 Walidacja

- Email – poprawny format
- Hasło – min. długość, zgodność z `confirmPassword`

### 📌 Przyszłość

- Potwierdzenie e-mail przez link aktywacyjny
- Panel dla administratora

---

## 📂 TasksPage.jsx (planowane)

### 📌 Opis

- Główny widok zalogowanego użytkownika (`/tasks`)
- Zabezpieczony przez `ProtectedRoute`

### 📌 Funkcje

- Wyświetla listę zadań użytkownika (pobranych z API)
- Obsługuje:
  - tytuł zadania
  - opis
  - datę, status, trudność
- Planowane: możliwość filtrowania, tworzenia, zamykania zadania

### 📌 Stylizacja

- Lista kart zadaniowych (`TaskCard`)
- Ewentualne użycie `grid`, `flex-col`, `gap-4`

---

## 📝 TaskFormPage.jsx (planowany)

### 📌 Opis

- Trasa: `/tasks/new`
- Pozwala użytkownikowi utworzyć nowe zadanie z pomocą AI

### 📌 Logika

- Wysyła `description` do endpointu `POST /api/tasks/ai-create`
- Odbiera `title`, `description`, `difficulty`, `dueDate`
- Zapisuje zadanie i generuje embedding

---

## 🧩 Relacje między widokami

| Strona       | Komponenty używane     | API                | Kontekst                            |
| ------------ | ---------------------- | ------------------ | ----------------------------------- |
| WelcomePage  | Header                 | brak               | brak                                |
| LoginPage    | Header, useAuth        | `/auth/login`      | `login()`                           |
| RegisterPage | Header                 | `/auth/register`   | brak                                |
| TasksPage    | Header, ProtectedRoute | `/tasks`           | `useAuth()` (pośrednio) (planowane) |
| TaskFormPage | Header                 | `/tasks/ai-create` | możliwy fallback (planowane)        |

---

## 📄 Dokumentacja powiązana

- `routing.md` – przypisanie tras do komponentów
- `components.md` – Header, ProtectedRoute
- `context.md` – `login()`, `logout()`, `isAuthenticated`
- `services.md` – logika `authService`, `taskService`
