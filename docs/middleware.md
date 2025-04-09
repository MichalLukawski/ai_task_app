# 🧱 Dokumentacja middleware – AI Task App

## 🧾 Plik: `middleware/auth.js`

### Opis:
Middleware autoryzacyjny sprawdzający nagłówek `Authorization` z tokenem JWT i weryfikujący jego poprawność.

### Działanie:
- Oczekuje nagłówka `Authorization: Bearer <token>`
- Weryfikuje token przy użyciu `JWT_SECRET`
- Dodaje `req.user = { id: <userId> }` do requestu
- W przypadku błędu zwraca `401 Unauthorized` z `sendError`

### Przykład błędnej odpowiedzi:
```json
{
  "status": "error",
  "message": "Invalid or expired token",
  "code": "INVALID_TOKEN"
}
```

---

Middleware ten powinien być używany w każdej trasie wymagającej autoryzacji użytkownika.
