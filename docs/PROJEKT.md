# Nightsky — projekt

Notatki robocze. Jeden plik, żeby nie pielęgnować dokumentacji kosztem kodu.

---

## 1. Czym to jest

Aplikacja webowa, mobile-first, odpowiadająca na jedno pytanie: **co dziś w nocy
widać na niebie nad moją głową i czy warto wyjść.**

Nie jest atlasem nieba ani narzędziem dla astronomów ze sprzętem. Odbiorcą jest
ktoś z balkonem, kto przeczytał newsa o burzy słonecznej albo zobaczył jasny
punkt nad dachem i chce wiedzieć, co to.

### Mechanika: bramka widoczności

Sedno całej aplikacji. Każde zjawisko ma zestaw warunków; każdy jest spełniony,
częściowo spełniony albo nie. Z iloczynu wychodzi werdykt, a z najsłabszego
ogniwa — wyjaśnienie, dlaczego nie.

    werdykt = zjawisko × ciemność × chmury × księżyc × horyzont

Dodanie zjawiska to opisanie go, nie budowanie ekranu:

    {
      nazwa: 'Zorza polarna',
      zjawisko: () => zasiegOwalu(kp, szerokoscMagnetyczna),
      oknoCiemnosci: [-90, -12],     // wysokość Słońca w stopniach
      horyzont: 'N',
      wrazliwoscNaKsiezyc: 0.4
    }

### Kluczowa obserwacja

**Ciemność nie jest uniwersalnym plusem.** Meteory i zorza chcą jak najgłębszej
nocy. Obłoki srebrzyste świecą tylko przy Słońcu 6–16° pod horyzontem i w środku
nocy gasną. ISS wymaga, żeby stacja była w słońcu, a obserwator w cieniu. Halo
dzieje się w dzień i potrzebuje cirrusów, które innym zjawiskom przeszkadzają.

Ta sama noc bywa idealna dla jednego zjawiska i beznadziejna dla drugiego.
Konkurencja tego nie pokazuje, bo każda aplikacja robi jedno zjawisko.

Stąd język interfejsu na spokojne wieczory:
*„za ciemno na obłoki, za jasno na meteory, w sam raz na Jowisza".*

### Zasady projektowe

- **Werdykt przed danymi.** Odpowiedź jest zdaniem, nie liczbą. Nikt nie wie,
  czy 3% to dużo. Liczby niżej, dla tych, którzy chcą drążyć.
- **Głównym stanem jest „nie".** Dobre „nie" mówi: nie, dlaczego nie, i kiedy
  sprawdzić ponownie.
- **Zero pytań na wejściu.** Ruch jest skokowy — ludzie przychodzą po newsie
  i mają jedno pytanie oraz zero cierpliwości.
- **Offline first.** Awaria sieci nie psuje strony.
- **Żadnych zmyślonych liczb.** Jeśli model jest przybliżony, jest to napisane
  na ekranie, nie schowane w FAQ.

---

## 2. Katalog zjawisk

To jest konfiguracja aplikacji, nie opis. Każdy wiersz przekłada się na obiekt
w kodzie prawie jeden do jednego.

| Zjawisko | Okno ciemności | Inne warunki | Dane | Stan |
|---|---|---|---|---|
| Zorza polarna | < −12° | Kp vs szer. magnetyczna, horyzont N, księżyc | NOAA | gotowe |
| Roje meteorów | < −18° | wysokość radiantu, księżyc, kalendarz | liczone | gotowe |
| Obłoki srebrzyste | −6° do −16° | szerokość 50–70°, horyzont NW | liczone | gotowe |
| Księżyc | — | faza, wschód, zachód | liczone | gotowe |
| Okna ciemności | — | cztery etapy zmierzchu | liczone | gotowe |
| Planety | < −4° | wysokość > 10°, jasność | liczone | — |
| Droga Mleczna | < −18° | sezon III–X, brak księżyca, horyzont S, zaświetlenie | liczone | — |
| Przeloty ISS | < −6° | stacja oświetlona, obserwator w cieniu | TLE | — |
| Starlinki po starcie | < −6° | jak ISS, kilka dni po wyniesieniu | TLE | — |
| Halo, słońca poboczne | **dzień** | cirrusy 30–70%, Słońce 20–35° | Open-Meteo | — |
| Światło zodiakalne | < −18° | wiosną wieczorem, jesienią rano | liczone | — |
| Zaćmienie Księżyca | dowolna | Księżyc nad horyzontem | liczone | — |
| Koniunkcje | zmierzch wystarczy | oba obiekty > 10° | liczone | — |

**Droga Mleczna** — najmocniejszy kandydat po zorzy. Centrum Galaktyki ma stałe
współrzędne, więc liczy się jak każdy obiekt. Wymaga jedynego brakującego
warunku: zaświetlenia nieba. To też jedyny warunek, na który użytkownik może
wpłynąć — przejechaniem trzydziestu kilometrów.

**Halo dzienne** — jedyne zjawisko, któremu wysokie chmury pomagają. Najlepszy
dowód na tezę, że warunki nie są uniwersalne: te same cirrusy tej samej doby raz
są problemem, raz warunkiem koniecznym. Przy okazji aplikacja przestaje być
martwa między 8 a 20.

**Starlinki** — po każdym starcie lecą sznurkiem przez kilka dni i wywołują falę
pytań „co to było". Ten sam rachunek i te same dane co ISS.

**Świadomie pomijane** — zaćmienia Słońca (rzadkie i medialne, nikt nie
potrzebuje do nich aplikacji), gwiazdy zmienne i okultacje (teren dla hobbystów
ze sprzętem), komety (jasność nieprzewidywalna nawet dla zawodowców; jasna
kometa raz na kilka lat, wtedy wpisze się ją ręcznie w dziesięć minut).

---

## 3. Dane

**Zjawisk na niebie się nie pobiera — je się liczy.** Zaćmienia, koniunkcje,
opozycje, fazy Księżyca, przesilenia i roje są deterministyczne na tysiąc lat
w przód. Z sieci pochodzą tylko trzy rzeczy: **pogoda, aktywność geomagnetyczna
i elementy orbitalne satelitów.**

### Liczone lokalnie

Pozycja Słońca (zachód, cztery etapy zmierzchu, azymut), pozycja i faza
Księżyca, szerokość geomagnetyczna i próg Kp, wysokości radiantów i realna
liczba meteorów na godzinę, sezon obłoków z warunku 6–16°, długość nocy
astronomicznej dla każdego dnia roku.

Obecnie na własnym kodzie, na wzór SunCalc. Docelowo **Astronomy Engine**
(cosinekitty/astronomy) — planety, zaćmienia, koniunkcje, opozycje, przesilenia,
wschody i zachody dowolnego ciała, dokładność do minuty kątowej, działa
w przeglądarce. Jedna zależność zamiast pięciu API.

### Pobierane

| Dane | Endpoint | Klucz | CORS |
|---|---|---|---|
| Prognoza Kp | `swpc.noaa.gov/products/noaa-planetary-k-index-forecast.json` | nie | tak |
| Kp obserwowane | `swpc.noaa.gov/products/noaa-planetary-k-index.json` | nie | tak |
| OVATION (zorza) | `swpc.noaa.gov/json/ovation_aurora_latest.json` | nie | tak, 1,5 MB |
| Chmury warstwami | `api.open-meteo.com/v1/forecast` | nie | tak |
| Szukanie miast | `geocoding-api.open-meteo.com/v1/search` | nie | tak |
| Nazwa z GPS | `api.bigdatacloud.net/data/reverse-geocode-client` | nie | tak |
| TLE satelitów | Celestrak `gp.php?CATNR=25544` | nie | do sprawdzenia |

Kp leci z trzech źródeł po kolei. Parser przyjmuje oba kształty, w jakich NOAA
podaje te dane. Każde źródło osobno przez `Promise.allSettled`, żeby awaria
jednego nie kasowała drugiego.

**Pobieramy dane na godzinę zmierzchu astronomicznego, nie na „teraz".**
Aplikacja odpowiada na pytanie o tę noc, więc zachmurzenie o 15:00 jest
bezużyteczne — liczy się to, co będzie o 21:40.

### Backend

Nie jest potrzebny do podpięcia danych — wszystkie źródła mają CORS i nie
wymagają klucza. Staje się potrzebny przy: cache'owaniu OVATION, powiadomieniach
push, sięganiu po dane bez CORS-a (Global Meteor Network, IMO MeteorFlux).
Wtedy Cloudflare Workers z cronem, obok Pages.

### Biblioteki do dołożenia

**Astronomy Engine** (planety, zaćmienia, efemerydy) i **satellite.js**
(propagacja SGP4 dla ISS i Starlinków; `tle.js` opakowuje to przyjaźniej).

### Weryfikacja

Roje mają rzadką zaletę: ktoś je realnie mierzy. Global Meteor Network publikuje
bieżący strumień z sieci kamer, IMO wykresy ZHR z sieci wideo. Można pokazać
obok siebie „przewidywaliśmy 15/h" i „zmierzono 22/h". Przy zorzy nie ma
odpowiednika.

---

## 4. Plan

### MVP

To, co już działa, **minus jedna rzecz, która jest zmyślona.**

Do wycięcia: **ocena jakości zachodu słońca (0–100)**. Za tą liczbą nie stoi
żaden model meteorologiczny — to wzór napisany na poczekaniu. Wszystko inne jest
albo prawdziwą astronomią, albo prawdziwymi danymi. Zmyślona liczba szkodzi
bardziej niż brakująca funkcja, bo podważa zaufanie do tych prawdziwych. Godziny
złotej i niebieskiej godziny zostają — są policzone poprawnie.

### Kolejność po MVP

1. **Wspólna bramka widoczności.** Dziś chmury liczone wspólnie, a ciemność
   zaszyta osobno w każdym miejscu. Trzeba to odwrócić. Bez tego każde nowe
   zjawisko to nowy kod.
2. **Astronomy Engine zamiast własnej astronomii.** Poszerza aplikację
   i usuwa najwięcej ręcznie pisanego kodu, w którym mogą siedzieć błędy.
3. **Planety.** „Co to za jasna gwiazda" to najczęstsze pytanie laika. Pół dnia.
4. **Droga Mleczna.** Najbardziej spektakularna po zorzy, w całości offline.
5. **Przeloty ISS.** Jedyne zjawisko widoczne z centrum miasta. Weekend, nie
   wieczór — logika widzialności nie jest w żadnej bibliotece.

### Dług techniczny

- [ ] znacznik „teraz" na osi czasu
- [ ] własne adresy podstron — bez tego nie da się wysłać linku do konkretnej nocy
- [ ] kliknięcie w rój na wykresie roku prowadzi na jego podstronę
- [ ] przejście w bok między sąsiednimi wydarzeniami
- [ ] miesiące na wykresie roku nieczytelne, brak znacznika „dziś"
- [ ] sprawdzić, czy trafiamy we właściwą godzinę prognozy zachmurzenia

### Uczciwie o modelach

- Szerokość geomagnetyczna z przybliżenia dipolowego — odległość do granicy
  owalu jest orientacyjna, zaokrąglana do 50 km.
- Daty maksimów rojów stałe co do doby; dokładne godziny w UT publikuje IMO.
- Sezon zorzowy wokół równonocy podany z pamięci, nie z danych —
  **do zweryfikowania u źródła przed wejściem do produktu.**

---

## 5. Odłożone

Nie odrzucone — odłożone, z powodem. Powód jest ważniejszy niż pomysł.

**Tryb kamery z nakładką (AR).** Na iOS wymaga zgody przez `requestPermission()`
z gestu i HTTPS; Safari daje `webkitCompassHeading`, Chrome
`deviceorientationabsolute` — dwie ścieżki. Gorzej, że kompas myli się o 10–20°,
a to na niebie dwie rozpostarte dłonie. Do tego deklinacja magnetyczna, w Polsce
5–6° na wschód, do odjęcia ręcznie. Przy nakładce na kamerę ten błąd widać
natychmiast i wygląda jak błąd aplikacji. *Zamiast tego za 10% pracy: schemat
nieba w SVG obracany kompasem, bez kamery — tam błędu nie widać, bo nie ma obok
prawdziwego nieba do porównania.*

**Jak wskazać, gdzie patrzeć.** Bez kamery, rosnąco: „azymut 247°" bezużyteczne
→ „na zachodzie nisko" zrozumiałe, ale nieprecyzyjne → **„dwie pięści nad
horyzontem"** (pięść na wyciągniętej ręce to ~10°, dłoń ~20°) → **„w prawo
i poniżej Księżyca, półtorej pięści"**, najlepsze, bo nikt nie myli Księżyca
z niczym, a liczy się trywialnie.

**„Widzę coś jasnego, co to jest".** Nie wymaga kompasu — wystarczy zapytać.
Cztery przyciski z kierunkiem, trzy z wysokością, lista kandydatów po jasności.
Dziewięć na dziesięć razy odpowiedzią jest planeta albo ISS.

**Kalibracja prognoz.** Zapisywanie własnych przewidywań i porównywanie później.
Nikt z konkurencji nie publikuje, ile razy jego procent miał rację. Przy rojach
dostaje się to niemal za darmo dzięki GMN i IMO.

**Powiadomienia push.** Przy ruchu skokowym retencja nie ma znaczenia. Uwaga:
na iOS push z weba działa dopiero po dodaniu strony do ekranu głównego jako PWA.
To jest realna granica między wersją webową a natywną.
