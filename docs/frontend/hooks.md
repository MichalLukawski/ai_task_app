# 🪝 Dokumentacja – `hooks/` (Frontend AI Task App)

Ten dokument opisuje niestandardowe hooki Reacta (`custom hooks`) utworzone w projekcie AI Task App. Hooki te służą do zarządzania stanem komponentów, obsługi logiki interakcji użytkownika oraz zapewnienia czytelnej separacji odpowiedzialności w kodzie. Zostały zaprojektowane tak, by zwiększyć spójność, reużywalność i łatwość testowania logiki aplikacji.

---

## 📦 Lokalizacja i struktura

```
src/
└── hooks/
    └── useTaskCardState.js
```

Folder `hooks/` przechowuje funkcje Reactowe rozpoczynające się od `use`, zgodnie z konwencją hooków. Każdy hook zawiera własną, enkapsulowaną logikę i może być importowany przez komponenty.

---

## 🧠 Hook: `useTaskCardState.js`

### 📌 Opis

Hook `useTaskCardState()` odpowiada za zarządzanie **lokalnym stanem pojedynczej karty zadania** (`TaskCard`). Umożliwia aktualizację pól edytowalnych, takich jak `dueDate` oraz `difficulty`, synchronizację z backendem, oraz zapewnia interfejs do komunikacji zwrotnej z komponentem nadrzędnym (`DashboardPage`). Hook został zaprojektowany w sposób reaktywny i odporny na błędy – obsługuje zarówno zapis, jak i synchronizację danych po aktualizacji.

Hook zarządza wewnętrznym stanem edytowanego zadania (`editedTask`), kontroluje proces zapisu (`saveDueDate`, `saveDifficulty`) oraz uruchamia refetch danych po stronie backendu. Obsługuje również logikę komunikatów „Zapisuję...” i „Zapisano ✔”.

### 📄 API hooka

```js
const {
  dueDate,
  difficulty,
  setDueDate,
  setDifficulty,
  saveDueDate,
  saveDifficulty,
  isSaving,
  showSaved,
  task,
} = useTaskCardState(initialTask, onTaskUpdated);
```

| Zmienna / Funkcja    | Typ        | Opis                                                         |
| -------------------- | ---------- | ------------------------------------------------------------ |
| `dueDate`            | `string`   | Lokalna wartość daty zakończenia (w formacie `YYYY-MM-DD`)   |
| `difficulty`         | `number`   | Lokalna wartość trudności zadania (1–5)                      |
| `setDueDate(val)`    | `function` | Ustawia nową datę (przed zapisem)                            |
| `setDifficulty(val)` | `function` | Ustawia nową trudność (przed zapisem)                        |
| `saveDueDate()`      | `function` | Wysyła PATCH i GET, aktualizuje zadanie, ustawia `showSaved` |
| `saveDifficulty()`   | `function` | Analogicznie jak wyżej                                       |
| `isSaving`           | `boolean`  | Czy trwa zapis (pokazuje animację „Zapisuję...”)             |
| `showSaved`          | `boolean`  | Czy pokazać informację „✔ Zapisano”                          |
| `task`               | `object`   | Najnowsza wersja zadania po aktualizacji z backendu          |

### 🔁 Dodatkowe szczegóły

- Po `PATCH` do backendu, automatycznie wywoływany jest `GET /tasks/:id`
- Odpowiedź z `GET` nadpisuje lokalny stan `task`, zapewniając spójność
- Mechanizm `refetchAfterSave` zapobiega rozjazdom danych z backendem
- Hook automatycznie resetuje `showSaved` po 1500ms (1.5 sekundy)

### 🔗 Powiązane komponenty

- `TaskCard.jsx` – steruje cyklem edycji, przekazuje funkcje do `TaskCardView`
- `TaskCardView.jsx` – aktywuje pola edytowalne i zapisuje dane przez hook
- `DueDateEditor.jsx`, `DifficultySelector.jsx` – używają `setXxx()` i `saveXxx()`

---

## 🧩 (Opcjonalnie) `useAuth()` – hook z kontekstu

Chociaż nie znajduje się w folderze `hooks/`, hook `useAuth()` zdefiniowany jest w `AuthContext.jsx` i również pełni funkcję hooka aplikacyjnego. Zapewnia dostęp do globalnego stanu autoryzacji.

### 📄 API:

```js
const { user, isAuthenticated, login, logout } = useAuth();
```

| Pole / funkcja    | Opis                                              |
| ----------------- | ------------------------------------------------- |
| `user`            | Obiekt zalogowanego użytkownika                   |
| `isAuthenticated` | Czy użytkownik jest zalogowany                    |
| `login(token)`    | Loguje użytkownika, zapisuje token                |
| `logout()`        | Usuwa token, resetuje stan i przekierowuje na `/` |

➡️ Szczegóły w `context.md`

---

## 🧪 Zasady tworzenia własnych hooków

- Nazwa **musi** zaczynać się od `use`
- Powinny one:
  - zarządzać stanem i logiką komponentu,
  - nie renderować niczego – tylko zwracać dane i funkcje,
  - unikać efektów ubocznych poza `useEffect`/`useCallback`,
  - być łatwe do testowania i izolacji
- Hooki powinny być dokumentowane (w tym w kodzie), testowane oraz osadzane wyłącznie w komponentach funkcyjnych

---

## 📄 Dokumentacja powiązana

- `components.md` – opis komponentów korzystających z hooków
- `task_flow.md` – opis cyklu życia edycji zadania
- `context.md` – opis `useAuth()` i logiki kontekstowej
