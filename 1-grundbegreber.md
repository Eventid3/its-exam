---
author: Esben Inglev
theme: gaia
date: Jan 2026
paginate: true
style: |
  section {
    font-size: 30px;
  }
---

# Grundbegreberne inden for IT-sikkerhed

Forklar de vigtigste grundbegreber indenfor IT-sikkerhed, og relater til
den øvrige undervisning og øvelser.
Gennemgå ’best practice steps’ for ’risk management’ for organisationer.
Perspektiver til ISO 27000-familien.

---

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

### Grundbegreber

**Vulnerabilities**

- En svaghed i systemet

---

### Grundbegreber

**Threats**

- Forskellige origins
  - Menneskelige og naturlige
- Utilsigtet
- Tilsigtet
  - Tilfældige
  - Rettet

---

### Grundbegreber

**Angribere (Advisaries)**

- Individer, organisationer eller lande
- Har ofte MOM
  - Method
  - Oppertunity
  - Motive

---

### Grundbegreber

**C-I-A Triad**

- Confidentiality
- Integrity
- Availability
- Authentication
- Acountability

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

---

### Grundbegreber

**Vulneratility-Threat-Control Paradigm**

_A Threat is blocked by a Control of a Vulneratility_

---

### Risk Management

- Resourcer er begrænsede
- Der skal prioriteres ud fra
  - Risk Assesment
  - Risk Analysis
  - Risk Management

---

### Risk Management

**Risk Analysis Steps**

1. Identify assets.
2. Determine vulnerabilities.
3. Estimate likelihood of exploitation.
4. Compute expected annual loss.
5. Survey applicable controls and their costs.
6. Project annual savings of control.

---

### Risk Management - Identify Assets

- Hardware
- Sofware
- Data
- Mennesker
- ...

---

### Risk Management - Determine vulnerabilities

| Asset    | Confidentiality | Integrity        | Availability |
| -------- | --------------- | ---------------- | ------------ |
| Hardware |                 | tampering        | overloaded   |
| Software | stolen          | trojan horse     | deleted      |
| Data     | leaked          | corrpted/damaged | deleted      |
| People   |                 |                  | retired/quit |

---

### Risk Management - Estimate likelihood of exploitation

- Det er umuligt på forhånd at kende til alle systemets vulnerabilities
- Mulige estimerings metoder
  - Kig på ovserveret data fra lignende systemer
  - Brug en expert udefra der kender lignende systemer
  - Brug et rating system

---

### Risk Management - Compute expected annual loss

- Tydelige costs: hardware der skal erstattes
- Skjulte costs
  - Genoprette tidligere data/state
  - Downtime cost
  - Legal fees
  - Tab af omdømme, selvsikkerhed og confidentiality
- Kan være svært at sætte exact pris på

---

### Risk Management - Survey and select new Controls

- Når man kender assets, vulnerabilities, likelihood of exploitation, cost of exploitation, kan man vælge controls
- En vulnerability kan kræve flere controls, og en control kan også virke for flere vulnerabilities.

---

### Risk Management - Project Costs and Savings

- Opvejer prisen for de valgte controls de sikkerhedsmæssige downsides?
- Prisen skal være et samlet billede efter en control er fuldt implementeret

---

### ISO 27000

- ISO 27000 familien er en serie af standarder/dokumenter
- Beskriver best practices indenfor information security management

---

### ISO 27001

- Kan ses som en specifik implementering af Risk Management best practice steps
- Beskriver hvordan man skal håndtere informations sikkerhed
- Virksomheder kan blive certificeret - obligatorisk for statslige virksomheder
- Har en række dokumenter der skal udfyldes
  - Assets, vulnerabilities, threats
  - Impact, likelihood
- Har en liste af controls i Annex A
