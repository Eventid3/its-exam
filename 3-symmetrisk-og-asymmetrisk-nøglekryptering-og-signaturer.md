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

# Symmetrisk og asymmetrisk nøglekryptering og digitale signaturer

---

### Symmetrisk Kryptering

- En nøgle til encryption og decryption
- Ulempe: kræver deling (usikkert)
- Fordel: performance
- AES ✔
- DES ❌

**Sender**

```
P = "Hej Poul"
C = E(P, K)    // C = "$\ØrEjkd`c!2k'+"
```

**Modtager**

```
D(C, K)        // "Hej Poul"
```

<!--
- Princip: Én nøgle til kryptering og dekryptering.
- Ulempe: Nøgledistribution ('key distribution problem').
- Fordel: Høj performance, velegnet til store datamængder.
- Algoritmer: AES (moderne standard), DES (usikker, forældet).
- Eksempel: Afsender krypterer med K, modtager dekrypterer med samme K.
-->

---

### Symmetrisk Kryptering

- Stream ciphers
  - Responsivt
  - Er modtagelig overfor insertions
  - Lav diffusion
- Block ciphers
  - Hver block er afhængig af den forrige
  - Initialization vector
  - Langsommere, men bedre diffusion

<!--
- **Stream ciphers**:
  - Krypterer løbende (bit/byte).
  - Fordel: Responsivt (real-tid).
  - Ulempe: Sårbar overfor 'insertions', lav diffusion.
- **Block ciphers**:
  - Krypterer i faste blokke.
  - Bruger 'chaining' (fx CBC) -> hver blok afhænger af forrige.
  - Kræver 'Initialization Vector' (IV) for at undgå mønstre.
  - Fordel: Høj diffusion. Ulempe: Langsommere.
-->

---

### Asymmetrisk kryptering

- To nøgler
  - Private & public
- Fordel: Vi behøver ikke at dele samme nøgle (usikkert)
- Ulempe: Langsommere end symmetrisk kryptering
- Typer
  - RSA
  - ECC

<!--
- Princip: Nøglepar (privat og offentlig). Privat holdes hemmelig, offentlig deles.
- Fordel: Løser nøgledistributionsproblemet.
- Ulempe: Beregningstungt, meget langsommere end symmetrisk.
- RSA: ældre, baseret på primtal faktorisering
- ECC: nyere, hurtigere med kortere nøgler
- Begge bruges i praksis i dag
-->

---

### Asymmetrisk kryptering

**Sender**

```
P = "Hej Poul!"
E(P, K_pub-r) -> C    // C = "$\ØrEjkd`c!2k'+"
```

**Reciever**

```
D(C, K_priv-r) -> P   // P = "Hej Poul!"
```

- Matematisk kan nøglerne i principper byttes om.

<!--
- **Fortroligheds-eksempel**:
  - Afsender krypterer med modtagers **offentlige** nøgle.
  - Modtager dekrypterer med sin **private** nøgle.
- **Bemærkning**: Kryptering med privat nøgle -> dekryptering med offentlig.
  - Giver ikke fortrolighed.
  - Muliggør digitale signaturer (autenticitet).
-->

---

### Asymmetrisk kryptering

**Anvendelse**

- Sikker kommunikation
- Fil encryption
- SSL Certifikater
- Signering

<!--
- **Sikker kommunikation**: Hybrid kryptering (fx TLS/SSL). Asymmetrisk til sikker udveksling af symmetrisk nøgle.
- **Filkryptering**: Sikker opbevaring af filer.
- **SSL/TLS Certifikater**: Binder identitet (fx domæne) til en offentlig nøgle. Grundlag for web-tillid.
- **Signering**: Beviser afsender (autenticitet) og dataintegritet.
-->

---

### Signering

```
P = "Hej Poul"
    ↧
H = Hash(P)        // "&#aF%3..."
    ↧
Sig = E(H, K_priv-s)
```

Sender P og Sig

**Validering**

```
H1 = Hash(P)         // "&#aF%3..."
H2 = D(Sig, K_pub-s) // "&#aF%3..."
H1 == H2 ? ✓
```

<!--
- **Formål**: Sikre autenticitet og integritet.
- **Afsender-proces**:
  1. Hash besked `P` -> `H` (unikt fingeraftryk).
  2. Krypter hash `H` med afsenders **private** nøgle -> `Sig`.
  3. Send `P` + `Sig`.
- **Modtager-validering**:
  1. Genberegn hash af `P` -> `H1`.
  2. Dekrypter `Sig` med afsenders **offentlige** nøgle -> `H2`.
  3. Sammenlign: `H1 == H2`? Hvis ja: gyldig signatur.
-->

---

### Signering

**Afsender**

```
P = "Hej Poul"
    ↧
Sig = E(Hash(P), K_priv-s)
C = E(P, SYM)
SYM_C = E(SYM, K_pub-r)
```

Sender C, Sig og SYM_C til modtager

**Modtager**

```
SYM = D(SYM_C, K_priv-r)
P = D(C, SYM)
H1 = Hash(P)
H2 = D(Sig, K_pub-s)
H1 == H2 ? ✓
```

<!--
- **Hybrid model (fortrolighed + autenticitet + integritet)**. Bruges i fx PGP/S/MIME.
- **Afsender**:
  1. Signer besked (hash + krypter med egen privat nøgle) -> `Sig`.
  2. Generer tilfældig symmetrisk nøgle `SYM`.
  3. Krypter besked `P` med `SYM` -> `C`.
  4. Krypter `SYM` med modtagers **offentlige** nøgle -> `SYM_C`.
  5. Send: `C`, `SYM_C`, `Sig`.
- **Modtager**:
  1. Dekrypter `SYM` med egen **private** nøgle.
  2. Dekrypter `C` med `SYM`.
  3. Valider `Sig` som før.
- **Perspektivering**: [NÆVN EKSEMPEL: Git commits, GPG, certifikater].
-->

---
