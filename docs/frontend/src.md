# 📁 Dokumentacja – `src/` (Frontend AI Task App)

Plik ten dokumentuje zawartość katalogu `src/`, stanowiącego główny punkt wejścia dla aplikacji frontendowej AI Task App. Celem dokumentu jest szczegółowe przedstawienie struktury plików, zależności między modułami oraz zasad organizacyjnych przyjętych w tym projekcie.

---

## 📦 Ogólna struktura katalogu `src/`

## 📦 Struktura katalogu `src/`

```
src/
├── api/
│   └── axios.js
├── assets/
├── components/
│   ├── Header.jsx
│   ├── ProtectedRoute.jsx
│   ├── CreateTaskForm.jsx
│   ├── TaskList.jsx
│   ├── TaskCard.jsx
│   ├── TaskCardView.jsx
│   ├── TaskCardEdit.jsx
│   ├── DifficultyStars.jsx
│   ├── DueDateProgress.jsx
│   └── DueDateEditor.jsx
├── context/
│   └── AuthContext.jsx
├── hooks/
│   └── useTaskCardState.js
├── pages/
│   ├── WelcomePage.jsx
│   ├── LoginPage.jsx
│   ├── RegisterPage.jsx
│   └── DashboardPage.jsx
├── utils/
│   └── dateUtils.js
├── App.jsx
└── main.jsx
```

---

## 🧱 Opis głównych folderów

### 📁 `assets/`

- Mieści pliki statyczne (np. logo aplikacji, ikony SVG)
- Mogą być importowane jako moduły w React

---

### 📁 `components/`

Zawiera **wszystkie komponenty wielokrotnego użytku** aplikacji. Główne grupy:

- **Nawigacja i autoryzacja**: `Header`, `ProtectedRoute`
- **Obsługa zadań**:
  - `CreateTaskForm` – tworzenie zadania z AI
  - `TaskList` – kontener na karty zadań
  - `TaskCard`, `TaskCardView`, `TaskCardEdit` – prezentacja i edycja zadania
- **Elementy interfejsu**:
  - `DifficultyStars` – prezentacja i wybór trudności
  - `DueDateProgress` – pasek postępu terminu
  - `DueDateEditor` – pole edycji daty

➡️ Szczegółowy opis znajduje się w pliku `components.md`.

---

### 📁 `context/`

Zawiera Reactowy kontekst globalny (`AuthContext.jsx`), który:

- Przechowuje token JWT i dane użytkownika
- Umożliwia `login(token)` i `logout()`
- Obsługuje `isAuthenticated` i `isLoading`
- Udostępniany przez `useAuth()`

> Używany w całej aplikacji (np. Header, ProtectedRoute)

---

## 🪝 `hooks/`

- `useTaskCardState.js` – niestandardowy hook do zarządzania trybem podglądu/edycji zadania (`TaskCard`).
- Abstrahuje logikę UI i umożliwia przełączanie stanu w spójny sposób.

➡️ Pełny opis hooków znajduje się w `hooks.md`.

## 📄 `pages/`

Zawiera komponenty widoków powiązane z trasami (`react-router-dom`):

- `WelcomePage` – ekran powitalny (`/`)
- `LoginPage` – formularz logowania (`/login`)
- `RegisterPage` – formularz rejestracji (`/register`)
- `DashboardPage` – główny widok użytkownika (`/dashboard`) z listą zadań i formularzem

➡️ Szczegóły: `pages.md`

---

### 📁 `services/`

Zawiera funkcje odpowiedzialne za komunikację z backendem przez REST API.

- `authService.js` – logowanie, rejestracja (`/api/auth`)
- `taskService.js` – pobieranie i zapisywanie zadań (`/api/tasks`)
- `api.js` (opcjonalnie) – ustawienia endpointów, np. `VITE_API_URL`

---

## ⚙️ `utils/`

- `dateUtils.js` – funkcje do przetwarzania dat (np. obliczanie postępu terminu, formatowanie).
- Wspierają komponenty takie jak `DueDateProgress`.

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

## 🧩 Zasady organizacji kodu

- Folder `components/` zawiera tylko komponenty UI – logika powinna być delegowana do hooków lub utils
- Folder `pages/` – tylko komponenty widokowe, powiązane z trasami
- `hooks/` i `utils/` – wspierające logikę i funkcjonalność
- `api/` – komunikacja z backendem (rozszerzalne na `taskService`, `authService`, itp.)
- `context/` – dane globalne (obecnie tylko `AuthContext`)

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

- `components.md` – szczegóły komponentów
- `pages.md` – komponenty widokowe
- `routing.md` – konfiguracja tras
- `hooks.md` – logika niestandardowych hooków
- `utils.md` – funkcje pomocnicze
