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

# 10 (U)SIKRE MENNESKER, NETVÆRK OG SYSTEMER – BLOCKCHAIN

<!--
Talking points:
- Blockchain er distribueret database uden central autoritet
- Løser Byzantine Generals Problem - consensus blandt mistroiske parter
- Proof-of-Work mining sikrer at det er dyrt at manipulere blockchain
- Praktiske øvelser viste difficulty scaling - exponentially harder med flere zeros
- Blockchain har både fordele (decentralisering, immutability) og ulemper (energi, skalerbarhed)
- Alternative consensus mekanismer som Proof-of-Stake adresserer nogle problemer
-->

---

### Hvad er en blockchain?

<!--
Talking points:
- Blockchain er distribueret ledger/database delt mellem mange noder
- Data organiseret i blokke linket sammen med kryptografiske hashes
- Ingen central autoritet eller trusted third party (bank, government)
- **Immutable:** Når data er tilføjet, kan det ikke ændres uden at bryde kæden
- Historik er permanent og transparent for alle deltagere
- Kryptografi og consensus mekanismer sikrer data integritet
-->

- Distribueret database/register
- Data organiseret i "blokke" der er kædet sammen
- Ingen central autoritet
- Immutable (uforanderlig) historik

---

### Problemet: Distribueret konsensus

<!--
Talking points:
- **Byzantine Generals Problem:** Hvordan opnår distribuerede parter enighed når nogle kan være ondskabsfulde?
- Klassisk central løsning: Bank holder databasen, alle stoler på banken
- Men hvad hvis banken er korrupt, hacket eller går konkurs?
- **Uden blockchain:** Hvem bestemmer sandheden? Hvem verificerer transaktioner?
- Hvad hvis nogen snyder og dobbeltbruger penge (double spending)?
- **Blockchain løser:** Consensus uden at stole på en central part
- Matematisk bevis (proof-of-work) i stedet for tillid til autoritet
-->

**Scenarie uden blockchain:**

- Hvordan bliver mange parter enige om sandheden?
- Hvem kan vi stole på?
- Hvad hvis nogen snyder?

**Blockchain løser:**

- Consensus uden central autoritet

---

### Forudsætninger for blockchain

<!--
Talking points:
- **Giver mening når:** Multiple stakeholders skal dele data men ingen stoler på hinanden
- Eksempel: Supply chain tracking mellem mange firmaer
- **Ingen trusted party:** Ingen neutral tredjepart alle kan stole på
- Eller central part ville have for meget magt
- **Transparens ønsket:** Alle skal kunne verificere transaktioner
- Public ledger hvor alle kan audit
- **Immutability kritisk:** Historik må ikke kunne ændres (auditability, compliance)
- **Giver IKKE mening:** Enkelt organisation - traditionel database er hurtigere og billigere
- Hastighed vigtigere end decentralisering - blockchain er langsom (7-15 TPS Bitcoin)
-->

**Blockchain giver mening når:**

- Flere parter skal dele data
- Ingen central trusted party
- Transparens er ønsket
- Immutability er vigtig

**Blockchain giver IKKE mening når:**

- En database med én ejer er nok
- Hastighed > decentralisering

---

### Block-struktur

<!--
Talking points:
- Hver block indeholder multiple transactions og metadata
- **Nonce:** Random number miners skal finde for at løse proof-of-work puzzle
- Varieres millioner af gange indtil hash opfylder difficulty target
- **Merkle Tree:** Effektiv datastruktur til at hash alle transactions
- Root hash repræsenterer alle transactions - ændring i én transaction ændrer root
- Gør det muligt at verificere enkelte transactions uden at downloade hele block
- **Previous Block Hash:** Linker til forrige block og skaber kæden
-->

![Block](img/blockchain-block.png)

- Nonce: det miners skal regne ud
- Merkle Tree: transactions hashet i en træ-struktur

---

### Block-struktur - Transaction

<!--
Talking points:
- Transactions bruger asymmetrisk kryptering for sikkerhed
- **Public key** fungerer som wallet adresse - identificerer sender
- Alle kan se din public key og verificere din signatur
- **Private key** beviser ejerskab - kun ejer kan signere transactions
- Digital signatur sikrer at kun du kan bruge dine coins
- **Chain of Trust parallel:** Ligner certificate chains
- Root of trust er din private key i stedet for CA
- Alle kan verificere autenticitet, kun ejer kan generere valid signatures
-->

![Transaction](img/blockchain-transactions.png)

- Asymmetrisk kryptering

**Ligner Chain of Trust:**

- Public key identificerer afsender
- Private key beviser ejerskab
- Alle kan verificere, kun ejer kan signere

---

### Kæden af blokke

<!--
Talking points:
- **Genesis block:** Første block i chain, hardcoded i software
- Ingen previous block hash - starter kæden
- **Hash linking:** Hver block indeholder hash af previous block
- Ændring i block N → ændrer block N's hash → bryder link til block N+1
- Angriber skal re-mine alle efterfølgende blocks
- **Dybde = sikkerhed:** Blokke dybt i chain er praktisk umulige at ændre
- Skal outpace hele netværkets mining power for at lave længere chain
- Bitcoin: 6 confirmations (blocks) anses for irreversibelt sikkert
-->

<img src="img/blockchain-chain.png" height="60%" />

- Første block: Genesis block
- Ændring i én blok → ændrer hash → bryder kæden
- Blokke længere tilbage i kæden er mere sikre
  - Man skal outpace netværket

---

### Hash-funktioner i blockchain

<!--
Talking points:
- **SHA-256** er den kryptografiske hash funktion brugt i Bitcoin
- Hash af hele block (header + transactions) skal opfylde difficulty
- Hash af previous block linker blocks sammen i chain
- Merkle tree bruger SHA-256 til at hash transactions effektivt
- **Deterministisk:** Samme input giver altid samme output
- **One-way:** Kan ikke reverse hash til at finde input
- **Avalanche effect:** Ændring af 1 bit i input ændrer ~50% af output bits
- Gør det umuligt at forudsige hash, skal brute force nonce
-->

**SHA-256 bruges til:**

- Hash af hele blokken
- Hash af previous block
- Hash af transactions (Merkle tree)

**Egenskaber:**

- Deterministisk
- One-way (irreversibel)
- Små ændringer → helt forskellig output

---

### Mining og Proof-of-Work

<!--
Talking points:
- **Proof-of-Work:** Bevise at du har brugt computational ressourcer (arbejde)
- Målet: Find nonce så block hash starter med X antal leading zeros
- **Proces:** Prøv nonce = 0, hash block. Ikke nok zeros? Prøv nonce = 1, hash igen
- Fortsæt brute force indtil hash opfylder target
- **Eksempel:** Target = 0000XXXX betyder hash skal starte med 4 zeros
- I gennemsnit skal 2^16 hashes prøves for 4 zeros
- **Fee belønning:** Miner der finder valid nonce får transaction fees
- Plus block reward (coinbase transaction) - nye coins skabes
- **Verificering:** Andre noder verificerer nonce hurtigt (1 hash)
- Mining er hårdt, verification er let - asymmetrisk puzzle
-->

**Målet:** Find en nonce så:

```
Hash(block data + nonce) starter med X antal 0'er
```

**Eksempel:**

```
Target: 0000XXXX...
Nonce = 0: Hash = 8A3F2... ✗
Nonce = 1: Hash = 7B9E1... ✗
Nonce = 2: Hash = 9C4D3... ✗
...
Nonce = 47823: Hash = 0000AB2... ✓
```

- Findes fee
- Nonce verificeres af netværket

---

### Difficulty og target

<!--
Talking points:
- **Difficulty adjustment:** Bitcoin justerer difficulty hver 2016 blocks (~2 uger)
- Mål: Holde block time omkring 10 minutter uanset total mining power
- **Flere miners join:** Total hashrate stiger, blocks findes hurtigere
- Difficulty øges automatisk - kræver flere leading zeros
- **Target nummer:** Lower target = sværere (færre valide hashes)
- Target = 0000FFFF tillader mange hashes under den værdi
- Target = 00000000FF tillader meget få - eksponentielt sværere
- Hver ekstra zero fordobler sværhedsgraden cirka
-->

**Difficulty justeres så:**

- Block time ≈ konstant (Bitcoin: ~10 min)
- Flere miners → højere difficulty

**Target eksempel:**

```
Let:    0000FFFF...  (mange valide hashes)
Svær:   00000000FF... (få valide hashes)
```

---

### Problemer med Proof-of-Work

<!--
Talking points:
- **Energiforbrug:** Bitcoin network bruger ~150 TWh årligt
- Mere end hele lande som Argentina eller Norge
- Miljømæssigt insustainabelt, primært fossil fuel powered
- **Centralisering paradoks:** Skulle være decentralized, men mining pools koncentrerer magt
- Top 4 mining pools kontrollerer >50% af hashrate
- Kun rentabelt at mine hvor strøm er billigst (Island, Kina)
- Almindelige users kan ikke compete
- **51% attacks:** Hvis én entity får >50% af hashrate
- Kan double spend ved at mine længere chain
- Kan censurere transactions ved at ikke inkludere dem
- Dyrt at udføre men teoretisk muligt
-->

**Energiforbrug:**

- Bitcoin: ~150 TWh/år

**Centralisering:**

- Mining pools koncentrerer magt
- Kun rentabelt med billig strøm

**51% attacks:**

- Kontrol af >50% hash power = kan manipulere
- Åbner for double spending

---

### Praktisk erfaring fra øvelser

<!--
Talking points:
- Praktisk demonstration af exponential difficulty scaling
- **CPU hashrate:** Moderne CPU kan ca. 1-10 MH/s (mega hashes per second)
- GPU er meget hurtigere (GH/s), ASIC miners endnu hurtigere (TH/s)
- **Øvelsesresultater:** Difficulty 5 (5 leading zeros) tog 2.5 sekunder
- Difficulty 6 tog 70 sekunder - ~28x længere tid
- Difficulty 7 tog 161 sekunder - ~2.3x længere end difficulty 6
- **Observation:** Hver ekstra zero fordobler cirka antallet af hashes nødvendige
- Bitcoin difficulty er omkring 50+ zeros - umuligt på almindelig hardware
- Viser hvorfor specialized ASIC miners er nødvendige for rentabel mining
-->

**På egen computer:**

- Moderne CPU: ~1-10 MH/s

**Mine forsøg:**

```bash
…/exercises/blockchain ❯ ./main
Difficulty 5. Mining block 1...
Block mined: 0000064590b09f3a53711dadea123948fefc0a4358ddf0504b058fd5d20e75aa
Mining took 2495 milliseconds
Difficulty 6. Mining block 2...
Block mined: 000000254a0dc57e84a0c76d221f01081771a61f73c92f499cc6411e39d74511
Mining took 70527 milliseconds
Difficulty 7. Mining block 3...
Block mined: 000000053a07229e51ed28ac675adcf4355413701a9a07efe9473d1597908df8
Mining took 161300 milliseconds
```

---

### Alternative consensus mekanismer

<!--
Talking points:
- **Proof-of-Stake:** "Dem der ejer mest regerer" - mere coins = mere voting power
- Validators "staker" (låser) coins som collateral
- Randomly selected til at propose/validate blocks baseret på stake size
- **Fordele:** Ingen energy-intensive mining, 99.95% mindre energiforbrug
- Hurtigere transactions og finality
- **Ethereum 2.0:** Switchede fra PoW til PoS i September 2022 (The Merge)
- Reducerede energy consumption med 99.95%
- **Proof-of-Reputation:** Trusted nodes baseret på historisk opførsel
- Bruges i enterprise/private blockchains
-->

**Proof-of-Stake:**

- Dem der har flere penge regerer over netværket
- Validatorer "staker" coins
- Intet energy-intensive mining
- Ethereum skiftede i 2022

**Andre:**

- Proof-of-Reputation

---

### Blockchain egenskaber

<!--
Talking points:
- **Fordele - Decentralisering:** Ingen single point of failure eller control
- Trustless system - kan handle med ukendte parter
- **Transparens:** Alle transactions er public og verifiable
- Audit trail for compliance og accountability
- **Immutability:** Historik kan ikke ændres - beskytter mod fraud
- Data integrity garanteret af kryptografi
- **Ulemper - Skalerbarhed:** Bitcoin: 7 TPS, Ethereum: 15 TPS vs Visa: 24,000 TPS
- Blockchain er inherently slow pga. consensus overhead
- **Energiforbrug:** PoW er miljømæssigt problematisk
- **Irreversibilitet:** Fejl kan ikke rettes, mistede keys = mistede penge
- Ingen chargebacks eller recovery mechanism
-->

**Fordele:**

- Decentralisering
- Transparens
- Immutability
- Trustless system

**Ulemper:**

- Skalerbarhed (lav throughput)
- Energiforbrug (PoW)
- Irreversibilitet (fejl kan ikke rettes)

---
