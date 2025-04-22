# 📦 AI Task App – Monorepo (Frontend + Backend)

AI Task App to inteligentna aplikacja do zarządzania zadaniami technicznymi, wspierana przez modele GPT-4o i OpenAI Embedding API. Projekt składa się z dwóch głównych części: backendu (Node.js + MongoDB) oraz frontendu (React + Vite + TailwindCSS).

---

## 📁 Struktura katalogów

```
ai-task-app/
├── backend/              # Serwer Express + AI + MongoDB
├── frontend/             # UI React + Tailwind + AuthContext
├── docs/                 # Dokumentacja projektu
├── .env                  # (globalny .env dla dev servera)
├── README.md             # (ten plik)
```

---

## ⚙️ Technologie

- Backend: Node.js, Express, MongoDB, JWT, GPT-4o, AES-256
- Frontend: React, TailwindCSS v4, Vite, JWT (localStorage)
- Komunikacja: REST API (`/api/...`), autoryzacja tokenem
- Styl: Prettier, modularny podział katalogów

---

## 🚀 Uruchomienie projektu (dev)

1. Sklonuj repozytorium:

```bash
git clone ...
```

2. Przejdź do katalogu i zainstaluj zależności:

```bash
npm install
```

3. Uruchom jednocześnie frontend i backend:

```bash
npm run dev
```

> Wymaga `concurrently` – dodane do `devDependencies`

---

## 🧪 Środowiska `.env`

- Backend: `backend/.env`
- Frontend: `frontend/.env`

---

## 📄 Dokumentacja

Główna dokumentacja: `docs/`  
Szczegółowe README:

- [`backend/README.md`](../backend/README.md)
- [`frontend/README.md`](../frontend/README.md)

---

## 📌 Status

- ✅ Backend w pełni funkcjonalny (AI, sesje, zadania, walidacja)
- ✅ Frontend: logowanie, rejestracja, obsługa zadań, AI, zamykanie
- 🧠 Integracja z AI działa (GPT-4o + embeddingi)
- 🧱 Dokumentacja aktualizowana – zgodna z wersją 0.0.15
