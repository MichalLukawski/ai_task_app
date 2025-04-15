# 🛠️ Dokumentacja – `utils/` (Frontend AI Task App)

Folder `src/utils/` zawiera funkcje pomocnicze (`utility functions`) używane w aplikacji frontendowej AI Task App. Funkcje te nie są bezpośrednio powiązane z komponentami React, ale wspierają logikę UI – np. poprzez obliczenia, formatowanie danych lub wspólną walidację.

Celem tego dokumentu jest przedstawienie aktualnej zawartości folderu `utils/` oraz zasad projektowania i rozbudowy tego typu funkcji.

---

## 📦 Lokalizacja

```
src/
└── utils/
    └── dateUtils.js
```

---

## 📄 Plik: `dateUtils.js`

Zawiera funkcje operujące na datach. Główne zastosowanie to wsparcie komponentów UI takich jak `DueDateProgress` oraz `DueDateEditor`.

---

### 🔧 Funkcje dostępne

#### `calculateDueDatePercentage(createdAt, dueDate)`

**Opis:**  
Oblicza procent czasu, jaki upłynął między `createdAt` a `dueDate`, względem dnia bieżącego (`now`).

**Sygnatura:**  
```js
function calculateDueDatePercentage(createdAt: string, dueDate: string): number
```

**Zwraca:** liczbę od 0 do 100 (lub więcej), oznaczającą poziom realizacji oś czasu zadania.

**Zastosowanie:** komponent `DueDateProgress` – do dynamicznego wypełniania paska kolorem.

---

#### `formatDateString(dateStr)`

**Opis:**  
Formatuje ciąg znaków będący datą do formy czytelnej dla użytkownika, np. `2025-04-13` → `13 kwietnia 2025`.

**Sygnatura:**  
```js
function formatDateString(dateStr: string): string
```

**Zwraca:** przyjazną wersję daty w języku polskim (lokalizacja: `pl-PL`).

**Zastosowanie:** może być używana wszędzie tam, gdzie chcemy zaprezentować użytkownikowi datę w czytelnej formie (np. szczegóły zadania).

---

## 🧪 Konwencje i dobre praktyki

- Funkcje w `utils/`:
  - nie mają zależności od stanu komponentów ani hooków Reacta,
  - przyjmują dane wejściowe i zwracają wynik bez efektów ubocznych (czyste funkcje),
  - powinny być możliwe do testowania jednostkowego,
  - mogą być używane w wielu miejscach frontendowego kodu.

- Nazwy plików pomocniczych powinny kończyć się na `Utils.js`, `Formatter.js`, `Parser.js`, itp. – zgodnie z konwencją roli.

---

## 🧩 Możliwe przyszłe funkcje

| Funkcja                     | Opis                                                                  |
|-----------------------------|-----------------------------------------------------------------------|
| `parseNaturalDate()`        | Rozpoznawanie dat w opisie tekstowym zadania (np. "jutro", "za 3 dni")|
| `isValidDateFormat()`       | Walidacja poprawności formatu daty (`YYYY-MM-DD`)                     |
| `compareDates(dateA, dateB)`| Zwraca informację, która z dat jest wcześniejsza/later                |

---

## 📄 Dokumentacja powiązana

- `components.md` – komponenty `DueDateProgress`, `DueDateEditor`
- `task_flow.md` – przebieg tworzenia i edycji zadania z datami
- `src.md` – struktura folderów frontendowych