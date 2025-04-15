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

Hook `useTaskCardState()` odpowiada za zarządzanie **lokalnym stanem pojedynczej karty zadania** (`TaskCard`). Umożliwia przełączanie między trybem podglądu (`View`) a trybem edycji (`Edit`), obsługuje aktualizację i anulowanie zmian, kontrolując wewnętrzny stan komponentu w sposób deklaratywny i czysty.

### 📄 API hooka

```js
const {
  isEditing,
  editedTask,
  handleEditClick,
  handleCancelEdit,
  handleSaveEdit,
  handleFieldChange
} = useTaskCardState(initialTask);
```

| Zmienna / Funkcja      | Typ        | Opis                                                                 |
|------------------------|------------|----------------------------------------------------------------------|
| `isEditing`            | `boolean`  | Czy karta aktualnie znajduje się w trybie edycji                    |
| `editedTask`           | `object`   | Kopia edytowanego zadania (lokalny stan)                            |
| `handleEditClick()`    | `function` | Przełącza kartę w tryb edycji                                       |
| `handleCancelEdit()`   | `function` | Anuluje edycję, przywraca dane z `initialTask`                      |
| `handleSaveEdit()`     | `function` | Zapisuje zmiany (zazwyczaj wywołuje PATCH do API)                   |
| `handleFieldChange()`  | `function` | Obsługuje zmiany pól formularza edycji                              |

### 🔗 Powiązane komponenty

- `TaskCard.jsx` – korzysta z hooka do przełączania stanu między widokiem a edycją
- `TaskCardView.jsx` – aktywuje `handleEditClick()`
- `TaskCardEdit.jsx` – używa `editedTask`, `handleFieldChange`, `handleSaveEdit`, `handleCancelEdit`

### 💡 Zalety podejścia

- separacja logiki edycji od logiki prezentacji,
- łatwość testowania (można testować hook niezależnie),
- ułatwienie debugowania i utrzymania kodu,
- umożliwia ponowne użycie logiki w innych komponentach (jeśli zajdzie taka potrzeba).

---

## 🧩 (Opcjonalnie) `useAuth()` – hook z kontekstu

Chociaż nie znajduje się w folderze `hooks/`, hook `useAuth()` zdefiniowany jest w `AuthContext.jsx` i również pełni funkcję hooka aplikacyjnego. Zapewnia dostęp do globalnego stanu autoryzacji.

### 📄 API:

```js
const { user, isAuthenticated, login, logout } = useAuth();
```

| Pole / funkcja | Opis                                                       |
|----------------|------------------------------------------------------------|
| `user`         | Obiekt zalogowanego użytkownika                            |
| `isAuthenticated` | Czy użytkownik jest zalogowany                         |
| `login(token)` | Loguje użytkownika, zapisuje token                         |
| `logout()`     | Usuwa token, resetuje stan i przekierowuje na `/`          |

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