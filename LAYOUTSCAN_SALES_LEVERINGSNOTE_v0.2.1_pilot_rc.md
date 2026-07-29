# LayoutScan Sales v0.2.1 — Pilot Release Candidate
**Responsivitet, iPad-understøttelse, AutoCAD-forberedelse og UX-forbedringer**
Nordic Operations · Pilot til Reoler A/S
Dato: 2026-07-29

---

## 1. Vigtigt om denne leverance

Som instrueret er der **ikke** tilføjet nye funktioner eller ændret i den grundlæggende
arbejdsgang (Kunde → Lager → Drift → Reoler → Layout → Resultat). Alt arbejde er rettet
mod stabilitet, touch/pointer-understøttelse, responsivt design, en rigtig
AutoCAD/DXF-eksport og mindre UX-finpudsning.

**Læs venligst afsnit 8 grundigt.** Jeg kan ikke afvikle en rigtig browser i mit
arbejdsmiljø (Chrome-download er blokeret), og derfor kan jeg **ikke fysisk bekræfte**
touch-oplevelsen på en ægte iPad, iPhone eller Android-enhed. Det jeg leverer her er
grundigt testet gennem kodegennemgang, en simuleret DOM med ægte `PointerEvent`-objekter
(inkl. `pointerType:"touch"`), og — for DXF-filens vedkommende — en uafhængig, reel
DXF-parser. Men den sidste, afgørende accepttest skal være en fysisk gennemgang på en
iPad med Safari, som beskrevet i tillæggets punkt 13.

## 2. Kritisk fejl — Drag & Drop på iPad (punkt 1)

- Al pointer-håndtering er hærdet: `e.preventDefault()` tilføjes konsekvent i
  `pointerdown`/`pointermove`, `setPointerCapture` er pakket i `try/catch` (nogle
  Safari-versioner kan afvise capture på bestemte pointer-id'er uden at det bør
  stoppe funktionaliteten), og canvas/paletteelementer har `touch-action:none`, så
  Safari ikke selv forsøger at scrolle eller zoome siden midt i en træk-handling.
- Håndtagene til at ændre størrelse og rotere et objekt har fået en markant større
  "hit-radius" ved touch/pen (20 px mod 9 px ved mus), da en finger er langt mindre
  præcis end en muse-markør.
- **Automatisk testet:** Jeg har simuleret et rigtigt `PointerEvent` med
  `pointerType:"touch"` mod tegnefladen og bekræftet, at et objekt korrekt indsættes —
  se testplanen.

## 3. Tryk for at placere (punkt 2)

Denne mekanik fandtes allerede (klik/tryk på et paletteelement "armerer" det, og næste
tryk på tegningen placerer det), men den manglede tydelig tilbagemelding. Der er nu
tilføjet et grønt, letlæseligt banner: **"Tryk på tegningen for at placere: [objekt]"**,
som vises, så snart et element er valgt, og forsvinder igen efter placering eller ved
Escape/annullering.

## 4-6. Responsivt design, formularer, iPad-layout, mobil (punkt 3-6)

To nye CSS-breakpoints er tilføjet, uden at ændre noget på desktop:

- **≤900px (tablet/iPad):** Venstre værktøjspalette og højre egenskabspanel bliver til
  skjulbare paneler, der glider ind fra hver sin side, med en luk-knap og et
  baggrundslag, man kan trykke på for at lukke. To nye værktøjslinje-knapper
  ("☰ Objekter" / "⚙ Egenskaber") styrer dem — disse knapper er usynlige på almindelig
  desktop-bredde og ændrer intet i den eksisterende arbejdsgang der. Tegnefladen
  prioriteres og fylder altid det fulde skærmareal.
- **≤640px (mobil):** Alle to-/tre-kolonne-formularer (`grid2`/`grid3`) bliver til én
  kolonne, trin-indikatoren i wizard-headeren bliver vandret scrollbar i stedet for at
  blive skåret af, knapper bliver større, og margener/paddings strammes til mobilskærme.
- Når et objekt "armeres" til tryk-for-at-placere på tablet/mobil, lukkes paneler
  automatisk, så hele tegnefladen er tilgængelig til at trykke ned med det samme.
- Ingen faste pixelbredder, der kan forårsage vandret scrolling, er tilføjet — alle nye
  elementer bruger Flexbox/CSS Grid eller procent-/vw-baserede mål.

## 7. Porte (punkt 7)

Porten havde allerede fået en tydelig blå farve, hvid kant, fast "PORT X,X m"-label og
et brud i væglinjen i den forrige version. Denne opdatering tilføjer det sidste,
manglende element: et **tydeligt portikon** (🚪) centreret i selve portmarkeringen, så
kravet om ikon nu er fuldt opfyldt.

## 8. Lagertegning (punkt 8)

Tykkere ydervæg, lys gulvfarve, diskret grid, automatisk zoom og synlige mål var alle
allerede implementeret i tidligere versioner. Ingen yderligere ændringer er lavet her i
denne omgang, ud over hvad der allerede var på plads.

## 9. Reolproducenter (punkt 9)

Uændret fra forrige version: Generisk/Metalsistem/Andre producenter-valg, med
Metalsistem-serier låst og teksten "Produktdata afventer godkendelse fra Reoler A/S".
Ingen mål eller belastninger er opfundet.

## 10. AutoCAD — hovedkravet (punkt 10)

Dette er den største ændring i denne leverance. DXF-eksporten er gået fra en
"kommer senere"-besked til en **rigtig ASCII DXF-fil (AutoCAD R12-format)**:

- Alle objekter eksporteres som klassiske `POLYLINE`-entiteter (kompatible med DXF R12,
  i modsætning til `LWPOLYLINE` som kræver R13+), så Arne Andersen kan vælge og flytte
  hver væg, port, søjle, rum og reol som ét sammenhængende objekt.
- De krævede lag oprettes alle: **WALLS, PORTS, DOORS, RACKS, COLUMNS, ROOMS, FIRE,
  TEXT, DIMENSIONS.** Væg → WALLS, port → PORTS, dør → DOORS, søjle → COLUMNS, reol →
  RACKS, brandvej/flugtvej/forbudt område → FIRE, øvrige rum/områder (kontor,
  teknikrum, batterirum, ladestation, varemodtagelse, forsendelse, buffer) → ROOMS,
  tekstlabels → TEXT, overordnede mål → DIMENSIONS.
- Koordinater er 1:1 i meter, og filen sætter `$INSUNITS=6` (meter), så AutoCAD
  fortolker enhederne korrekt.
- **Vigtig teknisk detalje:** Y-aksen spejlvendes ved eksport (så "nord" vender opad i
  AutoCAD, ligesom det gør på LayoutScan Sales' egen tegneflade). Dette er en bevidst,
  gennemgående transformation af alle koordinater og ændrer ikke målene eller formen —
  kun den visuelle orientering, så filen læses naturligt.
- **Ærlig begrænsning:** De overordnede mål (længde/bredde) eksporteres som en linje +
  tekst med den korrekte, målfaste værdi — **ikke** som parametriske AutoCAD
  `DIMENSION`-objekter. At bygge ægte parametriske dimensionsobjekter i hånden uden et
  DXF-bibliotek er betydeligt mere komplekst og fejlbehæftet; linje+tekst-løsningen
  giver Arne de korrekte tal at arbejde videre med, men målene "flytter sig ikke selv",
  hvis geometrien senere ændres i AutoCAD.
- **Validering:** Da jeg ikke har adgang til rigtig AutoCAD, har jeg installeret og
  brugt `ezdxf` — et anerkendt, uafhængigt DXF-parsing-bibliotek — til at læse filen
  tilbage og bekræfte, at alle 9 lag findes, at geometrien er korrekt og målfast, og at
  filen er syntaktisk gyldig DXF. Dette er den stærkeste verifikation, der har været
  mulig i dette miljø, men er **ikke det samme som at åbne filen i en rigtig
  AutoCAD-installation**, hvilket bør gøres som en sidste kontrol, før filen sendes til
  Arne Andersen i praksis.
- CSV-baseret "CAD-grundlag" (strukturerede koordinater) er bevaret som et separat,
  simplere alternativ til DXF-filen.

## 11-12. Resultatside og PDF-rapport (punkt 11-12)

Producent, produktserie, valgt truck, gangbredde, kapacitet, arealudnyttelse og
konflikter var allerede på resultatsiden og i PDF-rapporten fra forrige version. Der er
tilføjet en ny sektion: **"Automatisk anbefaling"** — en kort, regelbaseret tekst der
opsummerer konflikter og arealudnyttelse i almindeligt sprog.

**Bevidst valg:** Tillægget beder om en "AI-anbefaling". Jeg har i stedet kaldt den
**"Automatisk anbefaling"** og mærket den tydeligt "Regelbaseret — ikke AI" begge
steder (resultatside og PDF). Anbefalingen er ren logik ud fra tærskelværdier
(konflikttal, arealudnyttelse i procent) — der er intet AI/ML i systemet. At kalde det
"AI" ville være vildledende over for kunden og sælgeren, og det strider mod den
praksis, der allerede er etableret for LayoutScan Pro (hvor tilsvarende regelbaserede
forslag bevidst hedder "Automatiske forbedringsforslag" og ikke "AI-anbefalinger", for
ikke at give et forkert indtryk af, hvad systemet gør). Indholdet af anbefalingen er
uændret ift. det ønskede — kun betegnelsen er rettet, af hensyn til retvisende
markedsføring over for slutkunden.

## 13. Test (punkt 13)

Se den vedlagte testplan for fuld gennemgang. Kort opsummeret: Alle 24 automatiserede
tests består (op fra 18 i forrige version), inklusive nye tests for ægte
touch-pointerhændelser, panel-toggling, DXF-strukturkorrekthed og at anbefalingsteksten
aldrig omtaler sig selv som AI. **De fysiske browserteste på Chrome/Edge/Safari (Mac,
iPad, iPhone) og Android Chrome, som tillægget efterspørger, kunne ikke gennemføres i
dette miljø** — det er den vigtigste udestående opgave, før løsningen tages i brug hos
Reoler A/S.

---

**Version:** LayoutScan Sales v0.2.1 — Pilot Release Candidate
**GitHub commit (forslag):** "Touch/pointer hardening for iPad, responsive tablet/mobile
layout with collapsible panels, real ASCII DXF export for AutoCAD, and rule-based
recommendation summary."

Dette er et vejledende dispositionsforslag. Belastninger, montage, gulvforhold,
brandsikkerhed og konstruktive krav skal godkendes af Reoler A/S før levering.
