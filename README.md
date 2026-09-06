# niebo

Co dziś widać na niebie nad twoją lokalizacją — oś czasu nocy, podstrony ze
szczegółami i kalendarz roczny.

## Stan

Prototyp. Jeden plik `index.html`, bez zależności i bez procesu budowania.
Otwierasz w przeglądarce i działa.

## Co się liczy lokalnie

Bez żadnego zapytania sieciowego:

- pozycja Słońca — zachód, zmierzch cywilny, żeglarski i astronomiczny, azymut
- pozycja i faza Księżyca — wschód, zachód, procent oświetlenia
- szerokość geomagnetyczna i próg Kp dla widoczności zorzy
- wysokość radiantów rojów meteorów i realna liczba meteorów na godzinę
- długość nocy astronomicznej dla każdego dnia roku

Algorytmy Słońca i Księżyca przepisane inline na wzór SunCalc.

## Co wymaga sieci

| Dane | Źródło | Klucz | CORS |
|---|---|---|---|
| Prawdopodobieństwo zorzy (OVATION) | `services.swpc.noaa.gov/json/ovation_aurora_latest.json` | nie | tak |
| Kp i prognoza Kp | `services.swpc.noaa.gov/products/` | nie | tak |
| Zachmurzenie, warstwy chmur | `api.open-meteo.com/v1/forecast` | nie | tak |

W prototypie te wartości są na suwakach w panelu podglądu na dole strony.

## Uruchomienie lokalnie

    python3 -m http.server 8000

Potem `http://localhost:8000`. Otwarcie pliku przez `file://` też działa,
ale geolokalizacja wymaga `http://localhost` lub HTTPS.

## Publikacja

GitHub Pages: Settings → Pages → Deploy from a branch → `main` / `root`.

## Do zrobienia

- [ ] znacznik „teraz" na osi czasu
- [ ] własne adresy podstron, żeby dało się wysłać link do konkretnej nocy
- [ ] kliknięcie w rój na wykresie roku prowadzi na jego podstronę
- [ ] wybór lokalizacji zamiast wpisanego na sztywno Szczecina
- [ ] podpięcie prawdziwych danych zamiast suwaków
- [ ] przeloty ISS (TLE z Celestrak + SGP4)
- [ ] pozycje planet

## Uczciwie o modelach

Ocena jakości zachodu słońca to wzór przybliżony, nie model meteorologiczny.
Szerokość geomagnetyczna liczona z przybliżenia dipolowego, więc odległość do
granicy owalu zorzy jest orientacyjna. Daty maksimów rojów są stałe co do doby;
dokładne godziny w UT publikuje co roku IMO.

## Źródła danych

- NOAA SWPC — model OVATION Prime, indeks Kp
- Open-Meteo — prognoza zachmurzenia
- IMO — kalendarz rojów, współrzędne radiantów
