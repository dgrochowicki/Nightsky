# Nightsky

Co dziś w nocy widać na niebie nad twoją lokalizacją — oś czasu wieczoru,
podstrony z warunkami widoczności i kalendarz roczny.

**Na żywo:** https://dgrochowicki.github.io/Nightsky/

## Stan

Prototyp. Jeden plik `index.html`, bez zależności i bez procesu budowania.
Otwierasz w przeglądarce i działa.

## Dokumentacja

Wszystko w jednym pliku: **[PROJEKT.md](PROJEKT.md)** — koncept i mechanika,
katalog zjawisk z warunkami, źródła danych, plan i dług techniczny, pomysły
odłożone wraz z powodem.

Sekcja 2 (katalog zjawisk) jest najbliżej kodu — jej zmiany pociągają zmiany
w aplikacji.

## Uruchomienie lokalnie

    python3 -m http.server 8000

Potem `http://localhost:8000`. Otwarcie przez `file://` też działa, ale
geolokalizacja wymaga `http://localhost` albo HTTPS.

## Tryb testowy

Najważniejsze stany aplikacji są rzadkie — Kp 7 zdarza się kilka razy w roku,
Perseidy raz. Wymusza się je parametrami w adresie:

    ?debug=1                    panel z suwakami
    ?kp=7.3                     silna zorza, reszta z prognozy
    ?kp=7.3&lo=10&hi=30         zorza przy czystym niebie
    ?date=2026-08-12            noc Perseidów
    ?lo=95                      stan „niebo zakryte"

Wartości nienadpisane dalej idą z prognozy, więc `?kp=7.3` pokazuje zorzę
przy dzisiejszych, prawdziwych chmurach. Pasek u dołu robi się bursztynowy,
kiedy jakakolwiek wartość jest wymuszona.

## Publikacja

GitHub Pages: Settings → Pages → Deploy from a branch → `main` / `root`.
