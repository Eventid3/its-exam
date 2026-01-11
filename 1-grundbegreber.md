---
author: Esben Inglev
theme: gaia
date: Jan 2026
paginate: true
style: |
  section {
    font-size: 26px;
  }
---

# Grundbegreberne inden for IT-sikkerhed

---

<!--
Talking points:
- Lad os starte med det mest grundlæggende: Assets.
- Et asset er alt, hvad der har værdi for en virksomhed. Det er det, vi ønsker at beskytte.
- Hvad der er et asset, varierer fra virksomhed til virksomhed. For en tech-virksomhed kan det være kildekode, for et hospital kan det være patientjournaler.
- Værdien kan også ændre sig over tid. Tænk på data om et produkt, der endnu ikke er lanceret.
- For at forstå et asset, laver man en analyse: Hvad er værdien? Hvor længe kan vi undvære det? Hvad afhænger af det?
-->

### Grundbegreber

**Assets**

- Ting der har værdi
- Afhængigt af virksomhed
- Kan være tidsafhængigt
- Kan have tilknyttet en asset analyse
  - Værdi
  - Kan den undværes, og i hvor lang tid
  - Afhængigheder

---

<!--
Talking points:
- Det næste begreb er "Vulnerabilities" eller sårbarheder.
- En sårbarhed er simpelthen en svaghed i et system. Det er en åbning, som en trussel kan udnytte.
- Eksempler: En server, der mangler den seneste sikkerhedsopdatering, en svag adgangskodepolitik, en ulåst dør til serverrummet.
-->

### Grundbegreber

**Vulnerabilities**

- En svaghed i systemet
  - Kan være både fysisk og digital

---

<!--
Talking points:
- Nu til "Threats" eller trusler.
- En trussel er en potentiel fare, der kan udnytte en sårbarhed.
- Trusler kan komme fra forskellige kilder: Menneskelige (en hacker) eller naturlige (en oversvømmelse).
- De kan være utilsigtede (en medarbejder sletter ved en fejl data) eller tilsigtede (et målrettet angreb).
- Tilsigtede trusler kan være tilfældige (en hacker scanner nettet for enhver sårbar maskine) eller rettet mod en specifik organisation.
-->

### Grundbegreber

**Threats**

- Forskellige origins
  - Menneskelige og naturlige
- Utilsigtet
- Tilsigtet
  - Tilfældige
  - Målrettet

---

### Grundbegreber

**Angribere (Adversaries)**

- Individer, organisationer eller lande
- Har ofte MOM
  - Method
  - Oppertunity
  - Motive

<!--
Talking points:
- Hvem er angriberne så? Vi kalder dem "Adversaries".
- Det kan være alt fra enkeltpersoner (script kiddies, hacktivister) til organiserede grupper (kriminelle) eller endda stater (cyberspionage).
- For at forstå en angriber, bruger man ofte MOM-modellen: Method (metode, værktøjer), Opportunity (mulighed, dvs. en sårbarhed) og Motive (motiv, f.eks. penge, politik, hævn).
-->

---

### Grundbegreber

**C-I-A Triad**

- Confidentiality
- Integrity
- Availability
- (Authentication)
- (Acountability)

<!--
Talking points:
- For at strukturere vores beskyttelse, bruger vi C-I-A-triaden. Det er de tre hjørnesten i informationssikkerhed.
- Confidentiality (fortrolighed): At holde data hemmelige for uvedkommende. Kryptering er et eksempel.
- Integrity (integritet): At sikre, at data er korrekte og ikke er blevet manipuleret. Digitale signaturer er et eksempel.
- Availability (tilgængelighed): At sikre, at systemer og data er tilgængelige for brugerne, når de har brug for det. Redundans og backups er eksempler.
- Ofte tilføjer man to mere: Authentication (at bekræfte en brugers identitet) og Accountability (at kunne spore handlinger til en specifik bruger, også kaldet non-repudiation).
-->

---

### Grundbegreber

**Controls**
En countermeasure på en/flere threats

- Prevent
- Deter
- Deflect
- Mitigate
- Detect
- Recover

Kan også være fysisk

<!--
Talking points:
- Hvordan beskytter vi os så? Med "Controls" eller sikkerhedsforanstaltninger.
- En control er en modforanstaltning, der skal håndtere en eller flere trusler.
- De kan kategoriseres efter deres funktion:
- Prevent (forhindre): F.eks. en firewall.
- Deter (afskrække): F.eks. en advarsel om overvågning.
- Deflect (aflede): Gøre et andet mål mere attraktivt.
- Mitigate (mindske): Reducere skadens omfang, f.eks. med backups.
- Detect (opdage): F.eks. et Intrusion Detection System.
- Recover (gendanne): Genskabe normal drift efter et angreb.
- Vigtigt at huske, at controls også kan være fysiske, som en lås eller en vagt.
-->

---

### Grundbegreber

**Vulnerability-Threat-Control Paradigm**

_A Threat is blocked by a Control of a Vulneratility_

<!--
Talking points:
- Dette paradigme opsummerer sammenhængen mellem de begreber, vi lige har gennemgået.
- "En Trussel bliver blokeret af en Control af en Sårbarhed."
- Med andre ord: En trussel forsøger at udnytte en sårbarhed for at skade et asset. Vi implementerer en control for at blokere truslen og beskytte sårbarheden.
- Dette er kernen i defensiv IT-sikkerhed.
-->

---

### Risk Management

- Resourcer er begrænsede
- Der skal prioriteres ud fra
  - Risk Assesment
  - Risk Analysis
  - Risk Management

<!--
Talking points:
- Nu bevæger vi os over i "Risk Management" eller risikostyring.
- Pointen er, at man har begrænsede ressourcer (penge, tid, mennesker). Man kan ikke beskytte sig 100% mod alt.
- Derfor skal man prioritere. Denne prioritering sker gennem en struktureret proces, der involverer:
- Risk Assessment (identifikation af risici).
- Risk Analysis (vurdering af risicienes alvorlighed).
- Risk Management (beslutning om, hvordan risiciene skal håndteres).
-->

---

### Risk Management

**Risk Analysis Steps**

1. Identify assets.
2. Determine vulnerabilities.
3. Estimate likelihood of exploitation.
4. Compute expected annual loss.
5. Survey applicable controls and their costs.
6. Project annual savings of control.

<!--
Talking points:
- Her er en klassisk 6-trins model for riskoanalyse. Den giver en systematisk tilgang.
- 1. Identificer assets: Hvad er det, vi vil beskytte?
- 2. Find sårbarheder: Hvordan kan vores assets blive angrebet?
- 3. Estimer sandsynligheden for udnyttelse: Hvor sandsynligt er det, at en sårbarhed bliver udnyttet?
- 4. Beregn forventet årligt tab (ALE): Hvad vil det koste os, hvis det sker?
- 5. Undersøg mulige controls og deres omkostninger: Hvad kan vi gøre ved det, og hvad koster det?
- 6. Projektér de årlige besparelser ved en control: Kan det betale sig at implementere den?
- Vi gennemgår nu hvert trin kort.
-->

---

### Risk Management - Identify Assets

- Hardware
- Sofware
- Data
- Mennesker
- ...

<!--
Talking points:
- Første skridt er at identificere sine assets.
- Som nævnt kan det være mange forskellige ting.
- Her er nogle klassiske eksempler: Fysisk hardware som servere og laptops, software som operativsystemer og applikationer, data som kundedatabaser og intellektuel ejendom, og endda mennesker med specialviden. Listen er ikke udtømmende.
-->

---

### Risk Management - Determine vulnerabilities

| Asset    | Confidentiality | Integrity        | Availability |
| -------- | --------------- | ---------------- | ------------ |
| Hardware |                 | tampering        | overloaded   |
| Software | stolen          | trojan horse     | deleted      |
| Data     | leaked          | corrpted/damaged | deleted      |
| People   |                 |                  | retired/quit |

<!--
Talking points:
- Andet skridt er at bestemme sårbarheder for hvert asset i forhold til C-I-A triaden.
- Tabellen her giver eksempler.
- For Hardware: Integritet kan trues af "tampering" (fysisk manipulation), og tilgængelighed kan trues, hvis serveren bliver "overloaded".
- For Software: Fortrolighed trues, hvis koden bliver "stolen", og integritet hvis en "trojan horse" indsættes.
- For Data: Kan blive "leaked" (fortrolighed), "corrupted" (integritet) eller "deleted" (tilgængelighed).
- For Mennesker: Tilgængeligheden af deres viden forsvinder, hvis de siger op.
-->

---

### Risk Management - Estimate likelihood of exploitation

- Det er umuligt på forhånd at kende til alle systemets vulnerabilities
- Mulige estimerings metoder
  - Kig på observeret data fra lignende systemer
  - Brug en expert udefra der kender lignende systemer
  - Brug et rating system

<!--
Talking points:
- Tredje skridt er at estimere sandsynligheden. Dette er ofte det sværeste.
- Man kan aldrig kende alle sårbarheder på forhånd.
- Derfor bruger man estimeringsmetoder:
- Man kan se på historiske data fra lignende systemer – hvor ofte er de blevet angrebet?
- Man kan hyre en ekstern ekspert, der har erfaring fra branchen.
- Eller man kan bruge et mere kvalitativt rating-system som "høj", "middel", "lav" sandsynlighed.
-->

---

### Risk Management - Compute expected annual loss

- Tydelige costs: hardware der skal erstattes
- Skjulte costs
  - Genoprette tidligere data/state
  - Downtime cost
  - Legal fees
  - Tab af omdømme, selvsikkerhed og confidentiality
- Kan være svært at sætte exact pris på

<!--
Talking points:
- Fjerde skridt er at beregne, hvad en hændelse vil koste.
- Der er de tydelige omkostninger: En ødelagt server, der skal erstattes.
- Men de skjulte omkostninger er ofte meget større: omkostninger til at genskabe tabt data, tabt omsætning pga. nedetid, eventuelle bøder eller sagsanlæg, og ikke mindst tab af omdømme og kundetillid.
- Det er svært at sætte en præcis pris på alt dette, men det er nødvendigt for at kunne prioritere.
-->

---

### Risk Management - Survey and select new Controls

- Når man kender assets, vulnerabilities, likelihood of exploitation, cost of exploitation, kan man vælge controls
- En vulnerability kan kræve flere controls, og en control kan også virke for flere vulnerabilities.

<!--
Talking points:
- Femte skridt: Vælg de rigtige controls.
- Nu har vi viden om assets, sårbarheder, sandsynlighed og omkostninger. Med den viden kan vi træffe informerede valg.
- Man skal finde de controls, der bedst adresserer de mest kritiske risici.
- Det er vigtigt at forstå, at en enkelt sårbarhed kan kræve flere controls, og omvendt kan én control dække flere sårbarheder. Det er et komplekst puslespil.
-->

---

### Risk Management - Project Costs and Savings

- Opvejer prisen for de valgte controls de sikkerhedsmæssige downsides?
- Prisen skal være et samlet billede efter en control er fuldt implementeret

<!--
Talking points:
- Sidste skridt: Cost-benefit analyse.
- Her stiller vi det afgørende spørgsmål: Kan det betale sig? Opvejer prisen for en control de potentielle tab, den forhindrer?
- Prisen skal ses som den samlede omkostning over tid: indkøb, implementering, vedligeholdelse, oplæring osv.
- En control skal kun implementeres, hvis besparelsen (den undgåede risiko) er større end omkostningen.
-->

---

### ISO 27000

- ISO 27000 familien er en serie af standarder/dokumenter
- Beskriver best practices indenfor information security management

<!--
Talking points:
- Til sidst, lad os kigge på ISO 27000-familien.
- Dette er en internationalt anerkendt serie af standarder for informationssikkerhed.
- De beskriver "best practices" for, hvordan man etablerer, implementerer, vedligeholder og forbedrer it sikkerhed
-->

---

### ISO 27001

- Kan ses som en specifik implementering af Risk Management best practice steps
- Beskriver hvordan man skal håndtere informations sikkerhed
- Virksomheder kan blive certificeret - obligatorisk for statslige virksomheder
- Har en række dokumenter der skal udfyldes
  - Assets, vulnerabilities, threats
  - Impact, likelihood
- Har en liste af controls i Annex A

<!--
Talking points:
- Den mest kendte standard i familien er ISO 27001.
- Man kan se den som en konkret opskrift på, hvordan man implementerer de risikostyrings-principper, vi lige har talt om.
- Den beskriver præcist, hvordan man skal håndtere informationssikkerhed.
- Virksomheder kan blive certificeret i ISO 27001, hvilket viser over for kunder og partnere, at man tager sikkerhed alvorligt. For statslige virksomheder i Danmark er det et krav.
- Processen kræver en masse dokumentation: assets, sårbarheder, trusler, impact, likelihood – præcis som i vores model.
- Annex A i standarden indeholder en lang liste af mulige controls, man kan lade sig inspirere af.
-->

---
