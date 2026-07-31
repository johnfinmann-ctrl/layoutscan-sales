# LayoutScan Sales v1.0 — Final Pilot Release — Testplan & testresultater
**Nordic Operations · Pilot til Reoler A/S**
Dato: 2026-07-29

## Testmetode

Uændret fra tidligere versioner: HTML-validering (`html5lib`), JavaScript-
syntakskontrol (`node --check`), og funktionel simulering af hele brugerflowet i en
simuleret DOM (jsdom) med ægte hændelsesobjekter. DXF-eksporten er desuden
uafhængigt valideret med `ezdxf` (et rigtigt, separat DXF-parsing-bibliotek). Der er
fortsat **ingen adgang til en rigtig browser** i dette miljø (Chrome-download er
blokeret), hvilket er den vigtigste begrænsning ved denne test — se afsnittet
"Ikke testet" nedenfor for præcis hvad det betyder for punkt 10 i opgaven.

## Fuld automatiseret testkørsel — 32 tests, alle bestået

| Test | Resultat |
|------|----------|
| Fuldt 6-trins forløb (uændret arbejdsgang): kunde, lager, drift, reoler, layout, resultat | ✅ |
| Primær + sekundær truck, blandet reolvalg, konfliktpanel, automatisk rettelse | ✅ (alle uændrede funktioner fra v0.2.2 fortsat bestået — ingen regression) |
| Vedvarende placeringstilstand, touch-pointer, mobil/tablet panel-toggle | ✅ |
| DXF-eksport (uændret generator) | ✅ |
| **PDF-rapport: dynamisk sideantal og ny v1.0-struktur** — mindst 8 sider, indeholder "Projektresumé", "AI-vurdering" og "Fotos" som separate sider, korrekt dynamisk sidetal ("Side N / N" på sidste side) | ✅ |
| **Fotomodul**: tilføj foto med titel/kommentar/dato/fotograf, thumbnail vises, titel og kommentar optræder korrekt i PDF'en, sletning fungerer | ✅ |
| **AI-analyse**: AI Rådgiver, Kundespørgsmål og Risikovurdering returnerer alle indhold; teksten indeholder aldrig referencer til cloud/online/eksterne AI-tjenester | ✅ |
| **AI-modal**: åbner korrekt og viser alle tre sektioner samtidig | ✅ |
| **Projektnoter**: gemmes til `project.notes`, kommer med i PDF'en | ✅ |
| Fotos og noter overlever JSON-eksport/genindlæsning (separat rundtursverificering) | ✅ |
| Demoprojekt, JSON-rundtur, undo/redo, alle eksportformater | ✅ |

## Specifikt for v1.0

**Uændret arbejdsgang bekræftet:** Testen gennemfører det samme 6-trins forløb som i
alle tidligere versioner (Kunde → Lager → Drift → Reoler → Layout → Resultat) uden
nogen ændringer i trin-rækkefølgen. Fotos, AI-analyse og noter tilgås via separate
knapper og påvirker ikke selve trin-flowet — bekræftet ved, at hele det eksisterende
testforløb stadig består uændret.

**PDF-struktur:** `buildPrintReportHtml()` blev testet til at generere mindst 8 sider
(Forside, Projektresumé, Layout, Nøgletal, AI-vurdering, Fotos, Materialeliste,
Noter), med korrekt dynamisk sidetal frem for det tidligere faste "Side X / 5".

**AI-indholdets pålidelighed:** Der er specifikt testet for, at hverken AI
Rådgiver-teksten, kundespørgsmålene eller risikovurderingen nogensinde nævner "cloud",
"online", "server" eller lignende — for at bekræfte at implementeringen reelt er den
lokale, regelbaserede analyse, der er beskrevet i leveringsnoten, og ikke ved en fejl
antyder ekstern databehandling.

## Uafhængig DXF-gentest (ezdxf)

DXF-generatoren er uændret fra v0.2.1 Pilot RC, som instrueret. Den blev alligevel
gen-valideret efter denne opdatering for at udelukke utilsigtede sideeffekter fra de
øvrige ændringer: filen blev igen parset korrekt af `ezdxf`, med alle 9 påkrævede lag
(WALLS, PORTS, DOORS, RACKS, COLUMNS, ROOMS, FIRE, TEXT, DIMENSIONS) og korrekt,
målfast geometri.

## Ikke testet i dette miljø — kræver fysiske enheder/browsere (punkt 10)

Følgende er **ikke og kan ikke være** bekræftet i dette miljø, da det kræver en rigtig
browser og fysiske enheder:

- ✗ iPad Safari — herunder om fotomodulets kamera-/filvælger-integration fungerer
  korrekt på en fysisk iPad, og om lysboksen og AI-modalen ser rigtige ud
- ✗ Edge, Chrome — generel visuel og funktionel bekræftelse
- ✗ Om den færdige PDF rent faktisk udskrives/gemmes korrekt fra en browsers
  print-til-PDF-funktion, med korrekte sideskift og billedkvalitet
- ✗ Om en rigtig AutoCAD-installation kan åbne DXF-filen problemfrit (kun bekræftet
  med en uafhængig DXF-parser, `ezdxf`, ikke med AutoCAD selv)

**Dette er den vigtigste udestående opgave, før v1.0 er klar til reel pilotbrug hos
Reoler A/S.** Al forretningslogik, dataintegritet og struktur er grundigt verificeret
automatisk, men den endelige, fysiske gennemgang på rigtige enheder — herunder
sammen med Arne Andersen og en rigtig AutoCAD-installation — er ikke gennemført og
anbefales som det allersidste skridt før pilotstart.

## Konklusion

Alle ni funktionsområder i v1.0 — fotodokumentation, AI Rådgiver, AI Kundespørgsmål,
AI Risikovurdering, projektnoter, det udvidede projektresumé, den omstrukturerede
PDF, fraværet af unødig kompleksitet, og uændret DXF — er verificeret til at fungere
korrekt uden JavaScript-fejl og uden regression i den eksisterende, uændrede
arbejdsgang. Den afgørende, resterende test er en fysisk gennemgang på PC, Mac, iPad,
Android og i en rigtig AutoCAD-installation.
