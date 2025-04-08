Aplikacja webowa, która pełni funkcję osobistego asystenta AI do zarządzania zadaniami. Aplikacja będzie hostowana lokalnie na moim serwerze domowym i dostępna z dowolnego urządzenia. Dane będą zapisywane w MongoDB (zarówno lokalnie, jak i w kopii chmurowej MongoDB Atlas). System będzie wykorzystywać model GPT (np. GPT-4) do wsparcia użytkownika w tworzeniu, opisywaniu, przeszukiwaniu i zamykaniu zadań.

Cel systemu:

Pomoc osobom wykonującym skomplikowane zadania (np. programistom, naukowcom, administratorom IT) w dokumentowaniu i przypominaniu sobie rozwiązań podobnych problemów w przeszłości.

Kluczowe funkcjonalności:

System kont i logowania:

Rejestracja użytkownika tylko z wykorzystaniem slotu wygenerowanego przez administratora.

Potwierdzenie e-maila po rejestracji.

Logowanie przez JWT.

Role: admin i user.

Zarządzanie zadaniami:

Użytkownik wpisuje opis problemu lub zadania.

GPT tworzy strukturę zadania: tytuł, opis, termin, notatki itp.

Zadanie jest zapisywane do bazy MongoDB i widoczne w interfejsie.

Użytkownik może edytować atrybuty zadania (np. dodać termin).

Zamykanie zadań:

Użytkownik wpisuje, że zadanie zostało wykonane.

GPT odnajduje najbardziej pasujące zadanie na podstawie opisu.

Generuje podsumowanie zakończenia.

Użytkownik zatwierdza lub prosi o korektę opisu.

Przeszukiwanie historii zadań:

Użytkownik wpisuje opis problemu.

GPT przeszukuje semantycznie poprzednie zadania (zrealizowane).

Zwraca najbardziej podobne wraz z notatkami i metodami rozwiązania.

Integracja z OpenAI:

Użytkownik podaje swój własny klucz API OpenAI.

Obsługiwane są tylko konta z dostępem do GPT-4.

Klucz API przechowywany zaszyfrowany (np. AES).

Czyszczenie historii GPT:

Po zakończeniu rozmowy z GPT lub zamknięciu zadania, kontekst jest czyszczony.

Aplikacja nie zostawia historii rozmów na koncie OpenAI.

Dane i bazy:

MongoDB lokalnie + MongoDB Atlas (kopiowanie, backup).

Użytkownicy mają logicznie odseparowane dane (ownerId).

W przyszłości możliwość tworzenia organizacji i wspólnej bazy wiedzy wewnątrz firmy.

Przykładowy scenariusz użycia:

Użytkownik wpisuje: "Nie mogę się połączyć z zewnętrznym API, chyba chodzi o tokeny."

GPT wyszukuje w bazie podobne zadanie sprzed 2 miesięcy.

Odpowiada: "Wygląda na podobny przypadek: 'Integracja z API uczelni', gdzie trzeba było dodać nagłówek Authorization z JWT."

Użytkownik wykorzystuje znalezione informacje do szybszego rozwiązania problemu.

Technologie:

Frontend: React + TailwindCSS

Backend: Node.js (Express)

Baza danych: MongoDB (lokalnie i Atlas)

Autoryzacja: JWT, bcrypt

AI: OpenAI GPT-4 (z kluczem użytkownika)

Inne: dotenv, Mongoose, AES dla szyfrowania, middleware do kontroli rôl, łączenie logiki GPT z MongoDB

Cel promptu:

Ten prompt będzie wykorzystywany do współpracy z ChatGPT w trakcie tworzenia aplikacji. Chat powinien:

pomagać pisać kod (frontend/backend/API/modele)

sugerować architekturę folderów

doradzać w wyborze struktur danych

podpowiadać dobre praktyki zabezpieczeń i architektury

wspierać w integracji GPT z danymi użytkownika

pomagać debugować błędy i wspierać rozwój systemu

Docelowo system może być rozbudowany do poziomu firmowego narzędzia do zarządzania wiedza i rozwiązaniami, wspólnymi dla organizacji.

