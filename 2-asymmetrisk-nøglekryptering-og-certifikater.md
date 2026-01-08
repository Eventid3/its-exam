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

# Asymmetrisk nøglekryptering og digitale certifikater

<!--
- Introduktion til emnet: asymmetrisk kryptering og certifikater
- Vigtigt for sikker kommunikation på internet
- Bygger på matematisk relation mellem to nøgler
-->

---

### Asymmetrisk kryptering

- To nøgler
  - Private & public
- Fordel: Vi behøver ikke at dele samme nøgle (usikkert)
- Ulempe: Langsommere end symmetrisk kryptering

<!--
- Grundprincippet: et nøglepar, public kan deles frit
- Løser key distribution problemet fra symmetrisk kryptering
- Performance trade-off: langsommere men mere sikkert
-->

---

### Asymmetrisk kryptering

```
Sender:
P = "Hej Poul!"
E(P, K_pub-r) -> C    // C = "$\ØrEjkd`c!2k'+"
Reciever:
D(C, K_priv-r) -> P   // P = "Hej Poul!"
```

- Matematisk kan nøglerne i principper byttes om.

<!--
- Eksempel på flow: krypterer med modtagers public key
- Kun modtager kan dekryptere med sin private key
- Nøglerne er matematisk forbundet men kan byttes om
-->

---

### Asymmetrisk kryptering

**Typer**

- RSA
- ECC

<!--
- RSA: ældre, baseret på primtal faktorisering
- ECC: nyere, hurtigere med kortere nøgler
- Begge bruges i praksis i dag
-->

---

### Asymmetrisk kryptering

**Anvendelse**

- Sikker kommunikation
- Fil encryption
- SSL Certifikater
- Signering

<!--
- Bruges overalt på internet: HTTPS, email, osv.
- SSL/TLS certificater baseret på asymmetrisk kryptering
- Digital signering sikrer autenticitet og integritet
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
Step-by-step for afsender:
1. Tag beskeden P = "Hej Poul"
2. Hash beskeden til H
3. Krypter H med afsenders private key K_priv-s → får signatur Sig
4. Send både P og Sig til modtager

Step-by-step for modtager:
1. Modtag P og Sig
2. Hash beskeden P → får H1
3. Dekrypter Sig med afsenders public key K_pub-s → får H2
4. Sammenlign H1 og H2 - er de ens, er signaturen gyldig
5. Beviser at afsender har private key og beskeden er uændret
-->

---

### Signering - Med confidentiality

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
Step-by-step for afsender:
1. Tag beskeden P = "Hej Poul"
2. Hash P og krypter hash med K_priv-s → får signatur Sig
3. Generer symmetrisk nøgle SYM
4. Krypter P med SYM → får ciphertext C
5. Krypter SYM med modtagers public key K_pub-r → får SYM_C
6. Send C, Sig og SYM_C til modtager

Step-by-step for modtager:
1. Modtag C, Sig og SYM_C
2. Dekrypter SYM_C med sin private key K_priv-r → får SYM
3. Dekrypter C med SYM → får plaintext P
4. Hash P → får H1
5. Dekrypter Sig med afsenders public key K_pub-s → får H2
6. Sammenlign H1 og H2 - er de ens, er alt OK
7. Kombinerer confidentiality (C), integrity (I) og authenticity (A)
-->

---

### Certifikater

- Problem: Hvordan kan jeg hvem en offentlig nøgle tilhører
- Løsning: CA (Certificate Authority)
- Binder nøgle til identitet

<!--
- Trust problem: hvordan ved vi en public key tilhører den rigtige person?
- CA'er er trusted third parties
- Certifikater binder identitet til public key
-->

---

### Certifikater - Udstedelse

1. Bob genererer key pair: K_priv-Bob, K_pub-Bob
2. Bob sender CSR (Certificate Signing Request) til CA:
   - Sin identitet
   - K_pub-Bob
3. CA verificerer Bob's identitet
4. CA opretter certifikat og signerer det:
   Cert_Bob = {identitet, K_pub-Bob, gyldighed, ...}
   Sig_CA = E(Hash(Cert_Bob), K_priv-CA)
5. CA sender signeret certifikat tilbage til Bob

<!--
- Processen starter med key pair generering
- CSR indeholder identitet og public key
- CA verificerer identiteten (vigtig step!)
- CA signerer certifikatet med sin private key
- Certifikatet kan nu bruges til at bevise identitet
-->

---

### Certifikater - Verifikation

1. Hent K_pub-CA (CA's offentlige nøgle)
   - Truststore
   - Root certificates
2. Verificer CA's signatur:
   H1 = Hash(Cert_Bob)
   H2 = D(Sig_CA, K_pub-CA)
   H1 == H2 ? ✓
3. Tjek gyldighed:
   - Er certifikatet udløbet?
   - Er det tilbagekaldt? (CRL/OCSP)
4. Tjek identitet matcher:
   - Navnet i certifikatet matcher domænet?
5. Nu kan Alice stole på K_pub-Bob!

<!--
- CA's public key skal være pre-installed (truststore)
- Verificer signaturen matematisk
- Tjek om certifikatet er gyldigt og ikke revoked
- Tjek om identiteten matcher hvem vi taler med
- Chain of trust: tillid til CA = tillid til certifikat
-->

---

### Certifikater - Typer af verificering

- Domain Validated (DV)
- Organization Validated (OV)
- Extended Validated (EV)

<!--
- DV: kun verificerer domæne ejerskab (hurtig og billig)
- OV: verificerer også organisation (mellemting)
- EV: omfattende verificering, grøn bar i browser (dyreste)
- Højere niveau = mere tillid men også mere omkostning
-->

---

### Certifikater - Certificate Chain

```
Root CA (selvunderskrevet)
    ↓ signerer
Intermediate CA
    ↓ signerer
End-entity Certifikat (Bob's certifikat)
```

<!--
- Root CA's private key holdes ekstremt sikkert (offline)
- Intermediate CA håndterer daglige operationer
- Chain valideres ved at verificere hver signatur op til root
- Giver bedre sikkerhed: hvis intermediate kompromitteres kan den revokes
-->

---
