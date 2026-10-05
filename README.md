# Butla – dziennik winiarski

Jednoplikowa aplikacja (`index.html`, bez frameworka) do prowadzenia partii wina:
roczniki, dane nastawu, etapy fermentacji, fuzje, historia zmian z autorem.
Dane trzyma Firebase (Auth + Firestore, projekt `butla-91912`, baza `eur3`), strona stoi na GitHub Pages: `https://guldorf.github.io/butla/`.

## Dane w Firestore

| Kolekcja | Zawartość |
|---|---|
| `users/{uid}` | `login`, `name`, `role` (`admin`/`user`), `color` |
| `batches/{id}` | `name`, `year`, `fruit`, `cap`, `vol`, `yeast`, `yeastG`, `nutG`, `pulpaL`, `sugarKg`, `waterL`, `stage`, `color` |
| `history/{id}` | `batchId`, `bn` (nazwa partii), `d` (data `RRRR-MM-DD`), `t` (treść), `ts`, `uid` (autor) |
| `meta/setup` | `adminUid` – znacznik, że konto administratora już istnieje |

Login zamieniany jest na adres `login@butla.app` (to tylko identyfikator w Firebase, poczta nie istnieje).

## Konfiguracja Firebase (raz)

1. [console.firebase.google.com](https://console.firebase.google.com) → **Add project** → nazwa `butla` (Analytics można wyłączyć).
2. **Build → Authentication → Get started → Sign-in method → Email/Password → Enable** (bez „Email link”).
3. **Authentication → Settings → Authorized domains → Add domain** → `guldorf.github.io`.
4. **Build → Firestore Database → Create database** → lokalizacja `eur3` (lub `europe-central2`) → *production mode*.
5. **Firestore → Rules** → wklej zawartość `firestore.rules` → **Publish**.
6. **Project settings (koło zębate) → Your apps → Web (`</>`)** → nazwa `butla` → skopiuj obiekt `firebaseConfig`.
7. W `index.html` wpisz skopiowany obiekt w stałą `FIREBASE_CONFIG` (dla `butla-91912` już wpisany), np.
   ```js
   const FIREBASE_CONFIG = { apiKey: "…", authDomain: "butla-xxxx.firebaseapp.com", projectId: "butla-xxxx", … };
   ```
   Klucz `apiKey` nie jest tajny – dostęp chronią reguły Firestore i logowanie.
   Bez wpisanej konfiguracji strona pokazuje ekran, w którym można ją wkleić (zapisuje się wtedy tylko w tej przeglądarce).

## Pierwsze uruchomienie

1. Wejdź na stronę – pojawi się **Pierwsze uruchomienie**. Załóż konto administratora (login, imię, hasło).
2. Klik w swoje imię (prawy górny róg) → **Dodaj użytkownika**: login, imię, hasło. Przekaż je tej osobie.
   Hasło każdy może potem zmienić sam w tym samym oknie.
3. **Odbierz dostęp** usuwa dokument `users/{uid}` – konto w Authentication zostaje, ale nie ma już wstępu.
   Jeśli chcesz je skasować całkiem: Firebase → Authentication → Users.
4. **Importuj dane** przyjmuje eksport z wersji claude.ai, ze starszej wersji (localStorage) i z tej wersji.

## GitHub Pages

Repozytorium `guldorf/butla`, gałąź `main`, katalog główny → **Settings → Pages → Deploy from a branch → main / (root)**.
