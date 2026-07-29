# LayoutScan Sales v0.2.2 — Pilot Stabilization
**Nordic Operations · Pilot til Reoler A/S**
Dato: 2026-07-29

---

## 1. Formål

Som instrueret er der **ikke** ændret i den grundlæggende arbejdsgang eller tilføjet
"avancerede AI-funktioner". Fokus har været stabilitet, iPad-brugeroplevelse, hurtigere
arbejdsgang, konfliktløsning og forberedelse af AutoCAD-flowet. Alle tidligere
funktioner (v0.1.0 → v0.2.1 Pilot RC) er bevaret.

## 2. Fast værktøjspalette på iPad (punkt 1 — den vigtigste ændring)

Placeringstilstanden er nu **vedvarende** ("sticky"). Tidligere blev tilstanden afsluttet
automatisk, hver gang et objekt blev placeret, hvilket på iPad betød, at det slidte
paletpanel (som lukkes automatisk for at give plads til tegnefladen) skulle genåbnes for
hver eneste port, dør eller reol. Nu:

- forbliver det valgte objekt "armeret", efter det er placeret,
- kan sælgeren trykke Port, Port, Port, Port direkte på tegningen uden at åbne
  panelet igen,
- vises der et tydeligt, grønt banner: **"✓ Placering aktiv: [objekt] — tryk på
  tegningen for at placere"** med en rigtig knap: **"Afslut placering"**,
- afsluttes tilstanden kun ved tryk på "Afslut placering", Escape, eller ved at vælge
  et andet objekt i paletten.

Dette gælder alle objekttyper — bygningselementer, infrastruktur, områder og reoler.

## 3. Flere køretøjer (punkt 2)

Trin 3 er udvidet med et separat afsnit til **sekundære trucks** (flervalg via
afkrydsning), adskilt fra den eksisterende **primære truck** (uændret enkeltvalg).
Kun den primære truck bruges til beregning, gangbredde og layoutgenerator — det er
gjort eksplicit i UI'et ("bruges til beregning, gangbredde og layoutgenerator" vs.
"til rapport, flow og fremtidig brug — påvirker ikke selve beregningen"). Sekundære
trucks vises i PDF-rapporten og Excel-eksporten. Digital Twin/AI-brug er ikke
implementeret her, kun forberedt (data er gemt og tilgængeligt).

## 4. Blandede reolsystemer (punkt 3)

Dette krævede en strukturel ændring: `project.rackSelection` (ental) er erstattet af
`project.rackSelections` (en liste). Trin 4 er ombygget til reelt flervalg:

- Hver generisk reoltype vises som et afkrydsningskort (✅ når aktiv), og sælgeren kan
  markere flere samtidig — fx Dobbelt pallereol + Småvarereol + Grenreol + Push Back.
- Hver aktiv type får sin egen redigerbare målsektion (fagsbredde, dybde, højde,
  niveauer, pallepladser pr. niveau, antal fag).
- **Layoutgeneratoren bruger nu rent faktisk kombinationen**: rækkerne fordeles på
  skift mellem alle aktive typer (round-robin), så et genereret layout typisk
  indeholder flere reoltyper i praksis — ikke kun én.
- Paletten i Trin 5 har ét selvstændigt tryk-for-at-placere-element pr. aktiv type.
- Mindst én type skal altid være aktiv (systemet forhindrer at fjerne den sidste).

## 5. Konfliktpanel (punkt 4)

Statuslinjens farvede status (🟢/🟡/🔴) er nu en **rigtig knap**. Et klik åbner en liste
over alle fundne konflikter, hver med:

- en beskrivelse ("Dobbelt pallereol — Reol overlapper en søjle" osv.),
- **"Vis"**: zoomer, centrerer og markerer objektet direkte på tegningen,
- **"Ret automatisk"**: forsøger at rette *denne ene* konflikt (se punkt 6).

## 6. Automatisk rettelse (punkt 5 — hovedkrav)

Der er tilføjet en global **"🛠️ Ret automatisk"**-knap i værktøjslinjen samt en
per-konflikt "Ret automatisk"-knap i konfliktlisten. Begge bruger samme regelbaserede
motor:

- **Reol uden for lageret** → flyttes tilbage inden for grænserne.
- **Reol overlapper søjle/port/brandvej/forbudt område** → forsøger først at rykke
  reolen til siden (i små trin), dernæst at afkorte antal fag, og kun som allersidste
  udvej at fjerne reolsegmentet — med tydelig besked om kapacitetstab.
- **Overlap mellem to reoler** → rykker den ene fri af den anden.
- **For smal gangbredde** → rykker den efterfølgende række længere væk, så den valgte
  trucks anbefalede gangbredde overholdes; hvis der ikke er plads, fjernes rækken i
  stedet (med besked om kapacitetstab), frem for at skabe en ny konflikt.
- Reolvalg og trucksvalg **ændres aldrig** af rettelsen — kun geometri (placering,
  længde, antal fag) justeres.

Før rettelsen gemmes layoutet automatisk (samme fortryd-stak som ellers). Bagefter
vises en opsummering med hver enkelt ændring i almindeligt sprog (fx *"Gangbredde til
Dobbelt pallereol øget til 2,90 m (rykket 0,40 m)"*, *"Dobbelt pallereol fjernet —
kunne ikke placeres uden konflikt (kapacitet reduceret med 6 pallepladser)"*), samt
antal konflikter før/efter og den samlede kapacitetsændring — med knapperne **Godkend**
og **Fortryd**.

**Ærlig begrænsning:** Dette er en regelbaseret, gentagen "bedste forsøg"-rettelse —
**ikke** en fuldstændig optimerende løser. Den løser konflikter én ad gangen i op til
60 forsøg og kan i sjældne, meget tætpakkede layouts efterlade enkelte konflikter
uløst (fx hvis reolhøjden overstiger den frie lagerhøjde, hvilket kræver et andet
reolvalg i Trin 4 og ikke kan løses geometrisk). Sådanne resterende konflikter vises
tydeligt i opsummeringen.

## 7. Portvisning (punkt 6)

Uændret fra Pilot RC — allerede tydelig med blå farve, hvid kant, ikon, portbredde og
brud i væglinjen. Ingen yderligere ændringer var nødvendige.

## 8. Responsivt design (punkt 7)

Uændret fra Pilot RC (to breakpoints, skjulbare paneler på iPad, ingen vandret
scrolling). De nye elementer i denne version (sekundære trucks, blandede
reolsystemer, konfliktpanel) er bygget med de samme CSS-mønstre (Grid/Flexbox,
`width:100%`-inputs) og bør derfor arve den samme responsive opførsel, men er ikke
visuelt eftertestet på en fysisk enhed (se testplanen).

## 9. Reolproducenter (punkt 8)

Uændret. Generisk/Metalsistem/Andre producenter-strukturen er bevaret; Metalsistem
er fortsat låst med "Produktdata afventer godkendelse fra Reoler A/S". Ingen mål er
opfundet.

## 10. DXF (punkt 9)

Som instrueret er DXF-generatoren **ikke ændret**. Den er genvalideret efter denne
opdatering (se testplanen) og fungerer fortsat identisk til Pilot RC-versionen.

---

**Version:** LayoutScan Sales v0.2.2 — Pilot Stabilization
**GitHub commit (forslag):** "Sticky placement mode for iPad, multi-truck and mixed
rack-system support, clickable conflict panel, and rule-based automatic conflict
resolution with approve/undo."

Dette er et vejledende dispositionsforslag. Belastninger, montage, gulvforhold,
brandsikkerhed og konstruktive krav skal godkendes af Reoler A/S før levering.
