# LayoutScan Sales v1.0 — Final Pilot Release
**Nordic Operations · Pilot til Reoler A/S**
Dato: 2026-07-29

---

## 1. Formål

Denne version gør LayoutScan Sales klar til den første pilot hos Reoler A/S. Som
instrueret er der **ikke** ændret i den eksisterende arbejdsgang (de samme 6 trin:
Kunde → Lager → Drift → Reoler → Layout → Resultat). Fotos, AI-analyse og noter er
tilføjet som **selvstændige, tilgængelige overlejringer** (knapper i wizard-headeren,
Layout-værktøjslinjen og Resultat-headeren) — ikke som nye trin, der ændrer
rækkefølgen.

## 2. Fotodokumentation

Et nyt "📷 Fotos"-modul er tilgængeligt fra alle skærme. Funktionalitet:

- Hurtigvalgs-knapper med de foreslåede titler (Facade, Port 1, Port 2,
  Varemodtagelse, Forsendelse, Søjler, El-tavler, Batterirum, Eksisterende reoler,
  Loft, Særlige forhold) — tryk på en af dem åbner filvælgeren med titlen forudfyldt.
- Billeder vises som thumbnails i et grid; tryk åbner en "lysboks" med billedet i fuld
  størrelse og redigerbare felter: titel, kommentar, dato, fotograf.
- Billeder kan slettes fra lysboksen.
- Alle billeder gemmes som Base64 data-URL'er direkte i projektet (ingen server, ingen
  cloud), og overlever gem/genindlæs af projektet som JSON (verificeret).
- Alle billeder kommer automatisk med i PDF-rapporten (se afsnit 7), i grupper af 6
  pr. side med titel, dato, fotograf og kommentar.

## 3-5. AI Layout Advisor, Kundespørgsmål og Risikovurdering

Et samlet "🤖 AI"-modul (ét knaptryk, tre sektioner) er tilføjet — også tilgængeligt
fra alle skærme. **Vigtig præcisering om "AI":** Dette er, som tillægget selv
beskriver ("ikke AI-generator"), ren regelbaseret analyselogik, der kører 100% lokalt
i browseren. Der sendes ingen data nogen steder, og der er ingen "Online AI",
maskinlæring eller cloud-tjeneste involveret — det er eksplicit forbudt i afsnit 8 og
er heller ikke implementeret. Alle tre sektioner er mærket "Regelbaseret" i UI'et, af
hensyn til gennemsigtighed over for både sælger og kunde, uden at det ændrer den
ønskede knapnavngivning ("AI Rådgiver" osv.), som I selv har valgt.

- **AI Rådgiver**: analyserer arealudnyttelse, reolkombination, truckvalg og
  gangbredder, og forklarer *hvorfor* i almindeligt sprog (fx *"Arealudnyttelsen er
  35% — der er sandsynligvis plads til yderligere kapacitet."*). Flytter aldrig
  objekter — kun tekst.
- **Kundespørgsmål**: viser de ønskede eksempelspørgsmål, med let tilpasning ud fra
  projektets data (fx spørges der specifikt ind til AGV/AMR, medmindre det allerede
  er valgt som sekundær truck; spørgsmålet om manuel pluk omformuleres, hvis
  lagertypen allerede er sat til "Pluklager").
- **Risikovurdering**: regelbaserede tjek for mulige opmærksomhedspunkter — flaskehals
  ved varemodtagelsen (reolrækker tæt på ind-/udleveringsområdet), lang truckafstand
  (beregnet fra lagerets hjørne til fjerneste reolrække), begrænset
  ekspansionsmulighed (høj arealudnyttelse), krydsende truckruter (flere
  rækkeorienteringer i samme layout), og for lille/manglende bufferzone. Ingen
  ændringer foretages automatisk — kun tekst.

## 6. Projektnoter

Et nyt "📝 Noter"-modul med et enkelt tekstfelt, tilgængeligt fra wizard-headeren.
Gemmes automatisk (samme auto-gem-mekanisme som resten af projektet) og kommer med i
PDF-rapporten.

## 7. Projektresumé & PDF

PDF-rapporten er omstruktureret fra 5 faste sider til en dynamisk opbygget rapport
med følgende sektioner, i denne rækkefølge:

1. **Forside** — logo, kundenavn, adresse, lagerbillede, projekt/dato/sælger.
2. **Projektresumé** — kundenavn, adresse, dato, ansvarlig sælger, lagerstørrelse,
   valgt truck, reoltyper, pallekapacitet, arealudnyttelse, antal konflikter,
   AI-sammenfatning, og et projektfoto (det først tilføjede billede, hvis der er
   nogen) — præcis som efterspurgt.
3. **Layout** — lagerbillede i stor størrelse med mål.
4. **Nøgletal** — producent, produktserier, status, primær/sekundære trucks, KPI-grid.
5. **AI-vurdering** — AI Rådgiver-punkterne og risikovurderingen (mærket tydeligt som
   regelbaseret analyse uden automatiske ændringer).
6. **Fotos** — alle projektbilleder i grupper af 6 pr. side, med titel, dato,
   fotograf og kommentar. Viser en tydelig besked, hvis der endnu ikke er tilføjet
   billeder.
7. **Materialeliste** — den kendte foreløbige stykliste.
8. **Noter & forbehold** — projektnoter (hvis udfyldt), det vejledende
   dispositionsforslag-forbehold, og kontaktoplysninger.

Sidetallet ("Side X / N") beregnes nu dynamisk ud fra det faktiske antal sider —
rapporten bliver typisk 8 sider eller flere, afhængig af antal fotos, i stedet for
den tidligere faste 5-siders skabelon.

## 8. Ingen ny kompleksitet

Der er ikke tilføjet 3D, Digital Twin, chatbot, cloud-lagring, login, database, online
AI eller eksterne integrationer. Alle nye funktioner (fotos, AI-analyse, noter) kører
udelukkende med lokal JavaScript-logik og gemmer data i det samme lokale
projekt-JSON som hidtil.

## 9. Kodekvalitet

- **Syntakskontrol**: `node --check` uden fejl.
- **HTML-validering**: `html5lib`-parsing uden fejl, ingen dublet-ID'er, ingen
  ubundne `getElementById`-referencer, fortsat ingen eksterne afhængigheder (én
  selvstændig fil, nu ca. 180 KB).
- **AutoCAD DXF-test**: DXF-generatoren er uændret fra tidligere versioner (som
  ønsket) og er gen-valideret med `ezdxf` efter denne opdatering — alle 9 lag,
  korrekt geometri, filen afsluttes korrekt.
- **iPad Safari, Edge, Chrome**: Se testplanens vigtige forbehold i afsnit under
  punkt 10 — disse er **ikke** fysisk testet i dette miljø, da en rigtig browser ikke
  kan køres her. Se testplanen for hvad der i stedet er verificeret automatisk.

---

**Version:** LayoutScan Sales v1.0 — Final Pilot Release
**GitHub commit (forslag):** "Photo documentation module, rule-based AI advisor
(recommendations, customer questions, risk assessment), project notes, and a fully
restructured professional PDF report with dynamic page count."

Dette er et vejledende dispositionsforslag. Belastninger, montage, gulvforhold,
brandsikkerhed og konstruktive krav skal godkendes af Reoler A/S før levering.
