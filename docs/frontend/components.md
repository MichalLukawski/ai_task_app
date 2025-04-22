# 🧩 Dokumentacja – `components/` (Frontend AI Task App)

Plik ten dokumentuje zawartość katalogu `src/components/` aplikacji frontendowej AI Task App. Zawiera szczegółowe opisy komponentów wielokrotnego użytku, ich funkcji, zależności i zachowania. Komponenty te pełnią kluczową rolę w budowie interfejsu użytkownika i wspierają zarządzanie zadaniami, autoryzację, nawigację oraz responsywność.

---

## 🧱 Rola komponentów

W projekcie AI Task App komponenty w `src/components/` są **deklaratywne, atomowe i wielokrotnego użytku**. Ich zadaniem jest:

- budowanie interfejsu panelu użytkownika (lista zadań, karty, formularze),
- obsługa przepływów AI (tworzenie/zamykanie zadań),
- zapewnienie bezpieczeństwa dostępu (`ProtectedRoute`),
- organizacja nawigacji (`Header`),
- prezentacja danych (`DifficultyStars`, `DueDateProgress`),
- obsługa edycji, zapisów i synchronizacji danych z backendem,
- zapewnienie komunikacji zwrotnej i UX w komponencie `TaskCard`.

---

## 📦 Zawartość folderu `components/`

```
components/
├── CreateTaskForm.jsx
├── Header.jsx
├── ProtectedRoute.jsx
├── TaskList.jsx
├── TaskCard/
    ├── DifficultySelector.jsx
    ├── DifficultyStars.jsx
    ├── DueDateEditor.jsx
    ├── DueDateProgress.jsx
    ├── TaskCard.jsx
    ├── TaskCardEdit.jsx
    ├── TaskCardView.jsx
```

---

## 🧠 Komponenty zadaniowe

### 🔸 TaskList.jsx

**Opis:** Renderuje listę zadań przekazanych jako `props`. Deleguje renderowanie poszczególnych kart do komponentu `TaskCard`. Obsługuje też stan pustej listy.

**Zależności:** `TaskCard`, `map()` z Reacta

---

### 🔸 TaskCard.jsx

**Opis:** Główny komponent reprezentujący pojedyncze zadanie. Odpowiada za zarządzanie stanem fokusowania, widoczności, edycji oraz synchronizacji z backendem. Renderuje wewnętrznie `TaskCardView`. Obsługuje kliknięcia, klawisz Enter, blur oraz przechwytywanie interakcji z polami edycyjnymi.

Wspiera mechanizm `isFocused`, `isEditingDueDate`, `isEditingDifficulty`, `isSaving`, `showSaved`. Współdziała z `useTaskCardState()` oraz `onTaskUpdated()` przekazanym z `DashboardPage`.

**Zależności:** `TaskCardView`, `useTaskCardState()`, `ref`, `keydown`, `editable-field`

---

### 🔸 TaskCardView.jsx

**Opis:** Widok zadania w trybie pasywnym oraz edycyjnym. Pokazuje tytuł, poziom trudności (`DifficultyStars`), termin (`DueDateProgress`), opis, podobne zadania i przyciski akcji. Obsługuje kliknięcia w pola edytowalne, które w zależności od stanu zmieniają się na `input date` (`DueDateEditor`) lub `select` (`DifficultySelector`).

Rozróżnia pola klikane od kliknięcia w kartę dzięki klasie `editable-field`.

**Zależności:** `DifficultyStars`, `DueDateProgress`, `DueDateEditor`, `DifficultySelector`, `useRef`, `useEffect`

---

### 🔸 TaskCardEdit.jsx

**Status:** Nieużywany po refaktoryzacji. Wcześniej odpowiadał za oddzielny widok edycji. Obecnie edycja została zintegrowana bezpośrednio z `TaskCardView`.

**Zalecenie:** Można usunąć ten komponent jako nieużywany.

---

### 🔸 CreateTaskForm.jsx

**Opis:** Formularz tworzenia nowego zadania na podstawie opisu tekstowego użytkownika. Wysyła żądanie `POST /api/tasks/ai-create` przez Axios. Oczekuje odpowiedzi AI z tytułem, poziomem trudności i terminem.

**Zależności:** `axios`, `useAuth`, `useState`, `useEffect`

---

## 💠 Komponenty pomocnicze

### 🔸 DifficultyStars.jsx

**Opis:** Wizualna prezentacja trudności zadania w postaci gwiazdek (1–5). W trybie edycji umożliwia wybór przez użytkownika. Obsługuje `onChange`, `onBlur`, `onKeyDown` (Enter) oraz zapis do backendu przez `saveDifficulty`.

**Zależności:** `classnames`, `useState`, `props.level`, `editable-field`

---

### 🔸 DifficultySelector.jsx

**Opis:** Komponent `select` umożliwiający wybór poziomu trudności (1–5). Obsługuje `blur`, `onChange`, `onKeyDown` (Enter). Zapisuje nową wartość przez `saveDifficulty()` i aktualizuje lokalny stan.

**Zależności:** `useEffect`, `useState`, `editable-field`

---

### 🔸 DueDateProgress.jsx

**Opis:** Pasek postępu czasu od utworzenia zadania do terminu (`dueDate`). Kolor paska zależny od stopnia zaawansowania (%). Komponent tylko do odczytu.

**Zależności:** `dateUtils`, `tailwind`, `progress bar UI`

---

### 🔸 DueDateEditor.jsx

**Opis:** Komponent edytora daty – pole typu `date`, umożliwia wybór lub modyfikację terminu. Obsługuje `blur`, `onChange`, `onKeyDown`, `min=today`. Zapisuje dane przez `saveDueDate()`.

**Zależności:** `useState`, `editable-field`, `tailwind`, `input[type=date]`

---

## 🔐 Komponenty ogólne

### 🔸 Header.jsx

**Opis:** Nagłówek aplikacji. Renderuje dynamicznie przyciski zależnie od stanu logowania (`Login`, `Register`, `Dashboard`, `Logout`).

**Zależności:** `useAuth`, `useNavigate`, `Link`

---

### 🔸 ProtectedRoute.jsx

**Opis:** Wrapper tras wymagających logowania. Przekierowuje niezalogowanych użytkowników do `/login`, w przeciwnym razie renderuje `children`.

**Zależności:** `useAuth`, `Navigate`

---

## 🧩 Styl i TailwindCSS

- Wszystkie komponenty korzystają z Tailwind (`bg-`, `text-`, `rounded-`, `flex`, `hover-`, `shadow-`, `transition-` itd.)
- Responsywność wspierana klasami `sm:`, `md:`, `lg:`
- Komponenty posiadają spójną strukturę wizualną
- Brak użycia styled-components lub globalnych CSS
- Klasa `editable-field` oznacza pola wewnątrz kart, które nie powinny zamykać karty przy kliknięciu

---

## 📄 Dokumentacja powiązana

- `src.md` – struktura katalogu frontendowego
- `pages.md` – widoki z wykorzystaniem komponentów
- `task_flow.md` – przepływ tworzenia i zarządzania zadaniami
- `hooks.md` – logika `useTaskCardState()`
- `TaskCard.md` – dokumentacja szczegółowa komponentu `TaskCard`
- `api-integration.md` – integracja komunikacji frontend-backend

---

## 🧠 Nowe i zaktualizowane komponenty (2025-04)

### 🔹 CloseWithAiBox.jsx

**Opis:**  
Wyświetla pole tekstowe i przyciski `Zapisz` / `Anuluj` umożliwiające wprowadzenie podsumowania AI. Obsługuje tymczasowe przechowywanie treści i reaguje na błędy walidacji AI.

**Zależności:** `useTaskCardState`, `textarea`, `props.value`, `props.onSave`, `props.error`, `props.isSaving`

---

### 🔹 AiSummaryRejectedModal.jsx

**Opis:**  
Modal prezentowany, gdy AI odrzuci wprowadzone podsumowanie (`code === 'AI_REJECTED'`). Użytkownik może zaakceptować mimo to (`onConfirm`) lub wrócić do edycji (`onCancel`).

**Zależności:** `Dialog`, `props.onConfirm`, `props.onCancel`

---

### 🔹 TaskCardSummary.jsx

**Opis:**  
Odpowiedzialny za prezentację podsumowania (`summary`) oraz daty zamknięcia (`closedAt`) dla zadania o statusie `closed`.

**Zależności:** `formatDate`, `props.summary`, `props.closedAt`

---

### 🔹 SimilarTasksPopup.jsx

**Opis:**  
Dymek rozwijany z poziomu karty zadania, który prezentuje zadania z `similarTasks[]`. Pokazuje tytuł, opis, podsumowanie oraz daty utworzenia i zamknięcia. Wspiera przewijanie i zamykanie.

**Zależności:** `formatDate`, `props.tasks`, `props.onClose`, `tailwind scroll`, `overflow-auto`

---

## 📌 Dodatkowe zależności i standardy

- Komponenty współdziałają z hookiem `useTaskCardState`
- Komponenty AI oraz podobnych zadań są renderowane warunkowo w `TaskCardView`
- Wszystkie komponenty respektują styl `editable-field` (wyjątek z zamykania przy kliknięciu)
- Wprowadzono logiczne separacje komponentów zgodnie z zasadą Single Responsibility
- Zredukowano wcześniejsze komponenty zbiorcze (`TaskCardEdit`) na rzecz bardziej granularnych

---

## 📦 Rekomendacje zarządzania komponentami

- Rozważyć dalszą separację komponentów stanu i widoku
- Wprowadzić testy jednostkowe dla komponentów modalnych (`AiSummaryRejectedModal`)
- Wprowadzić dynamiczne importy (code splitting) dla komponentów dymków/modalnych

---
