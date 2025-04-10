# 🛠️ Dokumentacja utils – AI Task App

## 📄 Plik: `utils/responseHandler.js`

### `sendSuccess(res, message, data, status = 200)`
- **Opis:** Wysyła jednolitą odpowiedź sukcesu.
- **Parametry:**
  - `res` – obiekt odpowiedzi Express
  - `message` – tekstowy komunikat (np. "Login successful")
  - `data` – opcjonalne dane do zwrócenia
  - `status` – kod HTTP (domyślnie 200)

```json
{
  "status": "success",
  "message": "Login successful",
  "data": { "token": "..." }
}
```

---

### `sendError(res, message, status = 500, code = null)`
- **Opis:** Wysyła jednolitą odpowiedź błędu.
- **Parametry:**
  - `res` – obiekt odpowiedzi Express
  - `message` – tekst błędu
  - `status` – kod HTTP (np. 401, 500)
  - `code` – kod błędu wewnętrznego (opcjonalny, np. `INVALID_TOKEN`)

```json
{
  "status": "error",
  "message": "Invalid token",
  "code": "INVALID_TOKEN"
}
```

---

## 📄 Plik: `utils/logger.js`

### `logGPTFallback(rawResponse, userDescription)`
- **Opis:** Zapisuje przypadki, w których odpowiedź GPT nie mogła zostać sparsowana do JSON
- **Gdzie zapisuje:** do pliku `logs/gpt_fallbacks.log`
- **Co zawiera wpis:**
  - Data i czas
  - Oryginalny opis zadania
  - Surowa odpowiedź GPT
  - Separator dla czytelności

#### Przykład wpisu:
```
[2025-04-10T20:51:33.432Z]
🟡 Fallback JSON parsing failed
Opis użytkownika:
Nie działa endpoint logowania

Odpowiedź GPT:
{ "description": "..." }

-----------------------------------------------
```

---

## 🧩 Uwagi

- Plik `logs/gpt_fallbacks.log` nie wymaga osobnej dokumentacji – jego użycie opisane jest w kontekście `utils/logger.js`
- Można go analizować ręcznie lub zautomatyzować analizę statystyk błędów GPT
