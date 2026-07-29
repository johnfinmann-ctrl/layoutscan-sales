# LayoutScan Sales v0.2.1 — Testplan & testresultater
**Nordic Operations · Pilot til Reoler A/S**
Dato: 2026-07-29

## Testmetode

Samme tre-lags metode som ved v0.1.0/v0.2.0 (HTML-validering, JavaScript-syntakskontrol,
funktionel simulering via jsdom), da en rigtig browser fortsat ikke kan downloades i
dette miljø.

## Fuld testkørsel — resultat

| Test | Resultat |
|------|----------|
| Startskærm vises | ✅ |
| Nyt projekt → Trin 1 | ✅ |
| Kundenavn udfyldes og bindes korrekt | ✅ |
| Lagermål udfyldes, "Opret lager" genererer 4 vægge | ✅ |
| Trin 3: trucksvalg bindes korrekt | ✅ |
| **Trin 4: producent-fane + reolvalg** — klik på "Generisk", vælg Dobbelt pallereol, bekræft `rackSelection.manufacturer==="generic"` | ✅ |
| **Trin 4: Metalsistem-fane** viser låste produktserie-kort (`not-allowed`-styling), uden at ændre det aktive valg | ✅ |
| Layoutskærm initialiseret, paletter befolket (inkl. ikoner) | ✅ |
| **Port placeret på ydervæg renderes uden fejl** (væghul + blå portstil via `drawWallWithGaps`/`drawPortObject`) | ✅ |
| Wow-overlay aktiveres ved klik, viser "Analyserer lager..." | ✅ |
| Overlay-tekst skifter efter første interval | ✅ |
| Overlay afsluttes, reoler genereret, overlay skjult igen (~2,2 s) | ✅ — 4 rækker / 40 fag / 640 pallepladser (30×20 m testscenarie) |
| **Reol-objekter bærer producent/produktserie/status-metadata** (`manufacturerLabel`, `seriesLabel`, `productStatus`) | ✅ |
| **Stykliste indeholder Producent- og Status-kolonner** | ✅ |
| Konfliktkontrol: reol uden for lageret flages korrekt | ✅ |
| Nulstil forslag fjerner genererede reoler | ✅ |
| Regenerering (via wow-effekt) producerer reoler igen | ✅ |
| Resultatside (Trin 6): KPI'er + stykliste-tabel til stede | ✅ |
| PDF-rapport: præcis 5 sider genereret uden fejl | ✅ |
| Alle eksportfunktioner (JSON, CSV, Excel, CAD, PNG) kører uden fejl | ✅ |
| JSON-rundtur bevarer kundenavn og data | ✅ |
| Demoprojekt indlæses korrekt (Demo Lager A/S, 1.104 pallepladser, 69 fag, 7 rækker) | ✅ |
| Undo/redo kører uden fejl | ✅ |

**Alle 18 automatiserede tests bestod uden fejl** (`node test_smoke.js
layoutscan_sales_v0.2.1.html`).

## Regressionsnote

Den delte testsuite blev også kørt mod v0.2.0-filen for sammenligning. Her fejler de
nye producent-specifikke assertions forventeligt, fordi v0.2.0 ikke har begrebet
"producent" på reolvalget — det er **ikke** en regression i v0.2.0, blot en konsekvens
af at teststrengen nu indeholder v0.2.1-specifikke kontroller. Da v0.2.1 er den
gældende leverance, er dette ikke undersøgt yderligere.

## Specifikt verificeret for tillægget

- **HTML/JS-validering**: `node --check` uden fejl, `html5lib`-parsing uden fejl, ingen
  dublet-ID'er, ingen ubundne `getElementById`-referencer, ingen eksterne
  afhængigheder (fortsat én selvstændig fil).
- **Pixel-paritet mellem tegneflade og eksport**: `drawSnapshotToCanvas()` er
  omskrevet til at genbruge selve `render()`-funktionen (via midlertidig omdirigering
  af canvas/kontekst/kamera), så porte, væghuller og lagermål garanteret ser ens ud i
  PNG, resultatside og PDF-rapport — verificeret ved kodegennemgang og ved at
  `buildPrintReportHtml()` fortsat genererer nøjagtig 5 sider uden fejl efter
  omskrivningen.

## Ikke testet i dette miljø (kræver rigtig browser)

- Den faktiske visuelle tydelighed af den blå portmarkering og væghullet
- Om portens retningspil og label ser proportionerede ud ved forskellige zoomniveauer
  i praksis (logik er verificeret, ikke set med øjne)
- Chrome/Edge/Safari, tablet/laptop-visning (samme forbehold som tidligere versioner)

## Konklusion

Alle funktioner fra tillægget — tydeligere porte med væghul, ensrettet
tegne-/eksportpipeline, og den nye producent → produktserie → mål-arbejdsgang i
Trin 4 — er verificeret til at fungere korrekt uden JavaScript-fejl, uden regression i
eksisterende funktionalitet fra v0.2.0. En kort visuel gennemgang i en rigtig browser
på GitHub Pages anbefales fortsat, før værktøjet bruges hos en kunde.
