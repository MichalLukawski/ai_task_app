# Changelog

## [0.0.11] – 2025-04-13

### Added

- Uruchamianie frontend + backend jedną komendą `npm run dev` (z `concurrently`)
- Główne `README.md` opisujące:
  - strukturę monorepo
  - uruchomienie projektu
  - zależności pomiędzy warstwami
- Podział dokumentacji na frontend/backend + katalog ogólny
- Ujednolicenie numeracji wersji (frontend `0.0.1`, backend `0.0.11`)

### Changed

- Rozdzielenie changelogów i dokumentacji dla backendu i frontendu
- Uporządkowanie plików `.env` i zmiennych środowiskowych dla każdej warstwy
- Ustandaryzowanie stylu plików `.md` (nagłówki, bloki kodu, tabele)

### Planned

- Synchronizacja wersji backend + frontend w CI/CD
- Jednolity system tagowania release’ów Git
- Dashboard + interfejs administratora
