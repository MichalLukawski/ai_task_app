# 🛠️ Dokumentacja utils – AI Task App

## 📄 Plik: `utils/responseHandler.js`

### `sendSuccess(res, message, data, status = 200)`
- **Opis:** Wysyła jednolitą odpowiedź sukcesu.
- **Parametry:**
  - `res` – obiekt odpowiedzi Express
  - `message` – tekstowy komunikat (np. "Login successful")
  - `data` – opcjonalne dane do zwrócenia
  - `status` – kod HTTP (domyślnie 200)
- **Przykład odpowiedzi:**
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
- **Przykład odpowiedzi:**
  ```json
  {
    "status": "error",
    "message": "Invalid token",
    "code": "INVALID_TOKEN"
  }
  ```

---

Plik `responseHandler.js` zapewnia spójność odpowiedzi w całym backendzie.

