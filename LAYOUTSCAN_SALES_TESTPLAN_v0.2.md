# LayoutScan Sales v0.2.0 — Testplan & testresultater
**Nordic Operations · Pilot til Reoler A/S**
Dato: 2026-07-29

## Testmetode

Som ved v0.1.0 er testen udført uden adgang til en rigtig browser i dette miljø
(Chrome-download er blokeret). Samme tre-lags metode er brugt:

1. **HTML-validering** (`html5lib`) — filen er velformet, ingen dubletter af `id`, alle
   `getElementById`-referencer matcher et element i dokumentet.
2. **JavaScript-syntakskontrol** (`node --check`) — ingen syntaksfejl.
3. **Funktionel simulering (jsdom)** — hele brugerflowet gennemført programmatisk,
   inklusive den nye "wow-effekt"-sekvens ved "Generér layoutforslag", som nu er
   asynkron (~2 sekunder). Testen venter reelt de ~2 sekunder og bekræfter både
   mellemtrinnenes tekst og slutresultatet.

Punkt om Chrome/Edge/Safari, tablet/laptop-visning samt selve den visuelle
gitter-/bygningskontrast kræver fortsat manuel test i en rigtig browser.

## Regressionstest mod v0.1.0

Den fulde v0.1.0-testsuite er kørt igen mod v0.2.0-filen (bortset fra at
layoutgenereringstrinnet nu tester den nye asynkrone wow-sekvens i stedet for et
direkte funktionskald). **Alle tidligere bestående tests består fortsat** — ingen
regression i den eksisterende funktionalitet.

## Nye tests specifikt for v0.2.0

| Test | Resultat |
|------|----------|
| "Opret lager"-knap i Trin 2 findes og genererer bygningen øjeblikkeligt | ✅ Bestået |
| Klik på "Generér layoutforslag" aktiverer wow-overlayet | ✅ Bestået — overlay får klassen `active` |
| Første tekst i overlayet er "Analyserer lager..." | ✅ Bestået |
| Teksten skifter til næste trin efter ét interval (~480 ms) | ✅ Bestået — bekræftet forskellig fra første tekst |
| Overlayet skjules igen, og reolerne er genereret, efter hele sekvensen (~2,2 s) | ✅ Bestået — 4 reolrækker / 40 fag / 640 pallepladser i testscenariet (30×20 m) |
| Demoprojekt genererer stadig layout korrekt (nu via direkte kald, uden wow-forsinkelse ved indlæsning) | ✅ Bestået — 1.104 pallepladser / 69 fag / 7 rækker, identisk med v0.1.0 |
| `computeLayoutProposal` og `PROPOSAL_TYPES` er tilgængelige og forberedt til fremtidige forslagstyper | ✅ Bestået — kodegennemgang |
| Ingen dubletter af `id`, ingen ubundne `getElementById`-referencer | ✅ Bestået |
| Ingen eksterne afhængigheder (stadig én selvstændig HTML-fil) | ✅ Bestået |

## Ikke testet i dette miljø (kræver rigtig browser)

- Den visuelle kontrastforbedring på gitteret og bygningens tydelighed (kan kun
  vurderes visuelt)
- Fade-overgangene mellem trin (~250 ms CSS-transition) — logikken er til stede og
  bekræftet ved kodegennemgang, men den faktiske visuelle glidning er ikke set
- `fitToObjects()`s 80-85 %-udfyldning — formlen er verificeret ved kodegennemgang, men
  kan først bekræftes visuelt i en browser med en reel canvas-størrelse (jsdom har ikke
  et layout-engine, så `canvasWrap` har 0×0 i denne testopsætning)
- Print-til-PDF-output, tablet- og Safari-visning (samme forbehold som i v0.1.0)

## Konklusion

Kernelogikken — herunder den nye asynkrone wow-effekt-sekvens, den forberedte
`PROPOSAL_TYPES`-arkitektur, og alle eksisterende funktioner fra v0.1.0 — er verificeret
til at fungere uden JavaScript-fejl efter opdateringen. Den visuelle finish (kontrast,
luft, fade-overgange, faktisk 80-85 %-zoom) bør bekræftes ved en kort manuel gennemgang
på GitHub Pages, før værktøjet bruges hos en kunde.
