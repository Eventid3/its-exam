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

Redegør for og vis eksempler på asymmetrisk nøglekryptering, samt anvendelsen af
asymmetrisk nøglekryptering på Internet.
Samt forklar hvordan digitale certifikater udstedes og verificeres.
Perspektiver til relevante øvelser fra undervisningen.

---

### Asymmetrisk kryptering

- To nøgler
  - Private & public
- Fordel: Vi behøver ikke at dele samme nøgle (usikkert)
- Ulempe: Langsommere end symmetrisk kryptering

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

---

### Asymmetrisk kryptering

**Typer**

- RSA
- ECC

---

### Asymmetrisk kryptering

**Anvendelse**

- Sikker kommunikation
- Fil encryption
- SSL Certifikater
- Signering

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
```

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

**Modtager**

```
SYM = D(SYM_C, K_priv-r)
P = D(C, SYM)
```

Signering tjekkes som før

---

### Certifikater

- Problem: Hvordan kan jeg hvem en offentlig nøgle tilhører
- Løsning: CA (Certificate Authority)
- Binder nøgle til identitet

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

---

### Certifikater - Typer af verificering

- Domain Validated (DV)
- Organization Validated (OV)
- Extended Validated (EV)

---

### Certifikater - Certificate Chain

```
Root CA (selvunderskrevet)
    ↓ signerer
Intermediate CA
    ↓ signerer
End-entity Certifikat (Bob's certifikat)
```

---
