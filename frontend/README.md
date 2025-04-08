# AI Task App – Frontend

Ten folder będzie zawierał frontendową część aplikacji AI Task App – stworzoną w oparciu o React i TailwindCSS. Interfejs użytkownika będzie komunikował się z backendem przez REST API.

## 🧰 Technologie (planowane)

- React (Vite lub Create React App)
- TailwindCSS
- React Router (routing)
- Context API lub Redux (zarządzanie stanem)
- JWT Auth + localStorage
- Axios lub Fetch API

## 📂 Planowana struktura folderów

```
frontend/
├── public/             # Statyczne pliki
├── src/
│   ├── assets/         # Ikony, grafiki
│   ├── components/     # Komponenty UI
│   ├── pages/          # Widoki (Login, Register, Tasks, Dashboard)
│   ├── services/       # Komunikacja z API (np. auth, tasks)
│   ├── context/        # Autoryzacja i globalny stan
│   ├── App.jsx
│   └── main.jsx
├── .env
└── tailwind.config.js
```

## ✨ Planowane funkcje

- Rejestracja i logowanie użytkownika
- Przechowywanie tokenu JWT w localStorage
- Tworzenie i edycja zadań z pomocą GPT
- Widok historii i szczegółów zadania
- Przeszukiwanie zakończonych zadań
- Podsumowanie rozwiązania zadania przez GPT
- Panel admina (tylko dla użytkowników z rolą `admin`)
- Ukryty dashboard ze statystykami kliknięć (hasło + ikona)

## 🚀 Uruchomienie (docelowo)

1. Przejdź do folderu frontend:

```bash
cd frontend
```

2. Zainstaluj zależności:

```bash
npm install
```

3. Uruchom aplikację w trybie deweloperskim:

```bash
npm run dev
```

4. Otwórz w przeglądarce:

```
http://localhost:5173
```

## 🔗 Komunikacja z backendem

- Backend nasłuchuje na porcie `5000`
- Frontend będzie komunikować się z API za pomocą `fetch` lub `axios` (np. `http://localhost:5000/api/auth/register`)

## 📝 Dokumentacja

Zobacz: `../docs/AI_Task_App_backend_dokumentacja_szczegolowa.docx`
