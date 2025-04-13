# 🖼️ Dokumentacja – Struktura UI (Frontend AI Task App)

Ten dokument przedstawia szczegółowy opis struktury interfejsu użytkownika (UI) w aplikacji frontendowej AI Task App. Zawiera informacje o ułożeniu komponentów, architekturze layoutów, stylach, responsywności oraz planowanej ewolucji interfejsu. Przeznaczony jest zarówno dla osób rozwijających frontend, jak i tych, które chcą zrozumieć logikę prezentacji danych w aplikacji.

---

## 🎯 Założenia projektowe UI

- Interfejs oparty na **TailwindCSS**
- Responsywny layout: działa poprawnie na urządzeniach mobilnych, tabletach i desktopach
- Layout oparty na komponencie `Header` i dynamicznych widokach (`pages/`)
- Zachowanie spójności wizualnej bez użycia zewnętrznych bibliotek UI

---

## 🧱 Główne elementy interfejsu

### 1. 📌 Nagłówek (`Header.jsx`)

- Zawsze widoczny na górze każdej strony
- Zawiera:
  - nazwę aplikacji `AI Task App` (po lewej, link do `/`)
  - dynamiczne linki po prawej (`Login`, `Register`, `Logout`, `Dashboard`)
- Dostosowuje treść do stanu logowania (`useAuth()`)

### 2. 📄 Widok główny (`main`)

- Każda strona (`WelcomePage`, `LoginPage`, `TasksPage`) renderowana wewnątrz `<Routes>`
- Nie stosujemy dedykowanego komponentu layoutu (jak `MainLayout`) – ale można to rozważyć w przyszłości
- Stylizowane z wykorzystaniem `min-h-screen`, `flex`, `justify-center`, `px-4`, `py-8`

### 3. 🔐 Chronione trasy (`ProtectedRoute`)

- Działa jako wrapper dla widoków wymagających autoryzacji (`/tasks`)
- Zależny od `AuthContext` (`isAuthenticated`, `isLoading`)

---

## 🧭 Struktura layoutu – schemat

```
<body>
  <div id="root">
    <Header />
    <Routes>
      <Route path="/" element={<WelcomePage />} />
      ...
      <ProtectedRoute>
        <TasksPage />
      </ProtectedRoute>
    </Routes>
  </div>
</body>
```

---

## 📐 Stylowanie i layouty

- Wszystkie komponenty używają klas Tailwind (`text-gray-800`, `bg-gray-50`, `rounded`, itp.)
- Layout jest oparty na **Flexbox** (`flex`, `items-center`, `justify-between`)
- Brak użycia CSS-in-JS, SCSS czy Bootstrap
- Kolory, rozmiary i odstępy kontrolowane przez Tailwind
- `index.css` zawiera tylko:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

---

## 📱 Responsywność

- Layouty wspierają breakpointy: `sm`, `md`, `lg`
- Przykładowe klasy używane:
  - `text-3xl sm:text-4xl` (dynamiczny rozmiar nagłówków)
  - `max-w-xl`, `w-full`, `px-4` (ograniczenie szerokości)
- Komponenty skalują się poprawnie na telefonach i laptopach

---

## 🗺️ Widoki UI – zachowanie

| Widok        | Layout główny            | Zawartość UI                                 |
| ------------ | ------------------------ | -------------------------------------------- |
| `/`          | WelcomePage              | nagłówek, opis, linki do login/register      |
| `/login`     | LoginPage                | formularz logowania, błąd, przekierowanie    |
| `/register`  | RegisterPage             | formularz rejestracji, walidacja             |
| `/tasks`     | TasksPage                | lista zadań, dostępne tylko dla zalogowanych |
| `/tasks/new` | TaskFormPage (planowane) | formularz AI, odpowiedź GPT, zapis zadania   |

---

## 🔧 Planowane ulepszenia UI

| Funkcja          | Opis                                                 |
| ---------------- | ---------------------------------------------------- |
| `MainLayout.jsx` | komponent layoutu z nagłówkiem i `Outlet`            |
| `LoadingSpinner` | komponent do obsługi `isLoading` (`AuthContext`, AI) |
| `TaskCard`       | komponent zadania w `TasksPage`                      |
| `EmptyState`     | stan pustej listy zadań lub braku wyników            |
| `FormField`      | komponent formularzowy (input, label, błędy)         |

---

## 📄 Dokumentacja powiązana

- `components.md` – opis `Header`, `ProtectedRoute`
- `pages.md` – widoki odpowiadające trasom
- `routing.md` – przypisanie komponentów do ścieżek
- `context.md` – logika `AuthContext`, używana w UI
- `src.md` – pełna struktura katalogów
