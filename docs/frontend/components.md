# 🧩 Dokumentacja – `components/` (Frontend AI Task App)

Plik ten dokumentuje zawartość katalogu `src/components/` aplikacji frontendowej AI Task App. Zawiera szczegółowe opisy komponentów wielokrotnego użytku, ich funkcji, zależności i zachowania. Komponenty te pełnią kluczową rolę w budowie interfejsu użytkownika i wspierają zarządzanie zadaniami, autoryzację, nawigację oraz responsywność.

---

## 🧱 Rola komponentów

W projekcie AI Task App komponenty w `src/components/` są **deklaratywne, atomowe i wielokrotnego użytku**. Ich zadaniem jest:

- budowanie interfejsu panelu użytkownika (lista zadań, karty, formularze),
- obsługa przepływów AI (tworzenie/zamykanie zadań),
- zapewnienie bezpieczeństwa dostępu (`ProtectedRoute`),
- organizacja nawigacji (`Header`),
- prezentacja danych (`DifficultyStars`, `DueDateProgress`).

---

## 📦 Zawartość folderu `components/`

```
components/
├── CreateTaskForm.jsx
├── DifficultyStars.jsx
├── DueDateEditor.jsx
├── DueDateProgress.jsx
├── Header.jsx
├── ProtectedRoute.jsx
├── TaskCard.jsx
├── TaskCardEdit.jsx
├── TaskCardView.jsx
├── TaskList.jsx
```

---

## 🧠 Komponenty zadaniowe

### 🔸 TaskList.jsx

**Opis:** Renderuje listę zadań przekazanych jako `props`. Deleguje renderowanie poszczególnych kart do komponentu `TaskCard`. Obsługuje też stan pustej listy.

**Zależności:** `TaskCard`, `map()` z Reacta

---

### 🔸 TaskCard.jsx

**Opis:** Główny komponent reprezentujący pojedyncze zadanie. Renderuje wewnętrznie `TaskCardView` (podgląd) lub `TaskCardEdit` (edycja), w zależności od stanu. Zarządza przełączaniem trybu przy użyciu hooka `useTaskCardState()`.

**Zależności:** `TaskCardView`, `TaskCardEdit`, `useTaskCardState()`

---

### 🔸 TaskCardView.jsx

**Opis:** Widok pasywny zadania. Pokazuje tytuł, poziom trudności (`DifficultyStars`), termin (`DueDateProgress`), opis i akcje (`Edytuj`, `Zamknij`).

**Zależności:** `DifficultyStars`, `DueDateProgress`, `useTaskCardState()`

---

### 🔸 TaskCardEdit.jsx

**Opis:** Widok edycji zadania. Umożliwia modyfikację terminu (`DueDateEditor`) i poziomu trudności (select). Weryfikuje poprawność danych i pozwala anulować lub zatwierdzić zmiany.

**Zależności:** `DueDateEditor`, `useTaskCardState()`

---

### 🔸 CreateTaskForm.jsx

**Opis:** Formularz tworzenia nowego zadania na podstawie opisu tekstowego użytkownika. Wysyła żądanie `POST /api/tasks/ai-create` przez Axios. Oczekuje odpowiedzi AI z tytułem, poziomem trudności i terminem.

**Zależności:** `axios`, `useAuth`, `useState`, `useEffect`

---

## 🛠️ Komponenty pomocnicze

### 🔸 DifficultyStars.jsx

**Opis:** Wizualna prezentacja trudności zadania w postaci gwiazdek (1–5). W trybie edycji umożliwia wybór przez użytkownika.

**Zależności:** `classnames`, `useState`

---

### 🔸 DueDateProgress.jsx

**Opis:** Pasek postępu czasu od utworzenia zadania do terminu (`dueDate`). Kolor paska zależny od stopnia zaawansowania (%). Wykorzystuje funkcje z `utils/dateUtils.js`.

**Zależności:** `dateUtils`, `tailwind`, `progress bar UI`

---

### 🔸 DueDateEditor.jsx

**Opis:** Komponent edytora daty – pole typu `date`, umożliwia wybór lub modyfikację terminu. Waliduje poprawność daty.

**Zależności:** `useState`, `onChange`, `min=dzisiaj`

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

- Wszystkie komponenty korzystają z Tailwind (`bg-`, `text-`, `rounded-`, `flex`...)
- Responsywność wspierana klasami `sm:`, `md:`
- Brak globalnych CSS lub styled-components

---

## 📄 Dokumentacja powiązana

- `src.md` – struktura katalogu frontendowego
- `pages.md` – widoki z wykorzystaniem komponentów
- `task_flow.md` – przepływ tworzenia i zarządzania zadaniami
- `hooks.md` – logika `useTaskCardState()`
