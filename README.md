# 📦 AI Task App – Monorepo (Frontend + Backend)

AI Task App to inteligentna aplikacja do zarządzania zadaniami technicznymi, wspierana przez modele GPT-4o i OpenAI Embedding API. Projekt składa się z dwóch głównych części: backendu (Node.js + MongoDB) oraz frontendu (React + Vite + TailwindCSS).

---

## 📁 Struktura katalogów

```
ai-task-app/
├── backend/              # Serwer Express + AI + MongoDB
├── frontend/             # UI React + Tailwind + AuthContext
├── .env                  # (globalny .env dla dev servera)
├── .gitmodules           # jeśli używane submoduły Git
├── README.md             # (ten plik)
```

---

## ⚙️ Technologie

- Backend: Node.js, Express, MongoDB, JWT, GPT-4o, AES-256
- Frontend: React, TailwindCSS v4, Vite, JWT (localStorage)
- Komunikacja: REST API (`/api/...`), autoryzacja tokenem
- Styl: Prettier, modularny podział katalogów
- Monorepo: wspólna konfiguracja `npm run dev`

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

Pełna dokumentacja:

- [`env.md`](./docs/backend/env.md)
- [`env_FULL.md`](./docs/frontend/env.md)

---

## 📄 Dokumentacja

Dokumentacja projektu znajduje się w katalogu `docs/` (lub jako repozytorium Notion/Obsidian/Markdown).

Główne pliki:

- `frontend_README_FULL.md` – architektura frontendu
- `backend_README_FULL.md` – architektura backendu
- `api_spec.md` – endpointy REST API
- `auth_flow_FULL.md` – pełen przepływ sesji/logowania
- `services.md`, `controllers.md`, `middleware.md`
- `vite_setup.md`, `ui_structure.md`, `src.md`

---

## 👨‍💻 Autorzy i współtwórcy

- Projekt prowadzony jednoosobowo
- Architektura dokumentowana jako część pracy inżynierskiej / rozwojowej

---

## 📌 Status

- ✅ Backend w pełni funkcjonalny
- ✅ Frontend: logowanie, rejestracja, sesja, widok użytkownika
- 🧠 Integracja z AI działa (GPT-4o + embeddingi)
- 🧱 Trwa porządkowanie dokumentacji + planowanie panelu admina
