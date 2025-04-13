# 🔐 Dokumentacja – Zmienne środowiskowe `.env` (AI Task App – Backend)

Plik `.env` backendu AI Task App służy do konfiguracji kluczowych parametrów systemu serwerowego. Pozwala bezpiecznie przechowywać dane poufne oraz dostosowywać środowisko bez konieczności modyfikowania kodu źródłowego.

---

## 📁 Lokalizacja

Plik `.env` znajduje się w katalogu `backend/`. Nie powinien być commitowany do repozytorium – musi być dodany do `.gitignore`.

---

## ✅ Zmienne obowiązkowe

### 🔹 `PORT`

- **Typ:** liczba (np. `5000`)
- **Opis:** Port, na którym uruchamiany jest backend Express.

### 🔹 `MONGO_URI`

- **Typ:** string (URI MongoDB)
- **Opis:** Połączenie z bazą MongoDB (lokalne lub MongoDB Atlas).
- **Przykład:**
  ```
  MONGO_URI=mongodb://localhost:27017/ai-task-app
  ```

### 🔹 `JWT_SECRET`

- **Typ:** string
- **Opis:** Klucz używany do podpisywania tokenów JWT.
- **Wymagania:** powinien być długi i trudny do odgadnięcia.

---

## 🔐 Zmienne bezpieczeństwa i AI

### 🔹 `SECRET_ENCRYPTION_KEY`

- **Typ:** string (64 znaki hex – 32 bajty)
- **Opis:** Klucz AES-256-GCM używany do szyfrowania klucza OpenAI zapisywanego w MongoDB.
- **Wygeneruj własny:**
  ```
  node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
  ```

### 🔹 `OPENAI_API_KEY` (opcjonalny fallback)

- **Typ:** string (`sk-...`)
- **Opis:** Klucz API OpenAI – używany tylko, jeśli nie znaleziono zaszyfrowanego klucza w bazie danych.
- **Uwaga:** nie zaleca się trzymania tego klucza na produkcji bez szyfrowania.

---

## 🧪 Przykładowy `.env`

```env
PORT=5000
MONGO_URI=mongodb://localhost:27017/ai-task-app
JWT_SECRET=super_secret_key_here
SECRET_ENCRYPTION_KEY=0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef
# OPENAI_API_KEY=sk-abc123...
```

---

## 🧠 Uwagi bezpieczeństwa

- Klucz `JWT_SECRET` oraz `SECRET_ENCRYPTION_KEY` są **krytyczne** – ich utrata umożliwia pełny dostęp lub odszyfrowanie kluczy API.
- Nigdy nie przechowuj `OPENAI_API_KEY` bez szyfrowania na środowiskach produkcyjnych.
- `.env` powinien być monitorowany przez `.env.example` z pustymi wartościami.

---

## 📄 Dokumentacja powiązana

- `backend_overview.md` – struktura i działanie serwera
- `services/openaiKeyManager.js` – szyfrowanie kluczy OpenAI
- `middleware/auth.js` – uwierzytelnianie tokenem JWT
- `ai_integration.md` – fallback do `.env` lub klucz w bazie
