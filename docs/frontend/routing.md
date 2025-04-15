# 🧭 Dokumentacja – Routing w AI Task App (Frontend)

Dokument ten opisuje mechanizm routingu w aplikacji frontendowej AI Task App, oparty na bibliotece `react-router-dom`. Routing odpowiada za mapowanie tras URL na konkretne komponenty widokowe (`pages/`) oraz zabezpieczanie dostępu do wybranych widoków za pomocą `ProtectedRoute`.

---

## ⚙️ Implementacja: plik `App.jsx`

W aktualnej wersji aplikacji routing znajduje się bezpośrednio w pliku `App.jsx` i wygląda następująco:

```jsx
<Routes>
  <Route path="/" element={<WelcomePage />} />
  <Route path="/login" element={<LoginPage />} />
  <Route path="/register" element={<RegisterPage />} />
  <Route
    path="/dashboard"
    element={
      <ProtectedRoute>
        <DashboardPage />
      </ProtectedRoute>
    }
  />
</Routes>
```

---

## 📌 Opis zarejestrowanych tras

| Ścieżka      | Komponent       | Dostęp                 | Opis                                                                 |
| ------------ | --------------- | ---------------------- | -------------------------------------------------------------------- |
| `/`          | `WelcomePage`   | Publiczny              | Strona startowa z informacją o aplikacji                             |
| `/login`     | `LoginPage`     | Publiczny (dla gości)  | Formularz logowania użytkownika                                      |
| `/register`  | `RegisterPage`  | Publiczny (dla gości)  | Formularz rejestracji użytkownika                                    |
| `/dashboard` | `DashboardPage` | Tylko dla zalogowanych | Główny panel użytkownika z listą zadań, formularzem AI, akcjami CRUD |

---

## 🔐 Ochrona tras – `ProtectedRoute`

Komponent `ProtectedRoute` zabezpiecza trasę `/dashboard`:

```jsx
<Route
  path="/dashboard"
  element={
    <ProtectedRoute>
      <DashboardPage />
    </ProtectedRoute>
  }
/>
```

Mechanizm działania:

- Jeśli `isLoading === true` (np. trwa odczyt tokena) → zwraca `null`
- Jeśli użytkownik nie jest zalogowany (`!isAuthenticated`) → przekierowanie do `/login`
- Jeśli użytkownik jest zalogowany → renderuje `children` (czyli np. `DashboardPage`)

---

## 🔁 Zachowania domyślne

- ✅ Po zalogowaniu: przekierowanie na `/dashboard`
- ✅ Po wylogowaniu (`logout()` z `Header`): przekierowanie na `/`

---

## ❌ Trasy niezaimplementowane

Poniższa trasa była planowana, ale nie została zaimplementowana:

| Ścieżka      | Komponent      | Status       | Uwagi                                                                             |
| ------------ | -------------- | ------------ | --------------------------------------------------------------------------------- |
| `/tasks/new` | `TaskFormPage` | ❌ planowany | Formularz tworzenia zadania z pomocą AI został zintegrowany w `DashboardPage.jsx` |

---

## 💡 Propozycja refaktoryzacji struktury

Aby zwiększyć skalowalność i czytelność projektu, routing może zostać przeniesiony do osobnego katalogu:

```
src/
├── routing/
│   ├── RouteConfig.jsx       # Główna konfiguracja routera
│   ├── GuestRoutes.jsx       # Trasy dostępne publicznie
│   ├── ProtectedRoutes.jsx   # Trasy wymagające autoryzacji
│   └── routes.js             # Zbiór wszystkich ścieżek i ich komponentów
```

Korzyści:

- modularna struktura kodu,
- rozdział odpowiedzialności (auth / dashboard / admin...),
- łatwe wprowadzenie lazy-loadingu i kodu asynchronicznego,
- gotowość na rozbudowę aplikacji (np. `/profile`, `/admin`, `/tasks/:id`)

---

## 🔎 Zalecenia

- ✅ W kodzie należy konsekwentnie używać `/dashboard` jako trasy głównej użytkownika (nie `/tasks`)
- ✅ Należy usunąć przestarzałe wzmianki o trasie `/tasks/new` w dokumentacji
- 🛠️ W przyszłości warto wprowadzić komponent `Layout`, który będzie renderował `Header` + `Outlet`, pozwalając uprościć konfigurację tras

---

## 📄 Dokumentacja powiązana

- `pages.md` – opisy komponentów przypisanych do tras
- `components.md` – `ProtectedRoute`, `Header`
- `context.md` – `useAuth()` i jego wpływ na routing
- `src.md` – ogólna struktura katalogów frontendowych
- `task_flow.md` – szczegółowy opis działania panelu użytkownika i ścieżek zadań
