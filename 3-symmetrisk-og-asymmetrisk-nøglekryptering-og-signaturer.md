# Symmetrisk og asymmetrisk nøglekryptering og digitale signaturer

Redegør for og vis eksempler på symmetrisk og asymmetrisk nøglekryptering, samt
anvendelsen af disse i digitale signaturer.
Samt forklar hvordan digitale signaturer anvendes og stoles på i praksis.
Perspektiver til øvelser fra undervisningen hvor signering anvendes.

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

---

### Asymmetrisk kryptering

- To nøgler
  - Private & public
- Fordel: Vi behøver ikke at dele samme nøgle (usikkert)
- Ulempe: Langsommere end symmetrisk kryptering
- Typer
  - RSA
  - ECC

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
