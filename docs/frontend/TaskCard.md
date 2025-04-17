
## TaskCard – dokumentacja komponentu

### Przegląd

`TaskCard` to centralny komponent frontendowy w aplikacji zarządzania zadaniami oparty na AI. Reprezentuje pojedyncze zadanie wyświetlane w dashboardzie użytkownika. Umożliwia wyświetlanie szczegółów zadania oraz interaktywną edycję wybranych pól, takich jak termin (`dueDate`) oraz trudność (`difficulty`). Komponent wspiera również logikę fokusowania, dynamicznego zapisu zmian, synchronizacji z backendem i zapewnia informację zwrotną dla użytkownika.

Komponent działa w kontekście listy zadań (najczęściej w `DashboardPage`) i występuje wielokrotnie – każda instancja reprezentuje jedno zadanie.

---

### Struktura komponentu

`TaskCard` składa się z trzech głównych części:

1. **Widok główny (`TaskCardView`)** – odpowiada za prezentację danych zadania oraz renderowanie pól edycyjnych.
2. **Logika stanu (`useTaskCardState`)** – lokalny hook zarządzający stanem edycji i komunikacją z backendem.
3. **Obsługa interakcji użytkownika (`TaskCard`)** – logika kliknięć, fokusowania, Enter, blur oraz synchronizacji.

---

### Główne funkcjonalności

#### 1. Fokusowanie i przełączanie kart
- Kliknięcie lewym przyciskiem myszy w kartę ustawia `isFocused = true`.
- Kliknięcie w inną kartę automatycznie dezaktywuje poprzednią.
- Kliknięcie w już aktywną kartę:
  - Jeśli edytowano dane → zapisuje i zamyka.
  - Jeśli nie edytowano → po prostu zamyka kartę.
- Wciśnięcie `Enter`:
  - Przy otwartej karcie → zapisuje lub zamyka kartę.
  - Przy zamkniętej karcie → **nie fokusuje innej karty** (zapobiega przypadkowej aktywacji).

#### 2. Edycja danych (termin i trudność)

##### Termin (`dueDate`):
- Wyświetlany jako komponent `DueDateProgress`, zawierający datę i pasek postępu.
- Po kliknięciu staje się edytowalnym polem `<input type="date">` (`DueDateEditor`).
- Obsługa `blur`, `Enter` – zapis danych.
- Jeśli `dueDate` był wcześniej pusty, pojawia się przycisk `+ Ustaw termin`.

##### Trudność (`difficulty`):
- Wyświetlana jako graficzne gwiazdki (`DifficultyStars`).
- Po kliknięciu pole staje się `<select>` z poziomami trudności (`DifficultySelector`).
- Obsługa `blur`, `Enter` – zapis danych.
- Jeśli `difficulty` był wcześniej pusty, pojawia się przycisk `+ Ustaw trudność`.

#### 3. Zapis danych i synchronizacja z backendem
- Po każdej edycji pola wykonywany jest `PATCH /tasks/:id`.
- Następnie wykonywany jest `GET /tasks/:id`, by odświeżyć dane z backendu (eliminacja problemów synchronizacji).
- Komponent nadrzędny (`DashboardPage`) otrzymuje zaktualizowane dane poprzez `onTaskUpdated()`.

#### 4. Informacja zwrotna dla użytkownika
- W trakcie zapisu pojawia się komunikat `🔄 Zapisuję...`.
- Po poprawnym zapisie: `✔ Zapisano` (pokazywane przez 1,5 sekundy).
- Komponent obsługuje flagi `isSaving` oraz `showSaved` sterujące tym zachowaniem.

---

### Szczegóły techniczne i kluczowe zależności

- Obsługa kliknięć odfiltrowuje kliknięcia w `.editable-field`, aby zapobiec zamykaniu edytowanych pól.
- Obsługa `mousedown` została zastąpiona `click` z opóźnieniem (`setTimeout`), by React zdążył zaktualizować `isFocused` przed wykonaniem `handleClickOutside`.
- `useRef` przechowuje aktualną wartość `isFocused` jako `isFocusedRef`, aby uniknąć problemów wynikających z asynchroniczności `setState`.
- Edycja działa również przy klawiaturze (`Enter`) z pełną zgodnością z interakcjami myszą.
- Wszystkie stany są trzymane lokalnie, a synchronizacja z backendem odbywa się tylko przy zapisie.

---

### Wymagania projektowe i UX

- Tylko jedna karta może być otwarta (fokusowana) jednocześnie.
- Kliknięcia muszą być intuicyjne i rozróżniane: klik w edytowalne pole ≠ klik w kartę.
- Edytowanie wartości nie powinno być przerywane przez przypadkowe kliknięcie (szczególnie dotyczy to inputów i selectów).
- Nie wolno dopuścić do sytuacji, gdzie dane wizualnie wyglądają na zapisane, ale backend ich nie widzi – stąd wymuszony `GET` po `PATCH`.

---

### Dalsze możliwości rozwoju komponentu

- Obsługa edycji tytułu i opisu zadania inline.
- Wprowadzenie przycisku „Anuluj” przy edycji pól (obecnie tylko zapis).
- Umożliwienie cofnięcia zmian (`Esc`).
- Dodanie wizualnego wskaźnika błędu zapisu.
- Obsługa konfliktów wersji danych (np. zadanie zmodyfikowane przez innego użytkownika).

---

### Podsumowanie

`TaskCard` to złożony, ale wysoce interaktywny komponent, który zapewnia użytkownikowi przyjemne i intuicyjne zarządzanie zadaniami. Dzięki synchronizacji z backendem, obsłudze wszystkich kluczowych interakcji i mechanizmom walidacji, komponent pełni kluczową rolę w aplikacji AI Task App. Dokumentacja powinna być utrzymywana zgodnie z ewolucją komponentu, ze szczególnym uwzględnieniem UX oraz zmian w strukturze danych z backendu.
