# 📁 Dokumentacja – `src/` (Frontend AI Task App)

Plik ten dokumentuje zawartość katalogu `src/`, stanowiącego główny punkt wejścia dla aplikacji frontendowej AI Task App. Celem dokumentu jest szczegółowe przedstawienie struktury plików, zależności między modułami oraz zasad organizacyjnych przyjętych w tym projekcie.

---

## 📦 Ogólna struktura katalogu `src/`

```
src/
├── assets/          # Zasoby statyczne (np. logotypy, SVG)
├── components/      # Komponenty współdzielone (Header, Input, Modal itd.)
├── context/         # Zarządzanie globalnym stanem (AuthContext)
├── pages/           # Widoki stron (Welcome, Login, Register, Tasks itd.)
├── services/        # Funkcje do komunikacji z backendem API
├── styles/          # Style globalne (TailwindCSS – tylko index.css)
├── App.jsx          # Główna struktura routingu + Header
├── main.jsx         # Punkt wejścia React + Provider (AuthContext)
├── index.css        # Tailwind: @tailwind base, components, utilities
└── vite-env.d.ts    # (opcjonalnie) automatyczne typowanie env (jeśli TypeScript)
```

---

## 🧱 Opis głównych folderów

### 📁 `assets/`

- Mieści pliki statyczne (np. logo aplikacji, ikony SVG)
- Mogą być importowane jako moduły w React

---

### 📁 `components/`

Komponenty wielokrotnego użytku – np.:

- `Header.jsx` – widoczny na każdej stronie, zawiera nawigację zależną od logowania
- `ProtectedRoute.jsx` – otacza komponenty wymagające autoryzacji
- `Input.jsx`, `Button.jsx` – potencjalne komponenty UI

Każdy komponent powinien być **mały, deklaratywny, z pojedynczą odpowiedzialnością**.

---

### 📁 `context/`

Zawiera Reactowy kontekst globalny (`AuthContext.jsx`), który:

- Przechowuje token JWT i dane użytkownika
- Umożliwia `login(token)` i `logout()`
- Obsługuje `isAuthenticated` i `isLoading`
- Udostępniany przez `useAuth()`

> Używany w całej aplikacji (np. Header, ProtectedRoute)

---

### 📁 `pages/`

Zawiera pełne widoki stron (zgodne z trasami):

- `WelcomePage.jsx` – ekran główny (nawigacja: Login / Register)
- `LoginPage.jsx` – formularz logowania
- `RegisterPage.jsx` – formularz rejestracji
- `TasksPage.jsx` – strona z listą zadań użytkownika
- `TaskFormPage.jsx` – (planowane) – tworzenie zadania z AI

Każda strona stanowi samodzielny widok nawigacyjny (`Route`).

---

### 📁 `services/`

Zawiera funkcje odpowiedzialne za komunikację z backendem przez REST API.

- `authService.js` – logowanie, rejestracja (`/api/auth`)
- `taskService.js` – pobieranie i zapisywanie zadań (`/api/tasks`)
- `api.js` (opcjonalnie) – ustawienia endpointów, np. `VITE_API_URL`

---

## 📄 Pliki główne

### 🔹 `App.jsx`

- Definiuje strukturę routingu (`react-router-dom`)
- Zawiera `Header` i `<Routes>`
- Przekierowania: `/login`, `/register`, `/tasks`

### 🔹 `main.jsx`

- Punkt wejścia aplikacji React (`ReactDOM.createRoot`)
- Owijanie `AuthProvider`
- Import stylów `index.css`

```jsx
<React.StrictMode>
  <AuthProvider>
    <App />
  </AuthProvider>
</React.StrictMode>
```

---

## 🎨 Stylowanie

- Stylowanie odbywa się **wyłącznie przez TailwindCSS**
- Globalne style: `src/index.css` zawiera:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

- Nie używamy `.css`, `scss`, `styled-components` ani zewnętrznych bibliotek UI (poza planowanymi `shadcn/ui` lub `headlessui`)

---

## 🌐 Routing i ochrona tras

- Router: `react-router-dom`
- Widoki chronione przez `ProtectedRoute`
- Przykład:

```jsx
<Route
  path="/tasks"
  element={
    <ProtectedRoute>
      <TasksPage />
    </ProtectedRoute>
  }
/>
```

---

## 🔐 Autoryzacja (JWT)

- Po logowaniu token zapisuje się w `localStorage`
- `AuthContext` przy starcie aplikacji odczytuje go i aktualizuje stan
- Jeśli `isLoading = true` – komponenty chronione czekają z renderem

---

## 🧠 Responsywność i UI

- Layout mobilny oparty o `flex` + `min-h-screen` + `justify-center`
- Nawigacja (nagłówek) responsywny: `sm:`, `md:` w klasach Tailwind
- `Header` dynamicznie reaguje na stan logowania

---

## 📄 Dokumentacja powiązana

- `pages.md`
- `components.md`
- `context.md`
- `services.md`
- `routing.md`
- `auth_flow.md`
- `frontend_readme.md`
