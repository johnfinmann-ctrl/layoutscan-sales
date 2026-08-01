# LayoutScan Pro v1.1.1 — Levering: Business Case, Prioritet 1 (Strategiske data)

**Fil:** `layoutscan_pro_v1.1.1.html` (bygget oven på det godkendte `layoutscan_pro_v1.1.0.html`)

## Hvad er tilføjet

Business Case henter nu automatisk følgende fra hele projektet, ikke kun pallepladser/truck/areal:

- **LayoutScore** og **Sikkerhed** og **Truckflow** — direkte fra analysemotorens eksisterende scoreberegning (Sprint 3-4), urørt.
- **Øverste forbedringsforslag** — fra den eksisterende, regelbaserede anbefalingsmotor.
- **Kapacitetsforøgelse, antal reolfag, arealudnyttelse** — var allerede tilgængelige i Business Case fra Sprint 6, nu eksplicit vist sammen med de øvrige faktorer i et samlet "Strategiske faktorer"-panel i Trin 4 og i rapporten.
- **Fremtidssikring** og **Fleksibilitet** — disse to fandtes ikke i forvejen nogen steder i appen. Jeg har defineret dem som to nye, gennemsigtige tommelfingerregler (se nedenfor), ikke hentet fra en eksisterende beregning.

Alle disse bruges nu aktivt i en udvidet, regelbaseret **ledelseskonklusion** (4 sætninger i stedet for 2), der varierer sin formulering efter de faktiske tal — fx nævnes truckflow kun hvis det er beregnet, og formuleringen ændrer sig alt efter om truckflowet er stærkt eller svagt.

## Vigtig præcisering (samme linje som hele projektet igennem)

Konklusionen er **ikke** skrevet af en AI-model — der er ingen sprogmodel koblet på. Det er en mere avanceret skabelon-motor end i Sprint 6, med betinget sætningsopbygning, så teksten varierer naturligt med tallene. Det er tydeligt mærket "Automatisk ledelseskonklusion" i UI'en og rapporten, med en disclaimer om at den er regelbaseret.

## Metode bag de to nye faktorer

- **Fremtidssikring**: baseret på den nye arealudnyttelse. Under 65% → "God" (rigelig plads til vækst), 65-82% → "Moderat", over 82% → "Begrænset". Dette er en forenklet tommelfingerregel om ledig kapacitet — ikke en egentlig kapacitetsplanlægningsmodel.
- **Fleksibilitet**: baseret på den gennemsnitlige margin mellem de faktiske gangbredder og den valgte trucks minimumskrav (fra den eksisterende gangbreddekontrol). Jo mere margen, jo lettere vurderes det at skifte til en anden/større trucktype senere uden at bygge om. Ratio ≥1,15 → "God", ≥1,0 → "Moderat", derunder → "Begrænset".

Begge er transparente, dokumenterede heuristikker — ikke validerede branchestandarder. De bør præsenteres over for kunder med samme forbehold som resten af LayoutScores metode.

## Test udført

- Syntaks (`node --check`) og HTML-struktur (html5lib, alle 95 `getElementById`-kald matcher) — begge bestået.
- Diff mod v1.1.0: bekræfter at alle ændringer er additive og lokaliserede til seks steder (titel, rapportsektion, det nye datamodul, Trin 4-visning, eksport-tabel) — intet i den eksisterende Business Case-beregningsmotor, canvas, analysemotor eller UI er rørt.
- Isolerede sanity-tests af de to nye heuristikker: tærskelværdier for fremtidssikring (60/75/90% udnyttelse → God/Moderat/Begrænset) og fleksibilitet (rummelige/præcise/smalle gangbredder → God/Moderat/Begrænset) gav alle korrekt resultat, og et tomt datasæt gav `null` (vises som "–"), ikke en fejl.

**Ikke testet i browser endnu:** hvordan panelet og den udvidede konklusion ser ud i praksis, og om formuleringerne rammer den ønskede "erfaren logistikchef"-tone godt nok i jeres øjne — det vil jeg gerne have jeres reaktion på, før vi går videre.

---
**Anbefaling:** Se konklusionsteksten efter i en rigtig browser (prøv gerne med og uden valgt truck, og med forskellige arealudnyttelser), og sig til om tonen og indholdet rammer rigtigt. Når Prioritet 1 er godkendt, går vi videre til Prioritet 2 (den udvidede ledelsesrapport) — med samme præcisering om, at det fortsat er regelbaseret tekst, ikke en AI-model.
