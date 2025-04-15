# 🧩 Dokumentacja – Komponenty modułu zadań (Frontend AI Task App)

Dokument ten opisuje szczegółowo komponenty frontendowe odpowiedzialne za tworzenie, prezentację, edycję i zarządzanie zadaniami w aplikacji AI Task App. Przedstawia ich rolę, sposób interakcji oraz zależności między nimi. Stanowi rozwinięcie `components.md` i uzupełnienie dokumentacji funkcjonalnej `task_flow.md`.

---

## 🗂️ Główna struktura modułu zadań

```
components/
├── CreateTaskForm.jsx
├── TaskList.jsx
├── TaskCard.jsx
├── TaskCardView.jsx
├── TaskCardEdit.jsx
├── DifficultyStars.jsx
├── DueDateProgress.jsx
└── DueDateEditor.jsx
```

---

## ✍️ Komponent: `CreateTaskForm.jsx`

### Rola:
- Pozwala użytkownikowi opisać zadanie w języku naturalnym.
- Wysyła dane do API (`/tasks/ai-create`) i tworzy nowe zadanie.
- Po udanym utworzeniu dodaje nowe zadanie do listy.

### Zależności:
- `axios`
- `useState`, `useEffect`
- Obsługuje błędy, loading, walidację długości opisu

---

## 📋 Komponent: `TaskList.jsx`

### Rola:
- Renderuje listę zadań.
- Dla każdego zadania renderuje osobny `TaskCard`.

### Interfejs:
```jsx
<TaskList tasks={tasks} setTasks={setTasks} />
```

---

## 🔳 Komponent: `TaskCard.jsx`

### Rola:
- Główny kontener dla pojedynczego zadania.
- Renderuje `TaskCardView` lub `TaskCardEdit` w zależności od stanu.
- Używa hooka `useTaskCardState`.

### Zależności:
- `useTaskCardState`
- `TaskCardView`, `TaskCardEdit`

---

## 👁️ Komponent: `TaskCardView.jsx`

### Rola:
- Widok podglądu zadania.
- Prezentuje dane: tytuł, opis, trudność (`DifficultyStars`), termin (`DueDateProgress`)
- Udostępnia przyciski: `Edytuj`, `Zakończ`, `Zakończ z AI`

### Zależności:
- `DifficultyStars`, `DueDateProgress`
- `useTaskCardState`

---

## ✏️ Komponent: `TaskCardEdit.jsx`

### Rola:
- Widok edycji zadania.
- Pozwala edytować termin (`DueDateEditor`) i poziom trudności.
- Zawiera przyciski `Zapisz`, `Anuluj`.

### Interfejs:
```jsx
<TaskCardEdit
  editedTask={...}
  handleFieldChange={...}
  handleSaveEdit={...}
  handleCancelEdit={...}
/>
```

---

## ⭐ Komponent: `DifficultyStars.jsx`

### Rola:
- Prezentuje i (opcjonalnie) umożliwia wybór trudności (1–5).
- W trybie podglądu renderuje gwiazdki pasywne.
- W trybie edycji – gwiazdki klikalne.

### Logika:
- wewnętrzny `useState` dla tymczasowej selekcji
- `onClick` do ustawienia nowej wartości

---

## 📆 Komponent: `DueDateEditor.jsx`

### Rola:
- Umożliwia wybór daty zakończenia zadania (format `YYYY-MM-DD`)
- Waliduje wartość (nie może być w przeszłości)

### Użycie:
- We `TaskCardEdit` jako pole wejściowe (`<input type="date" />`)

---

## 📊 Komponent: `DueDateProgress.jsx`

### Rola:
- Pokazuje pasek postępu między datą utworzenia (`createdAt`) a terminem (`dueDate`)
- Kolor paska dynamicznie się zmienia – od zielonego do czerwonego
- Oblicza % upływu czasu za pomocą `calculateDueDatePercentage()`

### Zależności:
- `dateUtils.js`

---

## 🔗 Powiązania między komponentami

| Komponent         | Zawiera / Używa               | Występuje w                    |
|-------------------|-------------------------------|--------------------------------|
| TaskCard          | TaskCardView / TaskCardEdit   | TaskList                       |
| TaskCardView      | DifficultyStars, DueDateProgress | TaskCard                    |
| TaskCardEdit      | DifficultyStars, DueDateEditor | TaskCard                     |
| DueDateProgress   | dateUtils                     | TaskCardView                  |
| CreateTaskForm    | axios, useState               | DashboardPage                 |

---

## 📄 Dokumentacja powiązana

- `components.md` – ogólna dokumentacja komponentów frontendowych
- `hooks.md` – `useTaskCardState` i zarządzanie stanem edycji
- `task_flow.md` – logika funkcjonalna zarządzania zadaniami
- `utils.md` – obliczenia dat (`dateUtils`)