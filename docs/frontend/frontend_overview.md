# 🧱 AI Task App – Frontend Overview (wersja rozszerzona)

Ten dokument przedstawia kompletny przegląd architektury frontendowej aplikacji AI Task App. Opisuje zastosowane technologie, strukturę katalogów, komponenty, logikę autoryzacji, routing, komunikację z backendem oraz interakcję z AI.

---

## 🎨 Zastosowane technologie

- **React** – główny framework do budowy UI
- **TailwindCSS** – stylowanie utility-first
- **React Router DOM** – routing oparty na komponentach
- **Vite** – nowoczesny bundler (zamiast CRA)
- **JWT** – przechowywanie sesji po stronie klienta (w `localStorage`)
- **Fetch API** – komunikacja z backendem
- (planowane: `axios`, `react-query`, `shadcn/ui`)

---

## 🗂️ Struktura katalogów frontend (`src/`)

```
src/
├── assets/           # Grafiki, logotypy, ikony
├── components/       # Header, ProtectedRoute, UI helpers
├── context/          # AuthContext – zarządzanie stanem użytkownika
├── pages/            # Widoki stron: Login, Register, Tasks, Welcome
├── services/         # (planowane) komunikacja API (auth, tasks)
├── App.jsx           # Konfiguracja tras
├── main.jsx          # Punkt wejścia React + AuthProvider
├── index.css         # TailwindCSS
```

---

## 🧩 Widoki (`pages/`)

- `WelcomePage` – ekran powitalny, intro + linki do logowania/rejestracji
- `LoginPage` – formularz logowania z obsługą tokena
- `RegisterPage` – formularz rejestracji z walidacją
- `TasksPage` – widok główny użytkownika (po zalogowaniu)
- `TaskFormPage` – (planowane) tworzenie zadania z AI

---

## 🧱 Komponenty (`components/`)

- `Header` – widoczny zawsze, zawiera linki zależne od logowania
- `ProtectedRoute` – wrapper dla tras wymagających `AuthContext`
- Planowane:
  - `TaskCard`, `Loader`, `FormField`, `Modal`, `MainLayout`

---

## 🔐 Autoryzacja (`AuthContext`)

- Token JWT zapisany w `localStorage`
- Globalny kontekst `AuthContext` zarządza sesją
- Komponenty używają `useAuth()` do:
  - logowania (`login()`)
  - wylogowania (`logout()`)
  - sprawdzania `isAuthenticated` / `isLoading`

---

## 🧭 Routing (`App.jsx` + `ProtectedRoute`)

- Konfiguracja tras z użyciem `react-router-dom`
- `/tasks` zabezpieczony przez `ProtectedRoute`
- Wszystkie strony renderowane przez `<Routes>`

---

## 🌍 Komunikacja z backendem (VITE_API_URL)

- Endpointy zaczynają się od: `${import.meta.env.VITE_API_URL}/api/...`
- Użycie `fetch()` (planowane: przeniesienie do `services/`)
- Token przesyłany w nagłówku `Authorization`

---

## 💡 Interfejs użytkownika (UI/UX)

- Layout z `Header` + `main`
- Stylizacja oparta w 100% na TailwindCSS
- Responsywność:
  - `min-h-screen`, `px-4`, `sm:text-2xl`
  - Skalowanie na mobile/tablet/desktop
- Klasy Tailwind wykorzystywane konsekwentnie w komponentach

---

## 📦 Plik `.env`

```
VITE_API_URL=http://localhost:5000/api
```

> Szczegóły: patrz `env_FULL.md`

---

## 🧠 Integracja z AI (planowana)

- GPT-4o tworzy zadania na podstawie opisu
- AI ocenia podsumowania i je wygładza (`ai-close`)
- Komponent `TaskFormPage` (planowany) będzie wysyłać opis do API
- Po stronie backendu generowany jest embedding i `similarTasks`

---

## ✅ Funkcje zrealizowane

- [x] Rejestracja i logowanie z obsługą tokena
- [x] Ochrona tras (`ProtectedRoute`)
- [x] Dynamiczny Header
- [x] Przechowywanie sesji (`localStorage`)
- [ ] Widok zadań
- [ ] Tworzenie zadania z AI
- [ ] Panel admina
- [ ] Obsługa feedbacku do podobnych zadań
- [ ] Obsługa `.env` dla różnych środowisk

---

## 🧩 Dokumentacja powiązana

- `src.md` – szczegółowa struktura katalogów frontend
- `components.md` – opis komponentów globalnych
- `context.md` – pełna dokumentacja `AuthContext`
- `pages.md` – widoki: routing, logika, zależności
- `services_PLANNED.md` – logika komunikacji z backendem (planowana)
- `auth_flow_FULL.md` – pełny przepływ autoryzacji
- `ui_structure_FULL.md` – hierarchia layoutu i stylowanie
- `env_FULL.md` – zmienne środowiskowe
