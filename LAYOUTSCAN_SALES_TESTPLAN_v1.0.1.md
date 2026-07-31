# LayoutScan Sales v1.0.1 — Testplan & testresultater
**Nordic Operations · Pilot til Reoler A/S**
Dato: 2026-07-31

## Testmetode

Uændret fra tidligere versioner: HTML-validering (`html5lib`), JavaScript-
syntakskontrol (`node --check`), funktionel simulering i en simuleret DOM (jsdom), og
uafhængig DXF-validering med `ezdxf`. Fortsat ingen adgang til en rigtig browser i
dette miljø.

## Fuld automatiseret testkørsel — 36 tests, alle bestået

| Test | Resultat |
|------|----------|
| **Fejlrettelse — kopi i stedet for flyt**: placering af et Kontor afslutter automatisk placeringstilstanden; et efterfølgende tryk på det samme sted vælger/flytter objektet i stedet for at oprette en kopi | ✅ |
| Enkelt-placeringstype (væg) viser simpel besked uden "bliv i tilstand"-knap | ✅ |
| Gentagelig type (port) viser fortsat "✓ Placering aktiv" med "Afslut placering" | ✅ |
| **Regressionstest — reol-placeringsfejl**: manuel tryk-for-at-placere af en reol giver korrekte x/y/bays-værdier (fanger den fundne argument-rækkefølge-fejl) | ✅ |
| **Palletyper**: Udtræksskuffer kan vælges som primær, EUR-palle kan vælges som sekundær, kapacitetsestimatet vises | ✅ |
| Primær + sekundær truck, blandet reolvalg, konfliktpanel, automatisk rettelse (uændrede funktioner) | ✅ (ingen regression) |
| Vedvarende placeringstilstand for reoltype, touch-pointer, mobil/tablet panel-toggle | ✅ |
| DXF-eksport (uændret generator, gen-valideret) | ✅ |
| PDF-rapport (v1.0-struktur: Forside/Resumé/Layout/Nøgletal/AI/Fotos/Materialeliste/Noter) | ✅ |
| Fotomodul, AI-analyse (Rådgiver/Kundespørgsmål/Risikovurdering), projektnoter | ✅ |
| Demoprojekt, JSON-rundtur, undo/redo, alle eksportformater | ✅ |

## Specifikt for denne gennemgang

**Fejlfinding ud over det rapporterede:** Under arbejdet med at rette den rapporterede
"kopi i stedet for flyt"-fejl blev endnu en fejl fundet: manuel placering af en reol
via værktøjspaletten kaldte `createRackObject(...)` med parametrene i forkert
rækkefølge (en rest fra da blandede reolsystemer blev indført i v0.2.2), hvilket ville
have givet en reol med et objekt (reolvalget) som x-koordinat i stedet for et tal.
Dette var ikke opdaget af den tidligere automatiserede testsuite, fordi ingen
eksisterende test dækkede netop denne kombination (manuel tryk-for-at-placere af en
reol, til forskel fra den automatiske layoutgenerering, som bruger en anden kodesti
med korrekt parameterrækkefølge). En ny, dedikeret regressionstest er tilføjet for at
sikre, at denne kodesti ikke går i stykker igen.

**Testisolation:** De to nye tests for placeringsadfærd (Kontor-testen og
reol-regressionstesten) rydder selv op efter sig (fjerner de objekter, de opretter),
så de ikke påvirker senere, uafhængige tests som den isolerede
automatisk-ret-test — en lære fra tidligere testkørsler i dette projekt, hvor
manglende oprydning gav falske fejl.

## Uafhængig DXF-gentest (ezdxf)

DXF-generatoren er uændret i denne version. Gen-valideret efter ændringerne: alle 9
påkrævede lag til stede, korrekt geometri, filen parses uden fejl af `ezdxf`.

## Ikke testet i dette miljø — kræver fysiske enheder/browsere

Som ved alle tidligere versioner: iPad Safari, Edge, Chrome, og en rigtig
AutoCAD-installation kunne ikke testes fysisk. Dette gælder i særlig grad denne
omgangs vigtigste rettelse — det anbefales stærkt at en sælger afprøver
Placér → Vælg → Flyt → Redigér-flowet på en fysisk iPad, før pilotstart, da det var
netop dette flow, der var i stykker.

## Konklusion

Den kritiske, rapporterede fejl er rettet og grundigt testet, inklusive en beslægtet
fejl som blev fundet undervejs. Palletyper, kapacitetsestimat, forklarende tekster og
brugerdefineret-truck-præcisering er implementeret og testet. "Brug eksisterende
tegning" (punkt 8) er bevidst ikke implementeret i denne omgang — se leveringsnotens
begrundelse — og forbliver en udestående opgave til en senere version.
