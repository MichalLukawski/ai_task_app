# 🧩 Dokumentacja – `components/` (Frontend AI Task App)

Plik ten dokumentuje zawartość katalogu `src/components/` aplikacji frontendowej AI Task App. Zawiera szczegółowe opisy komponentów wielokrotnego użytku, ich funkcji, zależności i zachowania. Komponenty te pełnią kluczową rolę w budowie interfejsu użytkownika i wspierają mechanizmy autoryzacji, nawigacji oraz responsywności.

---

## 🧱 Rola komponentów

W projekcie AI Task App komponenty w `src/components/` są **deklaratywne, atomowe i wielokrotnego użytku**. Ich zadaniem jest:

- oddzielenie widoków od logiki nawigacji (np. `Header`),
- zapewnienie bezpieczeństwa dostępu (`ProtectedRoute`),
- standaryzacja interfejsu użytkownika (layout, responsywność).

---

## 📦 Zawartość folderu `components/`

```
components/
├── Header.jsx           # Główny nagłówek aplikacji (zawsze widoczny)
├── ProtectedRoute.jsx   # Zabezpieczenie tras wymagających logowania
```

---

## 📘 Header.jsx

### 🔹 Opis

Komponent `Header` jest zawsze widoczny – niezależnie od bieżącej trasy. Pełni funkcję głównej nawigacji w aplikacji. Jego zawartość jest **dynamiczna**, zależna od stanu logowania (`isAuthenticated` z `AuthContext`).

### 🔹 Funkcje

- Po lewej stronie wyświetla nazwę aplikacji (`AI Task App`) jako link do `/`
- Po prawej stronie dynamicznie renderuje:
  - ✅ Linki `Login` / `Register` (gdy użytkownik niezalogowany)
  - ✅ Link `Dashboard` (`/tasks`) i `Logout` (gdy użytkownik jest zalogowany)

### 🔹 Zależności

- `useAuth()` – z `AuthContext`, by sprawdzić stan użytkownika
- `useNavigate()` – przekierowanie po `Logout`

### 🔹 Responsywność

- Klasy Tailwind: `flex`, `justify-between`, `space-x-4`
- Mobilne i desktopowe widoki wspierane przez utility klasy (`sm:`, `md:`)

---

## 🔒 ProtectedRoute.jsx

### 🔹 Opis

Komponent `ProtectedRoute` zabezpiecza trasy przed dostępem użytkowników niezalogowanych. Używany jako wrapper dla tras w `react-router-dom`.

### 🔹 Działanie

- Sprawdza `isAuthenticated` z `useAuth()`
- Gdy `isLoading === true` → renderuje `null` (czekamy na odczyt tokena z localStorage)
- Gdy użytkownik nie jest zalogowany → `Navigate to="/login"`
- Gdy jest zalogowany → renderuje dzieci (`children`)

### 🔹 Przykład użycia:

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

### 🔹 Zależności

- `useAuth()` z `AuthContext`
- `Navigate` z `react-router-dom`

---

## 🧠 Dobór komponentów

Komponenty te są:

- **niezależne od widoków (`pages/`)** – mogą być używane wielokrotnie,
- **połączone z kontekstem (`AuthContext`)** – reagują na zmiany stanu globalnego,
- **czyste i deklaratywne** – nie zawierają zagnieżdżonej logiki API, tylko UI.

---

## 🧩 Planowane komponenty

| Komponent      | Opis                                                           |
| -------------- | -------------------------------------------------------------- |
| `TaskCard.jsx` | Karta pojedynczego zadania z danymi (tytuł, status, przyciski) |
| `TaskForm.jsx` | Komponent formularza tworzenia zadania z pomocą AI             |
| `Alert.jsx`    | Komponent komunikatów systemowych (sukces/błąd)                |
| `Loader.jsx`   | Komponent spinnera w trakcie ładowania danych lub AI           |

---

## 🎨 Styl i zgodność z TailwindCSS

- Stylowanie **wyłącznie przez TailwindCSS**
- Brak klas globalnych, brak osobnych arkuszy CSS
- Komponenty posiadają klasy utility (np. `text-blue-600`, `bg-gray-50`, `rounded-md`)
- Wspierają tryb mobilny (`sm:`, `md:`)

---

## 📄 Dokumentacja powiązana

- `src.md` – struktura katalogu głównego
- `context.md` – działanie `AuthContext`, od którego zależy Header i ProtectedRoute
- `pages.md` – widoki, w których komponenty są używane
- `routing.md` – konfiguracja tras z `ProtectedRoute`
