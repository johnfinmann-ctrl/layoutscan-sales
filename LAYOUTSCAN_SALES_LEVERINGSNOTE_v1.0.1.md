# LayoutScan Sales v1.0.1 — Review og tilretninger
**Nordic Operations · Pilot til Reoler A/S**
Dato: 2026-07-31

---

## Vigtigst: en reel fejl blev fundet og rettet

Gennemgangens punkt 5 ("Fejl") beskrev, at flytning af et allerede placeret objekt
(Kontor, Brandvej, Område osv.) i stedet oprettede en kopi. Dette er nu rettet — og
undervejs fandt jeg **endnu en fejl** af samme rod, som ikke var rapporteret, men som
havde samme årsag.

**Årsagen:** Da "vedvarende placeringstilstand" blev indført (så man kan placere
Port, Port, Port i træk), blev den lavet global — den gjaldt for ALLE objekttyper,
ikke kun dem der naturligt placeres mange gange. Det betød, at et tryk på et
allerede-placeret Kontor (for at flytte det) i virkeligheden blev tolket som "placér
endnu et Kontor", fordi appen stadig var i placeringstilstand.

**Rettelsen:** Kun objekter der naturligt placeres mange gange i træk — **Port, Dør,
Søjle og Reoler** — forbliver nu i placeringstilstand efter en placering. Alle andre
objekter (Kontor, Teknikrum, Batterirum, Ladestation, Brandvej, Flugtvej, Forbudt
område, Varemodtagelse, Forsendelse, Buffer) afslutter automatisk placeringen efter
ét tryk, så det næste tryk på objektet vælger og kan flytte det — præcis Placér →
Vælg → Flyt → Redigér, som ønsket.

**Bonusfejl fundet under samme gennemgang:** Manuel placering af en reol via
værktøjspaletten (tryk-for-at-placere) kaldte en intern funktion med parametrene i
forkert rækkefølge, hvilket ville have givet en reol med ugyldige mål/position, hvis
den var blevet ramt i praksis. Dette var ikke rapporteret af jer, men blev fundet ved
at skrive en ny automatiseret test specifikt for denne kodesti — nu rettet og
verificeret.

## 1. Palletyper

Udvidet fra 4 til 11 typer: EUR-palle, Industripalle, Halvpalle, **Kvartpalle**,
**Langgods**, **Tromler**, **Kasser**, **Plastkasser**, **Smågods**,
**Udtræksskuffer** (den efterspurgte, nu tilføjet) og "Andre lagerbærere"
(brugerdefineret). Mål er vejledende standardstørrelser, ligesom ved reoltyperne —
"Langgods" og "Andre lagerbærere" låser bredde/dybde/højde op til manuel indtastning,
da disse varierer for meget til en fast standard.

Der er tilføjet **Primær** og **Sekundær palletype** — samme mønster som primær/
sekundær truck. Sekundær palletype er til rapport/overblik og påvirker ikke selve
beregningen, ligesom sekundære trucks.

## 2. Drift — kapacitetsestimat

Et nyt kort **"📐 Forventet kapacitet"** er tilføjet nederst i Trin 3. Det giver et
hurtigt, forenklet estimat — baseret på lagermål (Trin 2), den primære reoltype og
den primære truck — af forventet antal reolrækker, pallepladser og
kapacitetsudnyttelse, og opdateres live når sælgeren ændrer ønsket antal pallepladser
eller trucks. Hvis det ønskede antal sandsynligvis ikke kan nås, forklares hvorfor
(fx "lageret er for lille i forhold til reoltype X's dybde og Y's gangbredde") samt
konkrete forslag (anden truck, tættere reoltype, eller reducér ønsket antal).

**Vigtig præcisering:** Dette er bevidst et *hurtigt estimat* til brug under selve
kundebesøget — ikke den endelige beregning. Det tager IKKE højde for porte, søjler
eller andre forhindringer, da disse først placeres i Trin 5. Det er tydeligt skrevet
i UI'et, og det faktiske, præcise tal beregnes fortsat af layoutgeneratoren i Trin 5.

## 3. Brugerdefineret truck

Der er nu en forklarende linje direkte under knappen: *"Kun til specialtrucks, der
ikke findes på listen ovenfor — fx en ATEX-godkendt truck eller en smal,
specialbygget maskine. De fleste lagre kan bruge standardlisten uden at skulle
tilføje noget her."* Funktionen er bevaret (ikke skjult), da den stadig kan have
værdi for de kunder, der reelt har en specialtruck — men formålet er nu tydeligt, som
ønsket.

## 4. Produktserie / palletype-sammenhæng

Dette punkt bad om en *undersøgelse*, ikke nødvendigvis en kodeændring. Vurdering: i
den nuværende arkitektur er reoltype (Trin 4) og palletype (Trin 3) allerede
uafhængige valg, der begge indgår i kapacitetsberegningen (pallepladser pr. niveau
ganges med reolfag, uanset palletype). At koble dem tættere sammen — fx automatisk
foreslå "Udtræksskuffer" kun sammen med bestemte reolserier, eller advare hvis
palletype og reoltype ikke passer sammen — vil kræve en kompatibilitetsmatrix, som
Reoler A/S bør levere fagligt korrekte data til (i stil med Metalsistem-strukturen),
frem for at jeg gætter forkerte sammenhænge. Jeg har **ikke** implementeret en sådan
matrix i denne omgang, for at undgå at opfinde tekniske sammenhænge, der kan vise sig
forkerte. Dette bør tages op som et konkret punkt til Reoler A/S, hvis det skal
implementeres i en senere version.

## 5. Layout — objekter kan flyttes/roteres/ændre mål

Ud over selve fejlrettelsen (se ovenfor) var denne funktionalitet allerede til stede:
Egenskabspanelet viser bredde, dybde og rotation for **alle** objekttyper — ikke kun
reoler — og de samme træk/rotér/skalér-håndtag på tegnefladen virker universelt.
Problemet var udelukkende, at man aldrig nåede frem til at bruge dem, fordi
placeringstilstanden forhindrede det. Med fejlrettelsen fungerer hele kæden
Placér → Vælg → Flyt → Redigér nu som forventet for alle objekttyper.

## 6-7. "Objekter" og "Egenskaber" — forklaring

Der er tilføjet korte, faste forklaringstekster øverst i begge paneler direkte i
appen:
- **Objekter**: *"Tryk på et objekt for at placere det på tegningen. Tryk derefter på
  det placerede objekt for at vælge, flytte, dreje eller redigere det i
  'Egenskaber' til højre."*
- **Egenskaber**: *"Vises automatisk for det objekt, du har valgt på tegningen.
  Felterne tilpasser sig objekttypen (fx antal fag og niveauer for reoler)."*

## 8. Scan tegning / "Brug eksisterende tegning"

**Dette er ikke implementeret i denne omgang** — det er den største udestående opgave
fra denne gennemgang. Årsagen er ærlig prioritering: dette er den mest omfattende
nye funktion i listen (billed-/PDF-import, kalibrering af skala og placering,
visning som baggrundslag på tegnefladen), og jeg har prioriteret at først rette den
kritiske "kopi i stedet for flyt"-fejl grundigt og verificere den med nye
automatiserede tests, frem for at forcere endnu en stor funktion igennem med
utilstrækkelig test. Den nuværende, veletablerede arbejdsgang (hurtig opmåling og
tegning fra bunden) er upåvirket og fortsat standard, som ønsket i punkt 9.

**Forslag til næste version:** en valgfri knap i Trin 2, der lader sælgeren
uploade et foto/billede af en eksisterende tegning, som vises som et
gennemsigtigt baggrundslag på tegnefladen til at tegne udenpå — uden manuel
hjørne-kalibrering i første omgang (billedet auto-tilpasses bygningens
omridsmål). Reelt PDF- og DXF-import er teknisk tungere og bør, som I selv
skriver, komme "senere".

## 9. Arbejdsgang

Ingen kodeændringer nødvendige — det beskrevne virkelighedsflow (opmål → tegn
hurtigt → placér forhold → vis forslag → tag fotos → aflever PDF/DXF) er allerede
appens standardflow, og forbliver det, da punkt 8 ikke er implementeret som andet
end en fremtidig, valgfri tilføjelse.

---

**Version:** LayoutScan Sales v1.0.1 — Review og tilretninger
**GitHub commit (forslag):** "Fix critical placement-mode bug (copy instead of move
for single-instance objects), fix rack tap-to-place argument-order bug, expand
pallet/carrier types with primary/secondary selection, add a live capacity estimator
in Drift, clarify custom truck purpose, and add inline help text for Objects/
Properties panels."

Dette er et vejledende dispositionsforslag. Belastninger, montage, gulvforhold,
brandsikkerhed og konstruktive krav skal godkendes af Reoler A/S før levering.
