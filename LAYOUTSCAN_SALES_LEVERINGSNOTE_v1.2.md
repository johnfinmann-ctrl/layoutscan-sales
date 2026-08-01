# LayoutScan Sales v1.2 — Enkel Montørvisning, Flere Layoutforslag og Scan Tegning
**Nordic Operations · Pilot til Reoler A/S**
Dato: 2026-07-31

---

## Læsevejledning

Denne opgave var eksplicit formuleret som en konkret implementeringsopgave, ikke en
analyse — og alle 13 funktionspunkter er implementeret direkte i
`layoutscan_sales_v1.2.html` og testet automatiseret. Hvor noget teknisk ikke kunne
implementeres fuldt ud lokalt uden backend eller ekstern AI, er den bedst mulige
lokale, fungerende version bygget, og begrænsningen er beskrevet ærligt herunder —
uden at det har blokeret resten af leverancen.

## 1. Ny startside — Vælg opgave ✅

Et nyt "Trin 0"-skærmbillede vises, når man trykker "Nyt kundeprojekt": fire store
kort (A. Mindre opgave, B. Ombygning, C. Nyt lager, D. Brug eksisterende tegning).
Valget gemmes i `project.taskType` og styrer automatisk, om appen starter i
montørvisning (mindre opgaver) eller avanceret visning (øvrige typer).

## 2. Enkel montørvisning ✅

Implementeret som en CSS-drevet tilstand (`ui-simple`/`ui-advanced`) frem for to
parallelle kodebaser — det er den mest robuste måde at garantere, at intet
eksisterende er fjernet. I montørvisning skjules: Fortryd/Gentag, Grid/Snap/Zoom/Fit,
Mål, Nulstil forslag, Ret automatisk, samt sekundær truck/palletype og
brugerdefineret-truck-kontroller i Trin 3. Der vises et gult banner med et
"Vis avancerede værktøjer"-knap, og en tilsvarende knap findes i selve
værktøjslinjen. Alt avanceret er fortsat fuldt funktionsdygtigt bagved — intet er
slettet, kun midlertidigt skjult.

## 3. Scan eller importér tegning ✅ (med én ærlig, dokumenteret begrænsning)

Et nyt "📄 Scan / importér tegning"-modul i Trin 2:
- **Vælg fil** (PNG/JPG/JPEG/foto) og **Tag billede** (kamera via `capture="environment"`,
  virker hvor browseren understøtter det, fx iPad/mobil).
- Baggrunden vises som et lag under tegnefladens objekter (bygningsgulvet skjules
  automatisk, mens en baggrund er synlig, så tegningen ikke dækkes til).
- **Rotér** (±90°), **gennemsigtighed** (slider), **lås**, **fjern**, og
  **positionering** (X/Y i meter).
- **Kalibrering**: brugeren klikker to punkter på en forhåndsvisning af billedet,
  indtaster den kendte, virkelige afstand mellem dem, og systemet beregner skalaen.
  Status vises tydeligt: **"Tegningen er kalibreret til 1:1"** eller
  **"Tegningen er ikke kalibreret — mål er kun vejledende."**
- Der findes ingen automatisk billedgenkendelse — brugeren markerer selv vægge,
  porte, søjler, kontorer, eksisterende reoler osv. med de eksisterende
  paletteværktøjer oven på baggrunden, præcis som opgaven kræver.

**Ærlig begrænsning:** PDF-filer vises **ikke** som billede i denne lokale version —
der findes ingen PDF-rendering uden et eksternt bibliotek, og appen må ikke bruge
online tjenester. Vælges en PDF-fil, viser appen en tydelig besked om at bruge et
foto/PNG/JPG af tegningen i stedet (fx et skærmbillede eller foto af en udskrevet
PDF). Dette er beskrevet i UI'et, ikke skjult. Kalibreringen tilbyder desuden ikke
fri træk-i-canvas-positionering af selve baggrunden på hovedtegnefladen — position
justeres via numeriske X/Y-felter i stedet for direkte træk. Dette er en bevidst,
tidsbesparende forenkling, som stadig giver fuld funktionalitet (rotation, skala,
position, synlighed).

## 4. Foto — bevaret og udbygget ✅

Alle 11 eksisterende kategorier er bevaret. Tilføjet 8 nye: Gulv, Reolskade,
Ladestation, Brandforhold, Opmåling, Plantegning, Montageområde, Andet. Hvert foto
har nu et separat **Kategori**-felt (ud over titel), og kan **tilknyttes et konkret
objekt** (fx "Port 2", "Reol R04") via en dropdown i redigeringsvinduet. Når objektet
vælges i egenskabspanelet, vises dets tilknyttede fotos automatisk som thumbnails.

## 5. Tre layoutforslag ✅ Implementeret nu, ikke udskudt

Generatoren er omskrevet til en genbrugelig kerne (`buildRackRowObjects`), der
understøtter orientering (langs/på tværs), gangbredde-faktor, reserveret
vendezone-andel og krydsgang. Et tryk på **"🧭 3 forslag"** simulerer tre reelt
forskellige konfigurationer — **uden at røre det gemte layout**, før brugeren aktivt
vælger et:

- **Forslag A — Maksimal kapacitet**: tætteste pakning, mindste vendezone.
- **Forslag B — Bedste flow**: bredere gangbredder (+15%) og en ekstra, bred
  krydsgang midt i hallen for bedre flow og færre blindgange.
- **Forslag C — Fleksibel og fremtidssikret**: reoler **på tværs** af hallen (en
  reelt anden orientering, ikke bare et ændret mål), større reserveret vendezone
  (pladsreserve) og lidt bredere gangbredder.

Hvis to forslag reelt ligner hinanden meget (fordi hallens geometri ikke giver
albuerum for alternativer), viser appen det ærligt med en tydelig advarsel i stedet
for at foregive tre forskellige løsninger.

**Ærlig begrænsning:** "Kombination af langs og tværs" er implementeret som at hvert
forslag vælger den orientering, der passer bedst til dets formål — ikke som at ét
enkelt forslag blander rækker med forskellig orientering internt. En fuldt
blandet enkelt-layout-algoritme er en betydeligt større opgave, som ikke kunne nås
med tilstrækkelig test i denne sprint.

## 6. Sammenlign forslag ✅

Sammenligningsskærmen viser tre kort med miniature, pallepladser, reolfag,
gangareal, ledigt areal, konflikter, Layout Score, fordel og ulempe — samt knapperne
**Se forslag**, **Brug dette forslag** og **Generér igen**. "Se forslag" bytter
midlertidigt layoutet om til forhåndsvisning (med et banner og en "Tilbage"-knap, der
gendanner det oprindelige layout **præcis** som det var — verificeret med en test).
Layoutet overskrives først permanent ved "Brug dette forslag".

## 7. Reel optimering — ikke falsk AI ✅

Betegnelserne "Optimeringsmotor" og "regelbaseret" bruges konsekvent, og alle
AI-mærkede sektioner er eksplicit mærket "Regelbaseret" i UI'et. Spørgsmålet
**"Hvad er vigtigst for kunden?"** er tilføjet direkte i sammenligningsskærmen med de
seks ønskede valg. Valget **omrangerer** de tre allerede genererede forslag efter en
gennemsigtig, dokumenteret vægtning (kapacitets-, flow-, fleksibilitets-,
sikkerheds- og fremtidssikrings-delscorer, forskelligt vægtet pr. prioritet) — uden
at gen-beregne selve geometrien, hvilket er hurtigere og mere forudsigeligt.

## 8. AI-rådgiveren sammenligner forslagene ✅

En ny, tal-baseret sammenligningstekst genereres ud fra de faktiske beregnede
værdier (ikke generiske skabelonsætninger) — fx *"Forslag A giver 62 flere
pallepladser end Forslag B, men har... et lavere Layout Score for truckflow."* AI'en
flytter fortsat aldrig noget automatisk.

## 9. Mindre opgaver ✅

"Arbejd kun med dette område"-afkrydsningen er tilføjet i Trin 2 for opgavetypen
"Mindre opgave". Det eksisterende "Opret lager"-flow fungerer allerede uændret som et
mindre arbejdsområde, når brugeren angiver mindre mål — der kræves ingen komplet
lagertegning, præcis som ønsket. Montørvisningen (punkt 2) er som standard aktiv for
denne opgavetype.

## 10. Send til teknisk afdeling / AutoCAD ✅

Ny knap **"📦 Send til teknisk afdeling"** på resultatsiden åbner et overblik med:
kundeoplysninger, opgavetype, valgt layoutforslag, alternativforslag, mål, reoltyper,
truck- og palletyper, antal fotos, antal objektkommentarer, konflikter, Layout Score,
og **ignorerede konflikter med begrundelse** (der spørges nu om en kort begrundelse,
hver gang en konflikt ignoreres i konfliktpanelet). Et felt til **"Besked til teknisk
afdeling"** gemmes med projektet og kommer med i PDF'en. Der er ingen
e-mail-afsendelse (ingen backend) — i stedet: **Download samlet projektpakke**
(udløser alle eksportformater i træk) og **Kopiér projektresume** (til udklipsholder).

## 11. PDF-rapport ✅

Udvidet med: opgavetype, valgt forslag, alternative forslag, objektkommentarer, og
ignorerede konflikter med begrundelse. Alle tidligere sektioner (Forside,
Projektresumé, Layout, Nøgletal, AI-vurdering, Fotos, Materialeliste, Noter) er
bevaret. **Ærlig note:** Rapporten er ikke gjort kortere for mindre opgaver — punktet
i opgaven bruger ordet "må gerne" (valgfrit), og jeg har prioriteret at holde én
robust, grundigt testet rapportstruktur frem for at indføre betinget sidelogik med
risiko for fejl i den resterende tid.

## 12. Bevar eksisterende funktioner ✅

Intet er fjernet. Hele den automatiserede testsuite fra v1.1 (konfliktmotor,
Ret automatisk, blandede reoltyper, primær/sekundær truck og palletype, sticky
placering, DXF, PDF/PNG/JSON/CSV/Excel/CAD, gem/genindlæs) består fortsat uændret —
verificeret ved regression.

## 13. Testkrav — se testplanen for fuld gennemgang

Se `LAYOUTSCAN_SALES_TESTPLAN_v1.2.md` for de fem konkrete testscenarier og deres
resultater.

---

**Version:** LayoutScan Sales v1.2
**GitHub commit (forslag):** "Task-type selector, CSS-driven installer/simple view,
background drawing import with 2-point calibration, expanded photo module with
object-linking, real three-proposal generator with priority-based ranking and
AI comparison text, and a Send-to-Technical-Department package overview."

Dette er et vejledende dispositionsforslag. Belastninger, montage, gulvforhold,
brandsikkerhed og konstruktive krav skal godkendes af Reoler A/S før levering.
