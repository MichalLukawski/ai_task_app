# 📄 Dokumentacja – `pages/` (Frontend AI Task App)

Folder `src/pages/` zawiera główne widoki (strony) aplikacji. Każdy plik odpowiada jednemu z komponentów React reprezentujących trasę w aplikacji (zgodnie z konfiguracją `react-router-dom`). Widoki te są odpowiedzialne za obsługę kluczowych funkcjonalności: autoryzacji, prezentacji danych zadań oraz tworzenia ich z pomocą AI.

---

## 📦 Struktura katalogu `pages/`

```
pages/
├── WelcomePage.jsx       # Ekran powitalny aplikacji
├── LoginPage.jsx         # Formularz logowania
├── RegisterPage.jsx      # Formularz rejestracji
├── DashboardPage.jsx     # Panel użytkownika z zadaniami (obecnie aktywny widok)
```

---

## 🧭 Zasady ogólne

- Każda strona jest komponentem funkcyjnym (`function ComponentName`)
- Widoki są montowane przez `App.jsx` na podstawie trasy (`react-router-dom`)
- Stylizacja: wyłącznie z użyciem TailwindCSS (`className`)
- Komponenty z `pages/` nie implementują niskopoziomowej logiki – korzystają z komponentów z `components/`, hooków oraz kontekstu

---

## 🌐 WelcomePage.jsx

### 📌 Opis

- Trasa: `/`
- Ogólny ekran startowy, dostępny bez logowania
- Zawiera nazwę aplikacji, opis systemu oraz odnośniki do logowania i rejestracji

### 📌 Funkcje

- Wyświetla zawartość statyczną + linki `Login`, `Register`
- Komponent informacyjny, nie korzysta z API

---

## 🔐 LoginPage.jsx

### 📌 Opis

- Trasa: `/login`
- Formularz logowania użytkownika

### 📌 Logika

- Pola: `email`, `password`
- Wysyła `POST /api/auth/login` z danymi logowania
- Obsługuje:
  - zapis tokena do `localStorage`
  - aktualizację kontekstu `AuthContext` poprzez `login(token)`
  - przekierowanie do `/dashboard` po sukcesie

---

## 🧾 RegisterPage.jsx

### 📌 Opis

- Trasa: `/register`
- Formularz rejestracji nowego użytkownika

### 📌 Logika

- Pola: `email`, `password`, `confirmPassword`
- Wysyła `POST /api/auth/register`
- Wyświetla komunikat o rejestracji
- Obecnie nie następuje automatyczne logowanie po rejestracji

---

## 📂 DashboardPage.jsx (dawniej TasksPage)

### 📌 Opis

- Trasa: `/dashboard`
- Główny widok użytkownika po zalogowaniu
- Zabezpieczony przez `ProtectedRoute`

### 📌 Funkcjonalność

- Pobiera listę zadań użytkownika z API (`GET /api/tasks`)
- Renderuje listę `TaskCard` (poprzez `TaskList`)
- Pozwala na:
  - dodawanie nowego zadania z pomocą AI (`CreateTaskForm`)
  - przegląd, edycję i zamykanie zadań (`TaskCardView` / `TaskCardEdit`)
  - podgląd poziomu trudności (`DifficultyStars`) i terminu (`DueDateProgress`)

### 📌 Stylizacja

- Layout oparty o Tailwind (`flex`, `gap-4`, `w-full`, `max-w-screen-md`)
- Responsywność: mobilna i desktopowa

---

## 🧩 Relacje między widokami i komponentami

| Strona        | Komponenty używane                                 | API                          | Uwagi                                               |
| ------------- | -------------------------------------------------- | ---------------------------- | --------------------------------------------------- |
| WelcomePage   | Header                                             | brak                         | Strona ogólnodostępna                               |
| LoginPage     | Header, useAuth                                    | `/auth/login`                | Ustawienie tokena i redirect                        |
| RegisterPage  | Header                                             | `/auth/register`             | Brak automatycznego logowania po rejestracji        |
| DashboardPage | ProtectedRoute, TaskList, TaskCard, CreateTaskForm | `/tasks`, `/tasks/ai-create` | Główna funkcjonalność użytkownika (CRUD zadań + AI) |

---

## 🧩 Uwagi i rozbieżności

- Nazwa `TasksPage.jsx` została zmieniona na `DashboardPage.jsx` – aktualna konwencja pliku i trasy to `/dashboard`
- Brak osobnej strony `TaskFormPage.jsx` – funkcja tworzenia zadania z AI została zintegrowana z `DashboardPage` (formularz `CreateTaskForm` jako część widoku)

---

## 📄 Dokumentacja powiązana

- `routing.md` – konfiguracja tras (`/`, `/login`, `/dashboard`)
- `components.md` – opis `TaskCard`, `CreateTaskForm`, `DifficultyStars`
- `task_flow.md` – szczegółowy przebieg tworzenia i obsługi zadania
- `context.md` – logika uwierzytelniania (`useAuth`)
