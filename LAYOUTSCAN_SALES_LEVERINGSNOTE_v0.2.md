# LayoutScan Sales v0.2.0 — Leveringsnote
**Nordic Operations · Pilot til Reoler A/S**
Dato: 2026-07-29

---

## 1. Formål med denne opdatering

v0.2.0 er en ren UX-opdatering af v0.1.0. Der er **ikke** tilføjet nye store funktioner —
alle eksisterende funktioner fra v0.1.0 er bevaret uændret. Fokus har udelukkende været:

- tydeligere lagerbygning
- mere professionelt layout
- hurtigere arbejdsgang
- "wow-effekt" når layoutet genereres

## 2. Ændringer, punkt for punkt

**1. Bygningen er tydeligere**
Ydervæggen er nu 2,5× tykkere (0,2 m → 0,5 m) med en mørkere kantfarve. Gulvet inde i
bygningen er lyst gråt; området udenfor er hvidt. Man kan nu se hallens omrids med det
samme, i stedet for at den druknede i gitteret.

**2. Gitter**
Kontrasten er reduceret markant. De små gridlinjer er nu meget lyse (`#EEF1F4`), og
hovedlinjerne (nu hver hele meter i stedet for hver 2,5 m) er en anelse mørkere
(`#DCE2E8`) — men aldrig så mørke, at de overdøver bygningen.

**3. Automatisk zoom**
Ny "Opret lager"-knap i Trin 2 giver øjeblikkelig visuel bekræftelse ("✓ Lagerhal
oprettet"). `fitToObjects()` er omskrevet til at centrere hallen i canvas og ramme
ca. 82 % udfyldning, første gang layoutskærmen åbnes — sælgeren skal ikke selv zoome.

**4. Større canvas**
Venstre værktøjspanel er reduceret fra 230 px til 186 px, højre egenskabspanel fra
270 px til 226 px. Selve tegnefladen får dermed mærkbart mere plads.

**5. Ikoner**
Alle 14 bygnings-, infrastruktur- og områdeelementer har fået et ikon (🧱 🚪 ⬛ 🏢 ⚙ 🔥
🟩 ⛔ 🔋 ⚡ 📦 🚚) i venstremenuen, så den er hurtigere at aflæse.

**6. Reolkort**
Reolkortene i Trin 4 er ca. 20 % større (mere padding, større ikon og tekst), så det er
let at pege og vælge sammen med kunden.

**7. Truckkort**
Designet er bevaret, men gangbredden vises nu som det klart dominerende tal på kortet
(store, grønne cifre) — det er den vigtigste information ved truckvalget.

**8. Lagermål på tegningen**
Længde og bredde vises nu direkte på selve tegningen (øverst og til venstre) og
opdateres automatisk, hver gang bygningen genereres eller ændres.

**9. "Generér layout" — wow-effekt**
Et klik på "Generér layoutforslag" viser nu en kort animation (spinner + facit-check)
med teksterne "Analyserer lager...", "Beregner truckgange...", "Optimerer kapacitet...",
"Finder bedste løsning..." over ca. 2 sekunder, afsluttet med "Layoutforslag klar."
Selve beregningen er stadig 100 % regelbaseret — animationen er ren tilbagemelding,
ikke en indikation af AI. Koden er samtidig omstruktureret (`computeLayoutProposal(type)`
+ `PROPOSAL_TYPES`-konstant) så en senere version kan tilføje "Maksimal kapacitet" og
"Bedst flow" som yderligere `type`-værdier uden at ændre resten af arkitekturen.

**10. Knapper**
Primære handlinger (Opret lager, Generér layoutforslag, Vis resultat, Rapport & eksport,
Gem) er nu konsekvent grønne. Sekundære funktioner (Fortryd, Gentag, Grid, Snap, Zoom,
Mål, Tilbage-knapper) har fået en dæmpet, mørk sekundær-stil.

**11. Resultatlinje**
Mere luft: padding øget fra 8×16 px til 14×24 px, afstand mellem felter øget fra 22 px
til 34 px, tallene er en anelse større.

**12. Animationer**
Alle skift mellem trin (start → wizard → layout → resultat) fader nu blødt ind over
ca. 250 ms i stedet for at skifte hårdt.

**13. Farver**
Uændret Nordic Operations-identitet (navy/grøn), men med mere luft og hvidt i kort,
paletter og reolvalg, og mindre dominerende mørke flader generelt.

## 3. Hvad er bevaret uændret

Al forretningslogik fra v0.1.0 er bevaret 1:1: den guidede 6-trins arbejdsgang,
objektbiblioteket, RACK_PRODUCTS/Metalsistem-strukturen (fortsat `approved:false`),
truckbiblioteket, konfliktkontrollen, beregningerne, den 5-siders rapport, stykliste og
alle eksportformater (JSON, CSV, Excel/SpreadsheetML, PNG, CAD-grundlag). DXF-status er
uændret: ikke implementeret, med samme informationstekst som i v0.1.0.

## 4. Kendte begrænsninger og noter

- Wow-effektens 2-sekunders sekvens er en fast UI-timer og kører altid, uanset
  lagerets størrelse — den erstatter ikke en reel fremdriftsindikator for tunge
  beregninger (relevant hvis fremtidige, tungere layoutalgoritmer tilføjes).
- "Opret lager"-knappen i Trin 2 og det automatiske kald ved "Næste" er nu begge til
  stede; begge kalder samme `generateBuildingFromDimensions()`, som er idempotent
  (fjerner og genopretter auto-vægge), så der opstår ikke dubletter.
- Arkitekturen til flere forslagstyper (Maksimal kapacitet / Bedst flow) er forberedt
  som beskrevet i punkt 9, men **ikke implementeret** i v0.2.0 — kun "Balanceret
  forslag" leveres, som i v0.1.0.
- Som i v0.1.0 er testningen af dette miljø udført via HTML/JS-validering og en
  simuleret DOM (se testplanen); den visuelle oplevelse i en rigtig browser, på
  tablet og i Safari er fortsat ikke bekræftet.

---

**Version:** LayoutScan Sales v0.2.0 — Pilot til Reoler A/S
**GitHub commit (forslag):** "UX improvements, warehouse visualization, larger canvas
and improved layout generation experience."

Dette er et vejledende dispositionsforslag. Belastninger, montage, gulvforhold,
brandsikkerhed og konstruktive krav skal godkendes af Reoler A/S før levering.
