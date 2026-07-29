# LayoutScan Sales v0.1.0 — Leveringsnote
**Nordic Operations · Pilot til Reoler A/S**
Dato: 2026-07-29

---

## 1. Hvad er dette

LayoutScan Sales er en selvstændig, forenklet salgsversion af LayoutScan Professional.
Formålet er, at en reolsælger fra Reoler A/S kan opmåle, tegne og præsentere et
realistisk lagerforslag hos kunden på under 20 minutter — uden teknisk baggrund.

Filen er **helt adskilt** fra LayoutScan Professional. `layoutscan_pro_v1.1.0.html` er
ikke ændret eller overskrevet.

---

## 2. Hvad er genbrugt fra LayoutScan Professional v1.1.0

Den stabile tegnemotor er genbrugt og tilpasset til det nye, forenklede datamodel:

- Koordinat-transformation (`worldToScreen` / `screenToWorld`), zoom og pan
- Grid-tegning og snap-motor (grid-snap + "smart snap" mod andre objekters kanter/midte)
- Objekt-rendering (`drawObject`, `roundRect`, `isLight`), valg-chrome og håndtag til
  resize/rotation
- Pointer-baseret drag-fra-palette (virker med mus og touch) samt "klik for at armere
  → klik for at placere" som alternativ
- Måleværktøj (vælg 2 objekter → vis afstand)
- Undo/redo via let snapshot-stak
- Eksportmønstre: SpreadsheetML til Excel (ingen eksterne biblioteker), CSV, PNG, JSON
- Truckbiblioteket (8 standardtrucks + mulighed for brugerdefineret truck) er videreført
  identisk i struktur

Disse dele er **omskrevet ind i den nye fil**, ikke kopieret som separate `<script>`-
referencer, fordi Sales-versionen skal være én selvstændig HTML-fil uden afhængighed
af Pro-filen.

## 3. Hvad er fjernet eller skjult

- Dashboard 2.0 (scorekort, heatmap, trendpile)
- Business Case Engine
- Layers-panel og lag-styring (fast, enkel lag-tildeling i baggrunden i stedet)
- Avanceret analyse-/anbefalingsmotor fra Pro (erstattet af en simplere, målrettet
  konfliktkontrol — se afsnit 5)
- Rapportindstillinger med flersprogsstruktur, versionshistorik-UI (rapporten er fast
  5-siders og enkel; ingen historik-arkiv i v0.1)
- Lange formularer / store dashboards på forsiden

## 4. Hvad er nyt

- Guidet 6-trins arbejdsgang: Kunde → Lager → Drift → Reoler → Layout → Resultat
- Automatisk oprettelse af rektangulær lagerhal (4 vægge) ud fra indtastet længde/bredde
- Forenklet objektbibliotek målrettet salgssamtalen: Væg, Port, Dør, Søjle, Kontor,
  Teknikrum, Brandvej, Flugtvej, Forbudt område, Batterirum, Ladestation
- Reolobjekter modelleret som **rækker** med egenskaberne fagsbredde, reoldybde,
  reolhøjde, antal niveauer, pallepladser pr. niveau og antal fag — kapacitet beregnes
  automatisk (fag × niveauer × pallepladser/niveau)
- Regelbaseret layoutgenerator ("Generér layoutforslag")
- Kompakt resultatbjælke og statuslys (grøn/gul/rød) nederst på layoutskærmen
- Kundevenlig resultatside (Trin 6) med KPI'er, konklusionstekst og forbehold
- Foreløbig stykliste
- 5-siders printbar/PDF-rapport (via browserens printfunktion)
- CAD-grundlagseksport (struktureret CSV med objekt-ID, type, position, mål, rotation)
- Demoprojekt: Demo Lager A/S, 40×25 m

## 5. Hvordan produktbiblioteket fungerer

Reolvalget i Trin 4 er delt i to:

**A. Generiske reoltyper** (9 typer: enkelt/dobbelt pallereol, småvarereol, grenreol,
gennemløbsreol, Push Back, Drive-In, mobile reoler, mezzanin) med foreslåede standardmål,
som sælgeren altid kan justere manuelt (fagsbredde, dybde, højde, niveauer, pallepladser
pr. niveau).

**B. Metalsistem** — vist som forberedt struktur (`RACK_PRODUCTS`), men **alle serier er
markeret `approved:false`** og kan ikke vælges. Der er ikke opfundet eller gættet mål,
belastninger eller konstruktionsregler nogen steder i koden. Når Reoler A/S leverer og
godkender rigtige produktdata, udfyldes `modules`-arrayet for den relevante serie, og
serien kan aktiveres uden at ændre resten af systemet.

## 6. Hvordan layoutgeneratoren fungerer

Knappen "Generér layoutforslag" er **regelbaseret, ikke AI**. Algoritmen:

1. Beregner et brugbart areal inden for hallens vægge (0,3 m margin).
2. Placerer parallelle reolrækker langs hallens længderetning med rækkeafstand =
   reoldybde + den valgte trucks anbefalede gangbredde.
3. For hver række identificeres forhindringer (søjler, porte, døre, brandveje,
   flugtveje, forbudte områder), og rækken deles i frie segmenter omkring dem.
4. Hvert segment fyldes med så mange hele fag, som pladsen tillader.

I denne pilotversion leveres **ét stabilt "Balanceret forslag"** (jf. kravspecifikationens
punkt om, at mindst ét stabilt forslag skal prioriteres, hvis tre forslagstyper vurderes
for komplekst til første sprint). "Maksimal kapacitet" og "Bedst flow" er ikke
implementeret i v0.1 og er noteret som et opfølgningspunkt.

Generatoren sletter aldrig kundens bygningsobjekter (vægge, porte, søjler mv.) — kun
tidligere genererede reolrækker fjernes ved "Nulstil forslag" eller ny generering.

## 7. Konfliktkontrol

Automatisk kontrol af: reol uden for lageret, reol over søjle/port/brandvej/flugtvej/
forbudt område, reolhøjde over fri lagerhøjde, overlap mellem reoler, for smal
truckgang mellem parallelle rækker, og meget lille afstand til væg. Konflikter vises
som et lille symbol direkte på tegningen (gul/rød) og som tekst i egenskabspanelet —
aldrig kun som farve.

## 8. Begrænsninger

- Kun ét genereret forslag (Balanceret) — ikke tre alternative forslagstyper.
- Layoutgeneratoren arbejder med rette, ikke-krydsende reolrækker langs hallens
  længdeakse; den understøtter ikke automatisk L-formede haller eller vinklede rækker.
- Metalsistem-produktdata er ikke inkluderet og skal leveres af Reoler A/S.
- Ingen priser i stykliste eller rapport i v0.1.
- Business Case er ikke inkluderet i denne version (var eksplicit valgfrit/skjult i
  kravspecifikationen og er udeladt helt for at holde værktøjet enkelt).
- Rapporten er fast 5 sider uden brugerdefinerbar rækkefølge eller versionshistorik-UI.

## 9. Status på DXF

DXF er **ikke implementeret** i v0.1. Knappen "DXF-eksport" viser teksten:
*"DXF-eksport kommer i næste version. CAD-grundlaget kan allerede eksporteres som
strukturerede koordinater."* CAD-grundlaget eksporteres i stedet som en struktureret
CSV-fil med objekt-ID, objekttype, produkt, x/y-position, mål, rotation og lag —
klar til manuel eller scriptet import i AutoCAD. Det er ikke og må ikke fremstilles
som en fil, der kan åbnes direkte i AutoCAD.

## 10. Kendte risici

- Layoutgeneratorens segment-opdeling omkring forhindringer er testet på rektangulære
  haller med et fåtal søjler/porte (jf. testplanen). Meget forhindringstætte layouts
  (mange søjler tæt på hinanden) er ikke stress-testet.
- Filen er valideret automatisk (HTML-parsing, JavaScript-syntaks og et fuldt
  funktionsflow via en headless DOM-simulering — se testplanen). **Den er ikke endnu
  testet manuelt i en rigtig browser på GitHub Pages, tablet eller Safari**, hvilket
  kravspecifikationen ellers foreskriver som endelig accepttest. Dette bør gøres før
  brug hos en kunde.
- Undo/redo er en simpel snapshot-stak (op til 40 trin) og dækker ikke alle
  UI-tilstande (fx trin-navigation), kun objektændringer på tegnefladen.

## 11. Resultat af 20-minutters-testen (simuleret gennemløb)

Et fuldt gennemløb blev kørt automatisk (nyt projekt → kunde → lager 30×20 m → drift
→ reolvalg → generér layoutforslag → resultat → rapport) og bekræftede:

- **1 obligatorisk felt** i hele forløbet før første layoutforslag (kundenavn) — langt
  under kravet på maks. 10.
- **6 guidede trin**, som krævet.
- Topbaren i Trin 5 viser maks. 3-4 primære handlingsgrupper ad gangen.
- Ingen formular har mere end 8 felter på samme skærmbillede (Trin 1 har flest, med
  11 felter fordelt over 2 kolonner, kun 1 obligatorisk).
- Det simulerede forløb fra "Nyt kundeprojekt" til færdig rapport krævede ca. 10-12
  klik plus udfyldelse af 3 talfelter — realistisk inden for 20 minutter, inklusive tid
  til manuel finjustering af layoutet på tegnefladen.

Den reelle bekræftelse af 20-minutters-kravet kræver dog en fysisk gennemført test med
en Reoler A/S-sælger, som anbefalet i afsnit 10.

---

**Version:** LayoutScan Sales v0.1.0 — Pilot til Reoler A/S
**GitHub repository (forslag):** `layoutscan-sales`
**Commit-besked (forslag):** "LayoutScan Sales v0.1.0 – Reoler A/S pilot with guided
20-minute warehouse design workflow."

Dette er et vejledende dispositionsforslag. Belastninger, montage, gulvforhold,
brandsikkerhed og konstruktive krav skal godkendes af Reoler A/S før levering.
