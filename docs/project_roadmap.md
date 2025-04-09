# AI Task App – Roadmapa projektu

## 🎯 Cel projektu

Stworzenie aplikacji AI wspierającej specjalistów technicznych w dokumentowaniu, przeszukiwaniu i realizacji zadań – z pomocą GPT-4 oraz integracji z MongoDB.  
Projekt ma służyć zarówno użytkownikom indywidualnym, jak i zespołom (w przyszłości także firmom).

---

## ✅ Etap 1: MVP (ukończony / w toku)

- [x] Konfiguracja backendu (Node.js + Express)
- [x] Połączenie z MongoDB + Mongoose
- [x] Rejestracja użytkownika (`POST /api/auth/register`)
- [x] Hashowanie haseł (`bcrypt`)
- [x] Spójne odpowiedzi API (`sendSuccess` / `sendError`)
- [x] Konfiguracja `.env`, `.gitignore`, dokumentacja
- [x] Podział na osobne repo: backend + frontend
- [x] README + submodule w głównym repo
- [x] Dokumentacja w folderze `/docs/`
- [ ] Logowanie z JWT
- [ ] Middleware `requireAuth` i `requireRole`
- [ ] Model i endpoint `Task`
- [ ] Testowy interfejs frontendowy (formularze)
- [ ] Połączenie front-back przez REST

---

## 🧩 Etap 2: Podstawowa funkcjonalność użytkownika

- [ ] Widok listy zadań (frontend)
- [ ] Tworzenie zadania wspomaganego przez GPT
- [ ] Edycja i zamykanie zadania
- [ ] Podsumowanie wykonania zadania (GPT)
- [ ] Przeszukiwanie semantyczne (najprostsze dopasowanie)
- [ ] Sortowanie wg terminu, trudności, priorytetu
- [ ] Obsługa błędów i komunikaty frontendowe

---

## 🔐 Etap 3: Autoryzacja i role

- [ ] Logowanie (`/api/auth/login`)
- [ ] JWT token + zapisywanie w localStorage
- [ ] Middleware autoryzacyjne backendu
- [ ] Ochrona tras frontendowych
- [ ] Rozpoznawanie ról (`admin`, `user`)

---

## 📈 Etap 4: Rozszerzenia i skalowanie

- [ ] Obsługa organizacji i zespołów (`organizationId`, `teamId`)
- [ ] Dashboard użytkownika z analizą AI
- [ ] Ukryta strona ze statystykami (hasło + ikonka)
- [ ] Gamifikacja (punkty za zamykanie zadań, ranking)
- [ ] Tryb offline (dane lokalne)
- [ ] Eksport zadań / notatek do Markdown / PDF

---

## 🤖 Etap 5: Rozbudowa integracji z AI

- [ ] Klasyfikacja trudności zadania przez GPT
- [ ] Priorytetyzacja kolejności zadań
- [ ] Sugestie AI: „Co warto zrobić teraz?”
- [ ] Wsparcie embeddingów i wektorowego przeszukiwania
- [ ] Integracja z Pinecone / Qdrant / Weaviate
- [ ] Prompt systemowy ustawiany przez administratora
- [ ] Caching odpowiedzi GPT (dla wydajności)

---

## ☁️ Etap 6: Hosting i produkcja

- [ ] Wersja produkcyjna backendu (Docker / VPS / Cloud)
- [ ] Wersja frontend (Netlify, Vercel, lokalnie)
- [ ] CI/CD – GitHub Actions dla backendu i frontendu
- [ ] Backup MongoDB (Atlas / dump)
- [ ] Rejestr logów + monitoring (np. PM2, Grafana)

---

## 🧠 Etap 7: Personalizacja i rozwój

- [ ] Profil użytkownika z notatkami osobistymi
- [ ] Zadania prywatne / współdzielone
- [ ] Edytor promptów GPT (dla power-userów)
- [ ] API publiczne (dla rozszerzeń / aplikacji zewnętrznych)
- [ ] Wersja mobilna (PWA lub aplikacja React Native)

---

## 🗂️ Dokumentacja powiązana

- `project_overview.md`
- `backend_overview.md`
- `frontend_overview.md`
- `ai_integration.md`

## 📄 Dokumentacja powiązana

- `project_overview.md` – pełny kontekst projektu, cele, architektura, repozytoria, AI, modularność
- `backend_overview.md` – opis struktury backendu, endpointów, technologii i modelu autoryzacji
- `frontend_overview.md` – opis frontendu, komponentów, architektury, interfejsów użytkownika
- `api_spec.md` – specyfikacja endpointów REST API (auth, tasks, AI), dane wejściowe/wyjściowe
- `ai_integration.md` – jak GPT-4 wspiera zadania: tworzenie, ocena, zamykanie, priorytetyzacja
- `project_roadmap.md` – roadmapa projektu: fazy rozwoju, MVP, AI, skalowanie, funkcje zespołowe