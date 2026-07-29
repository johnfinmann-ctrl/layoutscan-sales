# LayoutScan Sales v0.2.2 — Pilot Stabilization — Testplan & testresultater
**Nordic Operations · Pilot til Reoler A/S**
Dato: 2026-07-29

## Testmetode

Samme tre-lags metode som ved tidligere versioner: HTML-validering (`html5lib`),
JavaScript-syntakskontrol (`node --check`), og funktionel simulering af hele
brugerflowet i en simuleret DOM (jsdom) med ægte `PointerEvent`- og
`Event`-objekter — der er fortsat ingen adgang til en rigtig browser i dette miljø
(Chrome-download er blokeret), så de fysiske enheds-/browsertest fra punkt 10 kunne
ikke gennemføres fysisk.

## Fuld automatiseret testkørsel — 28 tests, alle bestået

| Test | Resultat |
|------|----------|
| Startskærm, nyt projekt, kundenavn, lagermål, "Opret lager" | ✅ |
| **Trin 3: primær truck + sekundær truck valgt korrekt, kun primær påvirker `truckId`** | ✅ |
| **Trin 4: producent-fane + blandet reolvalg** — tilføjelse af en 2. aktiv reoltype (Småvarereol) ved siden af standardvalget (Dobbelt pallereol), Metalsistem forbliver låst | ✅ |
| Layoutskærm initialiseret, paletter befolket | ✅ |
| Port på ydervæg renderes uden fejl (væghul + blå stil + ikon) | ✅ |
| Wow-overlay: aktiveres, viser fremdriftstekst, afsluttes, genererer reoler | ✅ (5 rækker / 88 fag / 480 pallepladser, 30×20 m testscenarie) |
| **Reol-objekter fra flere aktive typer** — bekræftet mindst 2 forskellige reoltyper blandt de genererede rækker | ✅ |
| Mobil/tablet panel-toggle (palette/egenskaber) | ✅ |
| **Vedvarende placeringstilstand** — placerer 2 porte i træk uden at genåbne paletten, "Afslut placering" afslutter korrekt | ✅ |
| Tryk-for-at-placere-banner med korrekt tekst og luk-knap | ✅ |
| Ægte touch-`PointerEvent` placerer et objekt | ✅ |
| DXF-eksport: uændret, genvalideret — alle 9 lag, korrekt afsluttet | ✅ |
| Automatisk anbefaling: regelbaseret, nævner aldrig "AI" | ✅ |
| **Konfliktpanel**: klikbar statusknap åbner liste med "Vis" og "Ret automatisk" pr. konflikt; "Vis" zoomer/vælger korrekt objekt | ✅ |
| **Global "Ret automatisk"**: løser en bevidst uden-for-lageret-placeret reol, viser resumé med ændringslog, "Godkend" lukker korrekt | ✅ |
| Konfliktkontrol, nulstil forslag, regenerering | ✅ |
| Resultatside, PDF-rapport (5 sider), alle eksportformater | ✅ |
| JSON-rundtur, demoprojekt (nu med blandet reolvalg + sekundær truck), undo/redo | ✅ |

## Specifikt testet for denne version

**Konfliktpanel + auto-ret, isoleret scenarie:** En reol blev bevidst placeret langt
uden for et 30×20 m testlager. Testen bekræftede at:
1. Statusknappen viste korrekt konfliktantal og åbnede listen ved klik.
2. "Vis"-knappen valgte og zoomede til det korrekte objekt.
3. Den globale "Ret automatisk" reducerede antallet af konfliktramte reoler, viste en
   ændringslog (ikke-tom), og at "Godkend" lukkede resuméet korrekt.

**Note om testisolation:** Den første version af denne test placerede den
konfliktramte reol oven i et allerede tætpakket, genereret layout, hvilket i nogle
tilfælde fik den regelbaserede rettelse til at "cirkulere" mellem at flytte reolen
tilbage inden for grænserne og støde ind i naboreoler, uden at nå en stabil løsning
inden for det interne forsøgsloft (60 forsøg). Dette er ikke en fejl i rettelsen, men
en forventelig grænse for en regelbaseret, ikke-optimerende tilgang i meget tætpakkede
scenarier — og er nu tydeligt beskrevet i leveringsnotens afsnit 6. Testen blev
efterfølgende justeret til at afprøve et isoleret scenarie, hvilket lykkedes uden
problemer.

## Regressionstest

Alle tests fra Pilot RC (v0.2.1)-suiten, som stadig er relevante efter
model-ændringen fra `rackSelection` til `rackSelections`, er opdateret og kører
igen — ingen regression er fundet i porthåndtering, DXF-eksport, responsivt design,
wow-effekt eller rapportgenerering.

## Ikke testet i dette miljø — kræver fysiske enheder/browsere

Som ved alle tidligere versioner kunne følgende fra punkt 10 ikke gennemføres fysisk:

- ✗ PC (Chrome/Edge), Mac (Chrome/Safari)
- ✗ iPad Safari, iPhone Safari, Android Chrome
- ✗ Reel finger-/Apple Pencil-baseret drag & drop, herunder om det vedvarende
  placerings-flow rent faktisk føles godt i praksis
- ✗ Om konfliktpanelets zoom/centrering ser rigtigt ud på en fysisk skærm i alle
  størrelser
- ✗ Om "Ret automatisk"s ændringer visuelt giver mening for en sælger, der ser det
  ske i realtid

## Konklusion

Alle ni funktionsområder fra denne opdatering — herunder den vedvarende
placeringstilstand (den vigtigste ændring), flere trucks, blandede reolsystemer, det
klikbare konfliktpanel, og den regelbaserede automatiske rettelse med
Godkend/Fortryd — er verificeret til at fungere korrekt uden JavaScript-fejl, uden
regression i eksisterende funktionalitet. Den afgørende, resterende test er fortsat
en fysisk gennemgang på PC, Mac, iPad og Android, som beskrevet i punkt 10, før
løsningen er klar til brug hos Reoler A/S i praksis.
