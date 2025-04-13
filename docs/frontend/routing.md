# 🧭 Dokumentacja – Routing w AI Task App (Frontend)

📌 **Uwaga:** Aplikacja frontendowa AI Task App obecnie nie posiada osobnego folderu `routing/`. Logika tras znajduje się bezpośrednio w pliku `App.jsx`. Ten dokument opisuje aktualny stan konfiguracji routingu oraz przedstawia propozycję struktury docelowej (planowanej), która może zostać wdrożona w przyszłości dla lepszej separacji odpowiedzialności.

---

## 📍 Aktualna konfiguracja (plik `App.jsx`)

Routing w aplikacji opiera się na `react-router-dom`. Główna konfiguracja znajduje się w pliku `App.jsx`, gdzie zadeklarowane są wszystkie trasy.

### ✨ Struktura tras:

```jsx
<Routes>
  <Route path="/" element={<WelcomePage />} />
  <Route path="/login" element={<LoginPage />} />
  <Route path="/register" element={<RegisterPage />} />
  <Route
    path="/tasks"
    element={
      <ProtectedRoute>
        <TasksPage />
      </ProtectedRoute>
    }
  />
</Routes>
```

### 🔐 Ochrona tras:

- Trasa `/tasks` jest zabezpieczona przez komponent `ProtectedRoute`
- Sprawdza `isAuthenticated` z `AuthContext`
- Przekierowuje niezalogowanego użytkownika do `/login`

---

## ✅ Dostępne ścieżki i ich funkcje

| Ścieżka      | Komponent      | Dostęp                 | Opis                                     |
| ------------ | -------------- | ---------------------- | ---------------------------------------- |
| `/`          | `WelcomePage`  | publiczny              | Ekran powitalny, linki do login/register |
| `/login`     | `LoginPage`    | tylko dla gości        | Logowanie użytkownika                    |
| `/register`  | `RegisterPage` | tylko dla gości        | Rejestracja nowego użytkownika           |
| `/tasks`     | `TasksPage`    | tylko dla zalogowanych | Lista zadań użytkownika                  |
| `/tasks/new` | `TaskFormPage` | (planowane)            | Tworzenie nowego zadania z pomocą AI     |

---

## 🔁 Zachowanie po zalogowaniu i wylogowaniu

- Po zalogowaniu → przekierowanie do `/tasks`
- Po `logout()` w `Header` → przekierowanie do `/`

---

## 🛡️ Ochrona tras – `ProtectedRoute`

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

- Komponent `ProtectedRoute` chroni widoki przed dostępem osób nieautoryzowanych
- Jeśli `AuthContext` zwraca `isLoading`, komponent tymczasowo nie renderuje nic (`null`)
- Jeśli `!isAuthenticated`, użytkownik jest przekierowywany do `/login`

---

## 💡 Planowana struktura (propozycja)

Dla większej skalowalności projektu warto przenieść routing do osobnego katalogu.

### 📁 Propozycja struktury:

```
src/
├── routing/
│   ├── routes.js         # Wszystkie trasy z przypisanymi komponentami
│   ├── GuestRoutes.jsx   # Trasy publiczne (np. login, register)
│   ├── ProtectedRoutes.jsx # Trasy prywatne (np. tasks, dashboard)
│   └── RouteConfig.jsx   # Główna konfiguracja routera
```

### 🧩 Korzyści z takiego podziału:

- lepsza czytelność,
- łatwiejsze dodawanie tras i layoutów,
- separacja odpowiedzialności (`auth`, `guest`, `admin`),
- gotowość do rozbudowy aplikacji (np. `/admin`, `/profile`, `/settings`).

---

## 🧭 Podsumowanie

- Routing aplikacji oparty jest na `react-router-dom`
- Konfiguracja znajduje się w `App.jsx`
- Trasy są bezpośrednio powiązane z komponentami z `pages/`
- Zabezpieczone trasy (`/tasks`) wymagają `ProtectedRoute`
- Przyszłościowo warto wydzielić routing do osobnego katalogu i dodać lazy-loading

---

## 📄 Dokumentacja powiązana

- `pages.md` – komponenty przypisane do poszczególnych tras
- `context.md` – `useAuth()` wykorzystywany w `ProtectedRoute`
- `components.md` – `ProtectedRoute.jsx`
- `src.md` – ogólna struktura frontendowego katalogu
