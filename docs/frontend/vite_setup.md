# ⚙️ Dokumentacja – Konfiguracja Vite (vite_setup.md)

Ten dokument opisuje sposób konfiguracji i działania Vite w projekcie frontendowym AI Task App, ze szczególnym uwzględnieniem integracji z TailwindCSS w wersji 4 oraz nowoczesnych praktyk rozwoju aplikacji React.

---

## 🧠 Co to jest Vite?

**Vite** to nowoczesny bundler, który działa błyskawicznie dzięki wykorzystaniu ESBuild i ESM. W AI Task App zastępuje on klasyczne środowiska jak CRA czy Webpack.

Zalety:

- natychmiastowy dev server,
- wsparcie dla ESModules i JSX,
- świetna integracja z React i TailwindCSS v4,
- automatyczne ładowanie zmiennych środowiskowych.

---

## 🚀 Jak utworzono projekt

Projekt frontendowy został zainicjowany poleceniem:

```bash
npm create vite@latest frontend
```

Wybrano:

- Framework: `React`
- Wariant: `JavaScript + SWC` (dla szybkiej kompilacji)

---

## 🎨 Integracja TailwindCSS v4

TailwindCSS został zainstalowany i skonfigurowany zgodnie z nowym podejściem (v4) z użyciem oficjalnej wtyczki dla Vite.

### 📦 Instalacja

```bash
npm install tailwindcss @tailwindcss/vite
```

> ⚠️ Nie używa się już `npx tailwindcss init` ani `tailwind.config.js`

### 🧩 Konfiguracja `vite.config.js`

```js
import { defineConfig } from "vite";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [tailwindcss()],
});
```

### 💅 Styl globalny (np. `index.css`)

```css
@import "tailwindcss";
```

> Wszystkie klasy Tailwind są automatycznie rozpoznawane – nie trzeba ręcznie definiować content path.

---

## 🌍 Obsługa `.env` (VITE_API_URL)

Zmienna API endpointu ustawiana jest w `.env`:

```env
VITE_API_URL=http://localhost:5000/api
```

W kodzie dostępna przez:

```js
import.meta.env.VITE_API_URL;
```

---

## 🛠️ Skrypty uruchomieniowe (`package.json`)

```json
"scripts": {
  "dev": "vite",
  "build": "vite build",
  "preview": "vite preview"
}
```

---

## 📦 Uruchamianie aplikacji

### Tylko frontend:

```bash
cd frontend
npm install
npm run dev
```

### Frontend + backend równolegle:

```bash
npm run dev
```

> Zakłada użycie `concurrently` w katalogu głównym repozytorium.

---

## 📄 Dokumentacja powiązana

- `frontend_overview.md` – ogólna architektura aplikacji
- `env_FULL.md` – opis zmiennych środowiskowych
- `src.md` – struktura katalogów frontendowych
