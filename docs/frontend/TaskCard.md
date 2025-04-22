## TaskCard – dokumentacja komponentu

### Przegląd

`TaskCard` to centralny komponent frontendowy w aplikacji zarządzania zadaniami oparty na AI. Reprezentuje pojedyncze zadanie wyświetlane w dashboardzie użytkownika. Umożliwia wyświetlanie szczegółów zadania, interaktywną edycję pól (`dueDate`, `difficulty`), generowanie podsumowania przez AI, ręczne zamykanie zadania, przegląd podobnych zadań oraz trwałe usuwanie zadania.

Komponent działa w kontekście listy zadań (najczęściej w `DashboardPage`) i występuje wielokrotnie – każda instancja reprezentuje jedno zadanie.

---

### Struktura komponentu

`TaskCard` składa się z trzech głównych części:

1. **Widok główny (`TaskCardView`)** – odpowiada za prezentację danych zadania, renderowanie pól edycyjnych oraz interfejs użytkownika do zamykania, przeglądania podobnych i usuwania zadania.
2. **Logika stanu (`useTaskCardState`)** – lokalny hook zarządzający stanem edycji, AI, synchronizacją z backendem i logiką usuwania.
3. **Obsługa interakcji użytkownika (`useTaskCardInteraction`)** – logika kliknięć, fokusowania, obsługi klawiatury i zamykania aktywnych kart.

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

#### 3. Podsumowanie i zamykanie zadania

##### Zamykanie z AI (`closeWithAi`):

- Użytkownik może kliknąć przycisk „🤖 Close with AI”.
- Wyświetlany jest komponent `CloseWithAiBox` z polem tekstowym do wpisania podsumowania.
- Po zatwierdzeniu:
  - Wysyłany jest `PATCH /tasks/:id/ai-close`.
  - AI ocenia jakość podsumowania – w razie odrzucenia wyświetlany jest `AiSummaryRejectedModal` z opcją „Zapisz mimo to”.
  - Jeśli użytkownik potwierdzi – podsumowanie zapisywane jest przez `PATCH /tasks/:id/close`.

##### Ręczne zamknięcie (`closeWithoutAI`):

- Użytkownik może ręcznie zamknąć zadanie poprzez modal AI, wybierając „Zapisz mimo to”.
- Wysyłany jest `PATCH /tasks/:id/close` z podanym podsumowaniem.

##### Efekty zamknięcia:

- Zadanie otrzymuje `status: 'closed'`, pole `summary` i `closedAt`.
- W widoku zadania prezentowane są:
  - Tytuł i opis
  - Sekcja „Podsumowanie”
  - Data zamknięcia (po prawej)

#### 4. Przegląd podobnych zadań

- Jeśli `similarTasks.length > 0`, wyświetlany jest przycisk „🧠 Podobne (X)”.
- Po kliknięciu pojawia się `SimilarTasksPopup`:
  - Wyświetla listę podobnych zadań (`title`, `description`, `summary`, `createdAt`, `closedAt`)
  - Umożliwia przegląd – ale nie edycję

#### 5. Usuwanie zadania

- Przycisk `🗑️ Usuń` jest dostępny dla zadań otwartych i zamkniętych.
- Po kliknięciu pojawia się `confirm(...)` z ostrzeżeniem.
- Po potwierdzeniu:
  - Wysyłane jest `DELETE /tasks/:id`.
  - Zadanie jest usuwane z bazy i lokalnego stanu `DashboardPage`.

#### 6. Zapis danych i synchronizacja z backendem

- Po każdej edycji pola wykonywany jest `PATCH /tasks/:id`.
- Następnie wykonywany jest `GET /tasks/:id`, by odświeżyć dane z backendu (eliminacja problemów synchronizacji).
- Komponent nadrzędny (`DashboardPage`) otrzymuje zaktualizowane dane poprzez `onTaskUpdated()`.

#### 7. Informacja zwrotna dla użytkownika

- W trakcie zapisu pojawia się komunikat `🔄 Zapisuję...`.
- Po poprawnym zapisie: `✔ Zapisano` (pokazywane przez 1,5 sekundy).
- Komponent obsługuje flagi `isSaving` oraz `showSaved`.

---

### Szczegóły techniczne i kluczowe zależności

- Obsługa kliknięć odfiltrowuje kliknięcia w `.editable-field`, aby zapobiec zamykaniu edytowanych pól.
- Obsługa `mousedown` została zastąpiona `click` z opóźnieniem (`setTimeout`), by React zdążył zaktualizować `isFocused` przed wykonaniem `handleClickOutside`.
- `useRef` przechowuje aktualną wartość `isFocused` jako `isFocusedRef`, aby uniknąć problemów wynikających z asynchroniczności `setState`.
- Edycja działa również przy klawiaturze (`Enter`) z pełną zgodnością z interakcjami myszą.
- Wszystkie stany są trzymane lokalnie, a synchronizacja z backendem odbywa się tylko przy zapisie.
- `useTaskCardState` zarządza wszystkimi żądaniami HTTP: `GET`, `PATCH`, `DELETE`.

---

### Wymagania projektowe i UX

- Tylko jedna karta może być otwarta (fokusowana) jednocześnie.
- Kliknięcia muszą być intuicyjne i rozróżniane: klik w edytowalne pole ≠ klik w kartę.
- Edytowanie wartości nie powinno być przerywane przez przypadkowe kliknięcie (szczególnie dotyczy to inputów i selectów).
- Zamykanie zadania z AI musi być jednoznacznie potwierdzone – użytkownik musi świadomie zaakceptować odrzucenie AI.
- Usuwanie zadania musi być zabezpieczone confirmem – bez przypadkowego kliknięcia.
- Nie wolno dopuścić do sytuacji, gdzie dane wizualnie wyglądają na zapisane, ale backend ich nie widzi – stąd wymuszony `GET` po `PATCH`.

---

### Dalsze możliwości rozwoju komponentu

- Obsługa edycji tytułu i opisu zadania inline.
- Wprowadzenie przycisku „Anuluj” przy edycji pól (obecnie tylko zapis).
- Umożliwienie cofnięcia zmian (`Esc`).
- Dodanie wizualnego wskaźnika błędu zapisu.
- Obsługa konfliktów wersji danych (np. zadanie zmodyfikowane przez innego użytkownika).
- Dodanie funkcjonalności przenoszenia zadań do kosza (przed usunięciem definitywnym).
- Rozbudowa dymku podobnych zadań o opcję „Otwórz szczegóły”.

---

### Podsumowanie

`TaskCard` to złożony, wielofunkcyjny komponent zarządzania zadaniami. Wspiera edycję danych, synchronizację z backendem, logikę AI, usuwanie, przegląd podobnych zadań oraz zabezpieczenia UX. Stanowi jeden z najważniejszych elementów interfejsu w aplikacji AI Task App. Jego rozwój powinien być stale dokumentowany i testowany w kontekście współpracy z backendem oraz zgodności z oczekiwanym zachowaniem użytkownika.

---
