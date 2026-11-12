# kAPItanIT — integracja z API Allegro

Dokumentacja aplikacji **kAPItanIT** zarejestrowanej w Allegro Developer Apps.

| | |
|---|---|
| Właściciel | EMKL Sp. z o.o. |
| Charakter | aplikacja prywatna i wewnętrzna: obsługuje wyłącznie konta Allegro właściciela, nie jest dystrybuowana ani udostępniana innym firmom |
| Użytkownicy | wyłącznie pracownicy właściciela, po zalogowaniu |
| Autoryzacja | OAuth 2.0, Authorization Code |

## Do czego aplikacja używa API Allegro

| Uprawnienie | Zastosowanie |
|---|---|
| `allegro:api:orders:read`, `allegro:api:orders:write` | pobieranie zamówień, aktualizacja statusu realizacji i numerów przesyłek |
| `allegro:api:sale:offers:read`, `allegro:api:sale:offers:write` | odczyt ofert, wystawianie i edycja ofert, aktualizacja stanów magazynowych |
| `allegro:api:sale:settings:read`, `allegro:api:sale:settings:write` | ustawienia sprzedaży potrzebne przy wystawianiu ofert |
| `allegro:api:messaging` | Centrum Wiadomości: wiadomości od kupujących i odpowiedzi |
| `allegro:api:disputes` | dyskusje i reklamacje |
| `allegro:api:payments:read`, `allegro:api:payments:write` | historia płatności, zwroty wpłat po decyzji pracownika |
| `allegro:api:billing:read` | odczyt opłat i salda konta |
| `allegro:api:shipments:read`, `allegro:api:shipments:write` | przesyłki „Wysyłam z Allegro”: nadawanie, etykiety, protokoły |

## Allegro Ads API (Beta)

**Etap 1 — odczyt i raporty.** W tym etapie aplikacja wyłącznie odczytuje dane i zamawia raporty:

- `GET /ads/reseller/advertisers` — reklamodawca przypisany do każdego konta,
- odczyt kampanii i grup reklam Sponsored Offers, Sponsored Display i Sponsored Video,
- `POST /ads/advertisers/{advertiserId}/reports` i pobranie gotowego raportu — dzienne raporty `standard`
  i `purchasedOffers` zamiast ręcznego eksportu z panelu Allegro Ads,
- `POST /ads/reseller/advertisers/{advertiserId}/offer-queries` — sprawdzenie, czy oferta może być reklamowana
  i jaka jest minimalna stawka.

**Etap 2 — zmiany wykonywane wyłącznie przez pracownika w interfejsie aplikacji:**

- stawki CPC, budżety dzienne i całkowite, statusy kampanii i grup reklam (z nagłówkiem `If-Match`),
- dodawanie i usuwanie ofert w grupach reklam.

W etapach 1 i 2 harmonogram aplikacji nie wprowadza żadnych zmian w kampaniach.

**Zasady techniczne:**

- nagłówki `Accept` i `Content-Type`: `application/vnd.allegro.beta.v1+json`,
- własny limit zapytań po stronie aplikacji; po odpowiedzi 429 przerwa co najmniej minutę,
- walidacja żądań przed wysłaniem; pola spoza specyfikacji nie są wysyłane,
- raporty odczytywane po nazwach kolumn; parametr `humanReadableReport` nie jest używany.

## Bezpieczeństwo

- Aplikacja nie prosi o login ani hasło do Allegro; dostęp wyłącznie przez OAuth 2.0.
- Tokeny są szyfrowane w spoczynku, a klucz szyfrujący jest przechowywany poza bazą danych.
- Token odświeżany jest automatycznie; interfejs aplikacji nigdy nie zwraca tokenów.
- Każde żądanie ma nagłówek `User-Agent` z nazwą i wersją aplikacji.
- Funkcje są dostępne według ról użytkowników, a każda zmiana trafia do dziennika audytu (kto, kiedy, co).

## Cofnięcie dostępu

Właściciel konta Allegro może w każdej chwili cofnąć zgodę dla aplikacji po stronie Allegro. Tokeny tracą wtedy
ważność i aplikacja nie może już wykonywać zapytań w imieniu tego konta.

---

Ostatnia aktualizacja: 12.11.2026
