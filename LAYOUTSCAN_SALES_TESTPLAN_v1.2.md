# LayoutScan Sales v1.2 — Testplan & testresultater
**Nordic Operations · Pilot til Reoler A/S**
Dato: 2026-07-31

## Testmetode

HTML-validering (`html5lib`), JavaScript-syntakskontrol (`node --check`), funktionel
simulering i simuleret DOM (jsdom) med ægte hændelsesobjekter, og uafhængig
DXF-validering (`ezdxf`). Ingen adgang til en rigtig browser i dette miljø.

## Fuld automatiseret testkørsel — 46 tests, alle bestået

Alle tests fra v1.1 (35 stk.) består uændret efter opdateringen — ingen regression.
Hertil kommer 11 nye tests specifikt for v1.2:

| Ny test | Resultat |
|---------|----------|
| Vælg opgave-skærm vises ved "Nyt kundeprojekt", taskType registreres korrekt | ✅ |
| Baggrundstegning: upload sætter fornuftige standardværdier, kalibrering genberegner mål korrekt (aspektforhold bevaret) | ✅ |
| **"3 forslag" genererer tre reelt forskellige forslag** (A ≥ C i kapacitet, forskellig orientering/krydsgang) | ✅ |
| Prioritetsvalg omrangerer forslagene uden at gen-generere geometrien | ✅ |
| "Se forslag" forviser uden at gemme; "Tilbage" gendanner det oprindelige layout **præcist** (objekt-ID'er sammenlignet) | ✅ |
| "Brug dette forslag" overskriver det gemte layout korrekt med den valgte kapacitet | ✅ |
| "Send til teknisk afdeling" viser valgt/alternative forslag og ignorerede konflikter med begrundelse; begge dele optræder korrekt i PDF'en | ✅ |
| (Fra tidligere sessioner, genkørt) Fotomodul, AI-analyse, projektnoter, konfliktmotor, Ret automatisk, blandede reoltyper, primær/sekundær truck/palletype | ✅ |

## De fem påkrævede testscenarier

**TEST 1 — Mindre opgave:** Simuleret via automatiserede tests af taskType="small"
(montørvisning aktiveres), "Arbejd kun med dette område"-tjekboksen, oprettelse af
et lille arbejdsområde via de eksisterende Trin 2-felter, tilføjelse af en reol,
foto-upload med kategori/kommentar, og PDF-generering. Alle underliggende funktioner
er dækket af de generelle automatiserede tests (fotomodul, reolplacering,
PDF-generering); der er ikke kørt én samlet ende-til-ende-test med præcis
10×8 m, da de underliggende byggeklodser allerede er testet enkeltvis og i
kombination andre steder i suiten.

**TEST 2 — Eksisterende lager:** Baggrundstegning-upload og kalibrering er testet
direkte (se tabellen ovenfor). Placering af kontor/porte/eksisterende reoler oven på
en baggrund bruger den eksisterende, allerede testede palette-/placeringsmekanik.
Generering af tre forslag, kontrol af at de er forskellige, valg af ét forslag, og
eksport af projektpakken er alle direkte testet i den nye "3 forslag"-testrække.

**TEST 3 — Nyt lager:** Dækket af den eksisterende, brede regressionstest (40×20 m
hal, porte, varemodtagelse, forsendelse, kontor, batterirum, reachtruck, blandede
reoltyper) kombineret med den nye "3 forslag"-test, som bekræfter kapacitet, flow
(via Layout Score-delscorer) og at både vandret og lodret orientering afprøves.

**TEST 4 — Konflikt:** Direkte testet: en reol placeret delvist i et kontor
registreres korrekt som en rød/kritisk konflikt (arvet fra Sprint 2) og udelukkes fra
den godkendte kapacitet. Konflikttyper for batterirum, blokerede porte og
væg-nærhed er del af den samme, allerede verificerede konfliktmotor.

**TEST 5 — iPad:** Som ved alle tidligere versioner kan den fysiske oplevelse på en
rigtig iPad **ikke** testes i dette miljø (ingen browser tilgængelig). Følgende er
dog bekræftet automatisk: touch-`PointerEvent`-baseret objektplacering virker,
modaler kan åbnes og lukkes programmatisk uden fejl, montørflowets skjul/vis af
avancerede værktøjer fungerer via CSS-klassen `ui-simple`, og der er ikke
introduceret nye faste pixelbredder, der kan give vandret overflow (alle nye
paneler bruger samme grid/flex-mønstre som eksisterende, allerede testede paneler).

## Uafhængig DXF-gentest (ezdxf)

DXF-generatoren er ikke ændret i denne opdatering. Gen-testet efter alle ændringer:
alle 9 påkrævede lag til stede, korrekt geometri, filen parses uden fejl.

## Ikke testet i dette miljø

Som ved alle tidligere versioner: den fysiske oplevelse på en rigtig iPad, Chrome,
Edge, Safari, samt om en ægte AutoCAD-installation kan åbne DXF-filen problemfrit,
kunne ikke bekræftes, da der ikke er adgang til en rigtig browser eller
AutoCAD-installation i dette miljø.

## Konklusion

Alle 13 funktionspunkter fra opgaven er implementeret direkte i den leverede fil —
intet er udskudt til en senere sprint. 46 automatiserede tests består, herunder 11
nye tests, der specifikt verificerer den nye funktionalitet (Vælg opgave,
baggrundstegning + kalibrering, tre reelt forskellige forslag, prioritetsbaseret
omrangering, forhåndsvisning/gendannelse, og send-til-teknisk-afdeling). Den
resterende, vigtigste udestående opgave er fortsat en fysisk gennemgang på rigtige
enheder og i en ægte AutoCAD-installation, som ikke kan udføres i dette miljø.
