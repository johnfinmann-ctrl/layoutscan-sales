# LayoutScan Sales v0.2.1 — Pilot Release Candidate — Testplan & testresultater
**Nordic Operations · Pilot til Reoler A/S**
Dato: 2026-07-29

## Testmetode

Samme tre-lags metode som ved tidligere versioner (HTML-validering, JavaScript-
syntakskontrol, funktionel simulering via jsdom), suppleret med to nye teknikker
specifikt til dette tillæg:

1. **Ægte `PointerEvent`-simulering** med `pointerType:"touch"` mod selve
   `<canvas>`-elementet, for at bekræfte at tryk-for-at-placere rent faktisk fungerer
   med et touch-lignende input, ikke kun med museklik.
2. **Uafhængig DXF-validering** med `ezdxf` (et anerkendt, open source Python-bibliotek
   til at læse/skrive DXF-filer) — den genererede DXF-fil læses tilbage af en helt
   separat kodebase end den, der genererede den, hvilket er en langt stærkere test end
   blot at "parse sin egen output".

Der er fortsat **ingen adgang til en rigtig browser** i dette miljø (Chrome-download er
blokeret), så de fysiske enheds- og browsertest fra tillæggets punkt 13 kunne ikke
gennemføres.

## Fuld automatiseret testkørsel — resultat (24 tests, alle bestået)

| Test | Resultat |
|------|----------|
| Startskærm, nyt projekt, kundenavn, lagermål, "Opret lager" | ✅ |
| Trin 3: truckvalg | ✅ |
| Trin 4: producent-fane + reolvalg, Metalsistem-serier forbliver låst | ✅ |
| Layoutskærm initialiseret, paletter befolket | ✅ |
| Port på ydervæg renderes uden fejl (væghul + blå stil + ikon) | ✅ |
| Wow-overlay: aktiveres, viser fremdriftstekst, afsluttes, genererer reoler | ✅ (4 rækker / 40 fag / 640 pallepladser, 30×20 m testscenarie) |
| Reol-objekter bærer producent/produktserie/status-metadata | ✅ |
| **Mobil/tablet panel-toggle:** åbn/luk palette og egenskabspanel, kun ét panel ad gangen, baggrundsklik lukker | ✅ |
| **Tryk-for-at-placere banner** vises korrekt med objektnavn og skjules ved annullering | ✅ |
| **Ægte touch-pointerhændelse** (`PointerEvent` med `pointerType:"touch"`) placerer et objekt på tegnefladen | ✅ |
| **DXF-eksport:** velformet gruppekode-par, alle 9 påkrævede lag til stede, `$INSUNITS` sat, filen afsluttes korrekt med `EOF` | ✅ |
| **Automatisk anbefaling:** genereres, indeholder aldrig ordet "AI" | ✅ |
| Konfliktkontrol, nulstil forslag, regenerering | ✅ |
| Resultatside (Trin 6): KPI'er, ny anbefalingssektion, stykliste | ✅ |
| PDF-rapport: præcis 5 sider, nu inkl. anbefaling på side 5 | ✅ |
| Alle eksportfunktioner (JSON, CSV, Excel, CAD-CSV, PNG) | ✅ |
| JSON-rundtur, demoprojekt, undo/redo | ✅ |

## Uafhængig DXF-validering med ezdxf

Et testprojekt (30×20 m hal, en port, en søjle, dobbelt pallereol-forslag) blev
eksporteret til DXF og læst tilbage med `ezdxf.readfile()`:

- Filen blev parset **uden fejl**.
- Alle 9 påkrævede lag blev fundet: `WALLS, PORTS, DOORS, RACKS, COLUMNS, ROOMS, FIRE,
  TEXT, DIMENSIONS` (samt AutoCAD's egne standardlag `0` og `Defpoints`, som altid
  tilføjes automatisk).
- Entitetstælling: 10 `POLYLINE` (4 vægge + 1 port + 1 søjle + 4 reolrækker), 13 `TEXT`,
  2 `LINE` (mållinjer).
- Stikprøvekontrol af hjørnekoordinater på østvæggen bekræftede korrekt, målfast
  geometri efter Y-akse-spejlingen.

Dette beviser, at filen er **syntaktisk gyldig DXF, som en uafhængig, anerkendt
DXF-parser kan læse korrekt**. Det beviser ikke, at en konkret AutoCAD-version vil
importere den problemfrit — det bør bekræftes som en sidste, fysisk kontrol, før filen
sendes til Arne Andersen i praksis.

## Ikke testet i dette miljø — kræver fysiske enheder/browsere

Følgende fra tillæggets punkt 13 er **ikke** og kan ikke være bekræftet her:

- ✗ Chrome Windows, Edge Windows, Chrome Mac, Safari Mac
- ✗ Safari iPad, Safari iPhone, Android Chrome
- ✗ Faktisk drag & drop-følelse med finger eller Apple Pencil på en fysisk iPad
- ✗ Om DXF-filen reelt åbner problemfrit i en rigtig AutoCAD-installation
- ✗ Visuel bekræftelse af, at der aldrig opstår vandret scrolling på en fysisk
  telefon/tablet i praksis (kun logisk/strukturelt verificeret via CSS-gennemgang)

## Konklusion

Alle automatiserbare aspekter af tillægget — touch/pointer-hærdning, responsivt
paneldesign, tryk-for-at-placere-tilbagemelding, den ægte DXF-eksport og den
regelbaserede anbefaling — er verificeret grundigt med de værktøjer, der er
tilgængelige i dette miljø, herunder en reel, uafhængig DXF-parser. Den afgørende,
resterende test er en kort, fysisk gennemgang på en Chrome/Edge/Mac-maskine, en iPad og
helst også i en rigtig AutoCAD-installation, før løsningen er klar til brug hos Reoler
A/S i praksis.
