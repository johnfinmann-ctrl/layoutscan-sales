# LayoutScan Sales v0.2.1 — Leveringsnote (tillæg til v0.2.0)
**Nordic Operations · Pilot til Reoler A/S**
Dato: 2026-07-29

---

## 1. Formål med dette tillæg

v0.2.1 implementerer tillægget til v0.2.0: tydeligere porte og en omstruktureret
reolvalg-proces (producent → produktserie → mål). Alle funktioner fra v0.2.0 er bevaret
uændret.

## 2. Porte er nu tydeligt adskilt fra væg, dør, søjle, forbudt område og brandvej

- Porten vises som en markant blå (#2F80ED) åbning med hvid kant — kan ikke forveksles
  med den nu meget mørke ydervæg (#08131F), den brune dør (#8D6E52), den grå søjle
  (#5B6B7A), det røde forbudte område (#C0392B) eller den orange brandvej (#E67E22).
- Portens bredde vises altid direkte ved porten som "PORT X,X m" — også ved lavt
  zoomniveau, hvor almindelige objektlabels ellers skjules.
- En simpel hvid pil markerer åbningsretningen.
- **Væggen brydes nu visuelt, hvor en port sidder.** Den tidligere massive vægrektangel
  tegnes som flere segmenter med et reelt hul ud for hver overlappende port
  (`drawWallWithGaps`), i stedet for at porten blot lå oven på en sammenhængende væg.
- **Vigtig teknisk rettelse undervejs:** PNG-eksport, resultatside og PDF-rapport brugte
  tidligere en selvstændig, forenklet tegnefunktion, der ikke kendte til porte,
  væghuller eller lagermål. Snapshot-funktionen er omskrevet til at genbruge den samme
  `render()`-pipeline som selve tegnefladen (ved midlertidigt at omdirigere canvas og
  kamera til et offscreen-billede), så porte og alt andet nu gengives **identisk** i
  tegnefladen, PNG, resultatsiden og PDF-rapporten, som krævet.

## 3. Reolvalg — Trin 4 omstruktureret

Trin 4 er nu opbygget i tre dele, som beskrevet i tillægget:

**A · Vælg producent** — store valgkort: Generisk (standard), Metalsistem, Andre
producenter (kommer senere).

**B · Vælg produktserie** — indholdet skifter efter valgt producent:
- Generisk: de 9 eksisterende reoltyper, uændret.
- Metalsistem: de 7 produktserier fra `RACK_PRODUCTS`, alle fortsat vist som låste
  kort med "Produktdata afventer godkendelse fra Reoler A/S". Klik gør intet aktivt —
  sælgeren kan se dem, men ikke bruge dem i beregningen, som krævet.
- Andre producenter: en kort tekst om, at dette kommer i en senere version.

**C · Reolmål** — de kendte manuelle felter (fagsbredde, reoldybde, reolhøjde,
niveauer, pallepladser/niveau, antal fag) vises kun, når en brugbar (generisk)
produktserie er aktiv.

Et fast banner øverst på trinnet viser altid det **aktive** valg, der reelt bruges i
beregningen ("Producent: Generisk · Produktserie: Dobbelt pallereol · Status:
Vejledende dispositionsdata") — uafhængigt af hvilken producent-fane sælgeren aktuelt
kigger på. Det sikrer, at det aldrig er tvetydigt, hvad der rent faktisk indgår i
layoutforslaget, heller ikke mens man browser Metalsistems (endnu ikke godkendte)
produktserier.

## 4. Producent, produktserie og status i rapport og eksport

Hvert reol-objekt stemples nu ved oprettelse med `manufacturerLabel`, `seriesLabel` og
`productStatus` (enten "Vejledende dispositionsdata" for generiske reoler, eller
"Produktdata godkendt af Reoler A/S" — forberedt til den dag Metalsistem-data er
godkendt og reelt kan vælges). Disse felter er nu med i:

- **Resultatsiden** (Trin 6) — ny linje: "Producent · Produktserie · Status"
- **Foreløbig stykliste** — nye kolonner "Producent" og "Status"
- **PDF-rapporten** — producent/produktserie/status på nøgletals-siden (side 2) og i
  styklisten (side 4)
- **CSV-eksport** — nye kolonner
- **Excel-eksport** — producent/produktserie/status i nøgletals-arket samt nye
  kolonner i stykliste-arket
- **CAD-grundlag** — nye kolonner "producent" og "status" (tomme for ikke-reol-objekter)
- **JSON-eksport** — automatisk inkluderet, da felterne nu er en del af hvert
  reol-objekt

## 5. Hvad er bevaret uændret

Alt fra v0.2.0 er uændret: den guidede 6-trins arbejdsgang, wow-effekten ved
"Generér layoutforslag" (nu arkitektonisk uafhængig af Trin 4-omlægningen —
`computeLayoutProposal(PROPOSAL_TYPES.BALANCED)` kaldes stadig efter samme mønster),
truckbiblioteket, konfliktkontrollen, DXF-status (fortsat ikke implementeret, samme
meddelelse som før), og alle øvrige eksportformater.

## 6. Vigtigt — ingen gættede Metalsistem-data

Som krævet i tillæggets punkt 4 er der ikke opfundet eller gættet mål, belastninger
eller produktkoder for Metalsistem noget sted i denne opdatering. `RACK_PRODUCTS`-
strukturen er uændret fra v0.1.0/v0.2.0 (alle serier `approved:false`), og kun
generiske reoltyper kan reelt vælges til beregning. Koden er skrevet, så en fremtidig
godkendt Metalsistem-serie kan gøres aktiv uden at ændre resten af arkitekturen
(`manufacturer:"metalsistem"` håndteres allerede i `getRackStatusLabel()` og i
rack-objektets metadata).

## 7. Kendte begrænsninger

- Da Metalsistem-serier ikke kan vælges aktivt i denne version, er
  "Status: Produktdata godkendt af Reoler A/S"-grenen kun verificeret ved
  kodegennemgang, ikke ved en reel, brugbar Metalsistem-beregning.
- Portens åbningsretning markeres med en enkel pil, ikke en fuld arkitektonisk
  sving-radius — tilstrækkeligt til salgssamtalen, men ikke en konstruktionstegning.
- Som ved tidligere versioner er testningen udført via HTML/JS-validering og en
  simuleret DOM (se testplanen); det visuelle resultat i en rigtig browser — herunder
  hvor tydeligt portens blå farve og væghullet ser ud i praksis — er endnu ikke
  bekræftet.

---

**Version:** LayoutScan Sales v0.2.1 — Pilot til Reoler A/S
**GitHub commit (forslag):** "Clearer gate/door rendering with wall gaps, unified
snapshot rendering pipeline, and manufacturer → product series selection flow for
racks (Generic / Metalsistem-ready)."

Dette er et vejledende dispositionsforslag. Belastninger, montage, gulvforhold,
brandsikkerhed og konstruktive krav skal godkendes af Reoler A/S før levering.
