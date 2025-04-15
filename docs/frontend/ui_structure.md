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

### 🔹 Widoki (`pages/`)

- `WelcomePage` – ekran startowy dla wszystkich użytkowników.
- `LoginPage` i `RegisterPage` – formularze logowania i rejestracji.
- `DashboardPage` – główny widok użytkownika po zalogowaniu, zawierający:
  - pasek nagłówkowy (Header),
  - formularz tworzenia zadania (`CreateTaskForm`),
  - listę zadań (`TaskList` z kartami `TaskCard`),
  - komponenty pomocnicze: `DifficultyStars`, `DueDateProgress`, `DueDateEditor`.

---

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

## 🗺️ Schemat widoku Dashboard

```
+------------------------------------------------------+
| Header (zawsze widoczny)                            |
+------------------------------------------------------+
|                                                      |
| CreateTaskForm [formularz AI]                        |
|                                                      |
| +-----------------------------------------------+    |
| | TaskCard (1)                                   |    |
| | - tytuł, opis, trudność, termin                |    |
| | - przyciski: Edytuj, Zakończ                   |    |
| +-----------------------------------------------+    |
|                                                      |
| +-----------------------------------------------+    |
| | TaskCard (2)                                   |    |
| | ...                                             |    |
| +-----------------------------------------------+    |
+------------------------------------------------------+
```

---

## 🧩 Główne komponenty UI

| Komponent         | Opis                                                                    |
| ----------------- | ----------------------------------------------------------------------- |
| `CreateTaskForm`  | Formularz oparty o AI, umożliwiający tworzenie zadań z opisu tekstowego |
| `TaskList`        | Kontener renderujący kolejne `TaskCard`                                 |
| `TaskCard`        | Widok zadania (z możliwością edycji lub zamknięcia)                     |
| `TaskCardView`    | Widok podglądu (readonly) pojedynczego zadania                          |
| `TaskCardEdit`    | Tryb edycji zadania z możliwością zmiany daty, poziomu trudności        |
| `DifficultyStars` | Prezentacja i edycja trudności (skala 1–5)                              |
| `DueDateProgress` | Pasek postępu czasu do terminu wykonania                                |
| `DueDateEditor`   | Pole wyboru daty zakończenia zadania                                    |

---

## 📐 Stylowanie i layouty

- UI oparty wyłącznie na **TailwindCSS**
- Użycie klas utility: `flex`, `grid`, `gap`, `rounded`, `shadow`, `text-`, `bg-`, `hover-`
- Obsługa trybu mobilnego (`sm:`, `md:`)
- Brak użycia CSS modules, SCSS, styled-components

## 📱 Responsywność

- Layouty wspierają breakpointy: `sm`, `md`, `lg`
- Przykładowe klasy używane:
  - `text-3xl sm:text-4xl` (dynamiczny rozmiar nagłówków)
  - `max-w-xl`, `w-full`, `px-4` (ograniczenie szerokości)
- Komponenty skalują się poprawnie na telefonach i laptopach

---

## ✅ Zrealizowane funkcje UI

| Funkcja                           | Status | Uwagi                                                 |
| --------------------------------- | ------ | ----------------------------------------------------- |
| Pasek nawigacyjny (`Header`)      | ✅     | Dynamiczny na podstawie `useAuth()`                   |
| Formularz tworzenia zadania (AI)  | ✅     | `CreateTaskForm`, integracja z GPT                    |
| Wyświetlanie zadań w postaci kart | ✅     | `TaskList`, `TaskCard` z podglądem/edycją             |
| Wizualizacja trudności (`Stars`)  | ✅     | `DifficultyStars`, tryb readonly i edycji             |
| Pasek terminu (`DueDateProgress`) | ✅     | Kolor i szerokość paska zależna od czasu do `dueDate` |
| Edycja daty (`DueDateEditor`)     | ✅     | Walidacja, wpisywanie lub wybór terminu               |

---

## 🛠️ Potencjalne rozszerzenia UI

| Funkcja                  | Status       | Uwagi                                                  |
| ------------------------ | ------------ | ------------------------------------------------------ |
| Filtrowanie zadań        | 🔜 planowane | Na podstawie statusu, trudności, terminu               |
| Widok zakończonych zadań | 🔜 planowane | Oddzielna sekcja lub filtr                             |
| Powiadomienia / Alerty   | 🔜 planowane | Komunikaty sukcesu/błędu w formularzach i interakcjach |
| Spinner ładowania        | 🔜 planowane | Podczas zapytań do AI i pobierania danych              |
| EmptyState               | 🔜 planowane | Komunikat, gdy lista zadań jest pusta                  |

---

## 🧩 Relacje komponentów

- `DashboardPage` używa: `CreateTaskForm`, `TaskList`
- `TaskList` renderuje wiele `TaskCard`
- `TaskCard` wewnętrznie korzysta z `TaskCardView` lub `TaskCardEdit` w zależności od trybu
- `TaskCardEdit` korzysta z `DueDateEditor`, `DifficultyStars`
- `TaskCardView` korzysta z `DueDateProgress`, `DifficultyStars`

---

## 📄 Dokumentacja powiązana

- `components.md` – opis `Header`, `ProtectedRoute`
- `pages.md` – widoki odpowiadające trasom
- `routing.md` – przypisanie komponentów do ścieżek
- `context.md` – logika `AuthContext`, używana w UI
- `src.md` – pełna struktura katalogów
