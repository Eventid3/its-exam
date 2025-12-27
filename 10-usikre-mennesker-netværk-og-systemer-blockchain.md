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

Forklar hvordan en blockchain virker. Tegn gerne et diagram.
Hvad er det for et (generelt) problem blockchain kan løse, og under hvilke
forudsætninger?
Beskriv proof-of-work, og hvilke problemer, der er forbundet dermed.
Inddrag øvelses erfaringer med blockchain og mining, fx hvor svær en mining du
rimeligt kan udføre på din egen computer

---

### Hvad er en blockchain?

- Distribueret database/register
- Data organiseret i "blokke" der er kædet sammen
- Ingen central autoritet
- Immutable (uforanderlig) historik

---

### Problemet: Distribueret konsensus

**Scenarie uden blockchain:**

- Hvordan bliver mange parter enige om sandheden?
- Hvem kan vi stole på?
- Hvad hvis nogen snyder?

**Blockchain løser:**

- Consensus uden central autoritet

---

### Forudsætninger for blockchain

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

![Block](img/blockchain-block.png)

- Nonce: det miners skal regne ud
- Merkle Tree: transactions hashet i en træ-struktur

---

### Block-struktur - Transaction

![Transaction](img/blockchain-transactions.png)

- Asymmetrisk kryptering

**Ligner Chain of Trust:**

- Public key identificerer afsender
- Private key beviser ejerskab
- Alle kan verificere, kun ejer kan signere

---

### Kæden af blokke

![Chain](img/blockchain-chain.png)

- Første block: Genesis block
- Ændring i én blok → ændrer hash → bryder kæden
- Blokke længere tilbage i kæden er mere sikre
  - Man skal outpace netværket

---

### Hash-funktioner i blockchain

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

**Energiforbrug:**

- Bitcoin: ~150 TWh/år

**Centralisering:**

- Mining pools koncentrerer magt
- Kun rentabelt med billig strøm

**51% attacks:**

- Kontrol af >50% hash power = kan manipulere
- Åbner for double spending

---

### Praktisk mining erfaring

**På egen computer:**

- Moderne CPU: ~1-10 MH/s
- Bitcoin difficulty: Urealistisk
- Test-netværk med lav difficulty: Muligt

**Mine forsøg:**

```
Difficulty = 4 leading zeros: ~5 sekunder
Difficulty = 6 leading zeros: ~5 minutter
Difficulty = 8 leading zeros: timer/dage
```

---

### Alternative consensus mekanismer

**Proof-of-Stake:**

- Dem der har flere penge regerer over netværket
- Validatorer "staker" coins
- Intet energy-intensive mining
- Ethereum skiftede i 2022

**Andre:**

- Proof-of-Reputation

---

### Blockchain egenskaber

**Fordele:**

- Decentralisering
- Transparens
- Immutability
- Trustless system

**Ulemper:**

- Skalerbarhed (lav throughput)
- Energiforbrug (PoW)
- Irreversibilitet (fejl kan ikke rettes)

