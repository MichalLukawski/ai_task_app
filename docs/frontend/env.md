# 🌍 Dokumentacja – Zmienne środowiskowe `.env` (AI Task App – Frontend)

Ten dokument zawiera szczegółowy opis zmiennych środowiskowych używanych w projekcie frontendowym AI Task App. Ich celem jest konfiguracja połączenia z backendem oraz (w przyszłości) ewentualna personalizacja środowisk (dev/prod).

---

## 📄 Plik `.env`

Plik `.env` powinien znajdować się w katalogu `frontend/` i zawierać wszystkie zmienne konfiguracyjne, które mają być dostępne podczas działania aplikacji Vite.

> ✅ Vite **wymaga**, aby każda zmienna dostępna w `import.meta.env` miała prefiks `VITE_`.

---

## ✅ Aktualnie używane zmienne

### 🔹 `VITE_API_URL`

- **Typ:** `string`
- **Przykład:**
  ```
  VITE_API_URL=http://localhost:5000/api
  ```
- **Opis:**  
  Adres backendu API, z którym frontend będzie się komunikował. Używany we wszystkich funkcjach `fetch()` lub planowanych `authService`, `taskService`.

- **Użycie w kodzie:**
  ```js
  const url = `${import.meta.env.VITE_API_URL}/auth/login`;
  ```

---

## 🧠 Typowe lokalizacje wykorzystania

| Plik / komponent          | Użycie `VITE_API_URL`            |
| ------------------------- | -------------------------------- |
| `LoginPage.jsx`           | logowanie przez `fetch(...)`     |
| `RegisterPage.jsx`        | rejestracja                      |
| `TasksPage.jsx`           | pobieranie zadań                 |
| `services/authService.js` | planowana obsługa logowania      |
| `services/taskService.js` | planowana komunikacja z `/tasks` |

---

## 🧪 Obsługa środowisk

W projekcie można utworzyć pliki:

- `.env` – domyślny (np. dev)
- `.env.production` – do buildów produkcyjnych
- `.env.local` – lokalna kopia dla developera (nie powinna być commitowana)

> 🔒 **Uwaga:** Frontend NIE powinien zawierać żadnych poufnych danych w `.env`, takich jak klucz OpenAI – takie dane należą wyłącznie do backendu.

---

## 📌 Planowane zmienne (opcjonalne)

| Nazwa               | Opis                                             |
| ------------------- | ------------------------------------------------ |
| `VITE_BUILD_ENV`    | np. `development`, `staging`, `production`       |
| `VITE_ENABLE_DEBUG` | `true` / `false` – włączanie widoków debugowania |
| `VITE_VERSION`      | numer wersji frontendu, wyświetlany w UI         |

---

## 📄 Dokumentacja powiązana

- `services_PLANNED.md` – korzysta z `VITE_API_URL`
- `pages.md` – miejsca użycia w `LoginPage`, `RegisterPage`, `TasksPage`
- `vite_setup.md` – sposób ładowania `.env` przez Vite
