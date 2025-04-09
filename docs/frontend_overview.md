# AI Task App – Frontend Overview

## 🎨 Technologie

- React
- TailwindCSS
- React Router DOM
- Fetch / Axios
- JWT (zapis w localStorage)
- Vite lub Create React App (w zależności od środowiska)

---

## 🏗️ Planowana struktura katalogów

```
frontend/
├── public/             # Statyczne pliki HTML / ikony
├── src/
│   ├── assets/         # Ikony, grafiki
│   ├── components/     # Elementy UI (np. Button, TaskCard)
│   ├── context/        # Konteksty globalne (np. AuthContext)
│   ├── pages/          # Widoki (Login, Register, Dashboard, TaskDetails)
│   ├── services/       # API: auth, tasks, GPT
│   ├── App.jsx
│   └── main.jsx
├── .env
└── tailwind.config.js
```

---

## 🧩 Widoki i komponenty (planowane)

### Strony (pages):

- `LoginPage` – formularz logowania
- `RegisterPage` – rejestracja użytkownika
- `DashboardPage` – lista zadań, sortowanie, filtry
- `TaskFormPage` – tworzenie/edycja zadania
- `TaskDetailsPage` – podgląd + zamykanie zadania
- `AdminPanelPage` – panel administratora (rola: `admin`)
- `StatsPage` – ukryta strona statystyk

### Komponenty (components):

- `TaskCard`
- `Input`, `Button`
- `Header`, `Sidebar`, `Layout`
- `Modal` (potwierdzenie, komunikaty AI)

---

## 🔐 Uwierzytelnianie

- JWT otrzymywany z backendu i przechowywany w `localStorage`
- Token przesyłany w nagłówkach API (`Authorization: Bearer`)
- Kontekst `AuthContext` zarządza stanem użytkownika i logowaniem
- Protected Routes do zabezpieczenia dostępu

---

## 🔗 Komunikacja z backendem

API backendu: `http://localhost:5000/api`

Przykład użycia:

```js
fetch(`${import.meta.env.VITE_API_URL}/api/tasks`, {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer ' + token
  },
  body: JSON.stringify(taskData)
});
```

---

## 📦 Konfiguracja

Plik `.env`:

```
VITE_API_URL=http://localhost:5000
```

---

## 🔄 Interakcja z GPT (planowana)

- Tworzenie zadań na podstawie opisu użytkownika
- Podpowiedzi AI do priorytetów, trudności, kolejności realizacji
- Generowanie notatek, streszczeń i sugestii rozwiązania

---

## ✅ Funkcje (stan i plany)

- [x] Rejestracja i logowanie
- [ ] Lista zadań z filtrowaniem
- [ ] Tworzenie/edycja zadań
- [ ] Zamknięcie zadania + podsumowanie GPT
- [ ] Semantyczne wyszukiwanie zadań
- [ ] Panel admina (rola: `admin`)
- [ ] Dashboard kliknięć (ukryty dostęp + hasło)

---

## 📄 Dokumentacja powiązana

- `project_overview.md` – pełny kontekst projektu, cele, architektura, repozytoria, AI, modularność
- `backend_overview.md` – opis struktury backendu, endpointów, technologii i modelu autoryzacji
- `frontend_overview.md` – opis frontendu, komponentów, architektury, interfejsów użytkownika
- `api_spec.md` – specyfikacja endpointów REST API (auth, tasks, AI), dane wejściowe/wyjściowe
- `ai_integration.md` – jak GPT-4 wspiera zadania: tworzenie, ocena, zamykanie, priorytetyzacja
- `project_roadmap.md` – roadmapa projektu: fazy rozwoju, MVP, AI, skalowanie, funkcje zespołowe