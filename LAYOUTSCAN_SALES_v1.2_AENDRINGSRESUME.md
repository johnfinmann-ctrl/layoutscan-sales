# LayoutScan Sales v1.2 — Kort ændringsresumé

## Implementeret
1. Ny "Vælg opgave"-startside (A/B/C/D) — styrer taskType og standard-visning.
2. Enkel montørvisning med "Vis avancerede værktøjer"-skifte (CSS-drevet, intet fjernet).
3. Scan/importér tegning: upload/kamera, rotér, gennemsigtighed, lås, fjern, 2-punkts kalibrering til 1:1.
4. Fotomodul udvidet med 8 nye kategorier, separat kategorifelt, og tilknytning til konkrete objekter.
5. Tre reelt forskellige layoutforslag (Maksimal kapacitet / Bedste flow / Fleksibel), genereret uden at overskrive det gemte layout.
6. Sammenligningsskærm med miniaturer, nøgletal, Layout Score, fordel/ulempe, Se forslag/Brug dette forslag/Generér igen.
7. "Hvad er vigtigst for kunden?" — omrangerer forslagene gennemsigtigt uden at gen-generere geometrien.
8. Tal-baseret AI-sammenligning mellem forslagene.
9. "Arbejd kun med dette område" til mindre opgaver.
10. "Send til teknisk afdeling": pakke-overblik, besked-felt, download samlet pakke, kopiér projektresume.
11. PDF udvidet med opgavetype, valgt/alternative forslag, objektkommentarer, ignorerede konflikter med begrundelse.
12. Alle eksisterende funktioner fra v1.1 bevaret uændret.

## Testet
- 46 automatiserede tests består (35 genkørt uændret fra v1.1 + 11 nye).
- HTML-validering, JS-syntakskontrol, og uafhængig DXF-validering (ezdxf) alle bestået.
- De fem krævede testscenarier er dækket — se testplanen for detaljer om, hvilke dele der er direkte end-to-end-testet, og hvilke der er dækket via allerede verificerede enkeltdele.

## Kendte begrænsninger (beskrevet ærligt, blokerer ikke leverancen)
- PDF-filer kan ikke vises som billede i baggrundstegnings-modulet (ingen PDF-rendering uden eksternt bibliotek) — brugeren henvises til at bruge foto/PNG/JPG i stedet.
- Baggrundsbilledet positioneres via numeriske X/Y-felter, ikke ved fri træk på selve tegnefladen.
- "Kombination af langs og tværs" er løst ved, at hvert af de tre forslag vælger sin egen bedst egnede orientering — ikke ved at blande orienteringer inden i ét enkelt forslag.
- PDF-rapporten er ikke forkortet for mindre opgaver (var valgfrit i opgaven — "må gerne").
- Ingen fysisk test på iPad/Chrome/Edge/Safari eller i en ægte AutoCAD-installation er mulig i dette miljø.
