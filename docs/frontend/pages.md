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
- Logika biznesowa znajduje się w hookach (`useTaskCardState`) lub backendzie

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
  - przegląd, edycję i zamykanie zadań (`TaskCardView`)
  - aktualizację terminów i trudności (`DueDateEditor`, `DifficultySelector`)
  - wizualizację stanu (`DueDateProgress`, `DifficultyStars`)
- Obsługuje aktualizację lokalnej listy zadań poprzez `onTaskUpdated(task)`
- Zarządza fokusowaniem kart (`focusedCardId`) – tylko jedna karta może być aktywna w danym czasie

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
- Brak osobnej strony `TaskFormPage.jsx` – funkcja tworzenia zadania z AI została zintegrowana z `DashboardPage`
- Fokusowanie i zamykanie kart jest teraz obsługiwane w pełni przez `DashboardPage`, nie przez same komponenty kart

---

## 📄 Dokumentacja powiązana

- `routing.md` – konfiguracja tras (`/`, `/login`, `/dashboard`)
- `components.md` – opis `TaskCard`, `CreateTaskForm`, `DifficultyStars`
- `task_flow.md` – szczegółowy przebieg tworzenia i obsługi zadania
- `context.md` – logika uwierzytelniania (`useAuth`)
- `hooks.md` – zarządzanie stanem edycji zadań

---

## 🔄 Aktualizacje funkcjonalne – DashboardPage (2025-04)

### ✅ Zakładki: Your Tasks / Closed Tasks

DashboardPage posiada teraz dwa tryby widoku zadań:

- **Your Tasks** – zawiera zadania o `status !== 'closed'`
- **Closed Tasks** – pokazuje tylko zadania zamknięte (`status === 'closed'`)
- Domyślnie aktywna zakładka to `Your Tasks`, przełączana przyciskiem UI

### ✅ Sortowanie zadań

- Zadania otwarte (`Your Tasks`) są sortowane priorytetowo:
  - Najpierw po `dueDate` rosnąco (jeśli istnieje)
  - Następnie po `createdAt` malejąco
- Zadania zamknięte są sortowane po `closedAt` malejąco (najświeższe na górze)

### ✅ Obsługa aktualizacji i usuwania zadań

- `handleTaskUpdated(updatedTask)` – aktualizuje zadanie w stanie `tasks`
- `handleTaskDeleted(taskId)` – usuwa zadanie z listy lokalnie po `DELETE`

### ✅ Obsługa podsumowania AI

- Każda karta zadania (`TaskCard`) może być zamknięta z użyciem AI
- Podsumowanie jest oceniane i przetwarzane przez backend (GPT)
- W przypadku odrzucenia użytkownik ma opcję „Zapisz mimo to”

### ✅ Przegląd podobnych zadań

- `TaskCard` może wyświetlać przycisk 🧠 „Podobne (X)”
- Kliknięcie rozwija `SimilarTasksPopup`, który wyświetla listę `title`, `description`, `summary`, `createdAt`, `closedAt` każdego powiązanego zadania

### ✅ Usuwanie zadań

- Każde zadanie (otwarte lub zamknięte) może zostać usunięte permanentnie
- Wymaga potwierdzenia `confirm(...)`
- Wywołuje `DELETE /tasks/:id` i usuwa zadanie lokalnie (`onTaskDeleted`)

---

## ⚙️ Dodatkowe informacje techniczne

- Widok korzysta z hooka `useApi()` do komunikacji z backendem
- Funkcja `fetchTasks()` jest wykonywana przy `useEffect` on mount
- Komponenty `TaskCard` renderowane są w pętli na podstawie `sortedOpenTasks` lub `sortedClosedTasks`
- `CreateTaskForm` znajduje się zawsze po lewej stronie (kolumna `md:col-span-1`)
- Komponenty są rozmieszczone w układzie siatki (`grid grid-cols-1 md:grid-cols-4`)

---

## 🆕 Komponenty używane przez DashboardPage (po rozszerzeniu)

| Komponent                | Rola                                                       |
| ------------------------ | ---------------------------------------------------------- |
| `TaskCard`               | Główna karta zadania                                       |
| `CloseWithAiBox`         | UI do wprowadzenia podsumowania                            |
| `AiSummaryRejectedModal` | Modal z pytaniem o zaakceptowanie odrzuconego podsumowania |
| `SimilarTasksPopup`      | Panel z listą podobnych zadań                              |
| `TaskCardSummary`        | Sekcja podsumowania i daty zamknięcia                      |

---

## 🧭 Powiązane pliki

- `TaskCard.jsx` – logika stanu, delegowanie do `TaskCardView`
- `TaskCardView.jsx` – UI przycisków, dymków, modali
- `hooks/useTaskCardState.js` – zapis, AI, usuwanie, synchronizacja
- `api/tasks/:id` (GET, PATCH, DELETE) – obsługiwane przez DashboardPage

---
