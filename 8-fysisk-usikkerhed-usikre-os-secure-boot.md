---
marp: true
theme: default
---

# 8 FYSISK (U)SIKKERHED OG (U)SIKRE OS – SECURE BOOT

Forklar formålet og principperne i Secure Boot.
Hvordan bruges kryptografi i Secure Boot?
Redegør for nogle af udfordringerne i implementering af Secure Boot.
Eksemplificer for eksempel med hvordan Secure Boot bruges på iPhone.
Perspektiver til Chain of Trust mere generelt i IT sikkerhed og relevante øvelser.

---

### Secure Boot - Formål

- Sørge for at kun **trusted** software bliver kørt ved boot
- Opbygger en **Chain of Trust**
- Forhindrer tampering, rootkits og bootkits (malware der indlæses før OS)

---

### Secure Boot - Opbygning

- **Root of Trust**: Hardwaren starter processen (Immutable)
- UEFI Firmware tjekker signaturer mod en database af godkendte nøgler (**db**)
- UEFI tjekker bootloaderen (f.eks. GRUB eller Windows Boot Manager)
- Bootloaderen tjekker OS-kernel og drivere
- Hvert led verificerer det næste, før kontrollen overgives

---

### Secure Boot - Kryptografi

- Bruger **Digitale Signaturer** (Asymmetrisk kryptering)
- Udvikleren signerer koden med en **Private Key**
- En **Public Key** (certifikat) er lagret i hardwarens/firmwarens NVRAM
- Ved boot hashes koden, og signaturen verificeres
  - Signaturen ligger sammen med den eksekverbare fil
- Hvis hashværdien ikke matcher, afbrydes boot-processen

---

### Secure Boot - Udfordringer

- **Vendor Lock-in**: Kan gøre det svært at installere alternative OS (f.eks. Linux)
- **Key Management**: Hvem ejer nøglerne? (Microsoft vs. Hardware producent)
- **Revocation (DBX)**: Hvis en nøgle bliver lækket, skal den "sortlistes". Dette er svært at opdatere sikkert uden at "bricke" enheden.
- **Sårbarheder i firmware**: Hvis selve UEFI har en bug, falder hele korthuset.

---

### Secure Boot - iPhone Case (Chain of Trust)

- **Bootrom (Hardware Root of Trust)**: Første led, indkodet i chippen (Immutable).
- **LLB (Low Level Bootloader)**: Verificeres af Bootrom via signaturtjek.
- **iBoot**: Det næste lag, der verificeres af LLB.
- **Kernel**: Selve kernen af iOS, som verificeres af iBoot.
- **System Software & Apps**: Slutningen af kæden.
- **Princip**: Hvert led tjekker signaturen på det næste, før det får lov at køre.

---

### Angreb på Chain of Trust (Jailbreaking)

- **Sårbarhed**: Fejl i koden (f.eks. iBoot eller LLB) udnyttes til "Code Execution".
- **Privilege Escalation**: Hackeren opnår "Root"-adgang til systemet.
- **Patching**: Angriberen "patcher" boot-filerne for at fjerne signaturtjek.
- **Konsekvens**: Når tjekket er fjernet, kan uautoriseret kode (f.eks. Jailbreak) indlæses.
- **iBoot Leak (2018)**: Kildekoden til iBoot blev lækket, hvilket gjorde det lettere for hackere at finde sårbarheder i boot-kæden.

---

### Chain of Trust - Generelt

- **Hardware Root of Trust**: Fundamentet skal være fysisk sikkert.
- **Browser-eksempel**:
  - Root CA (i browseren) -> Intermediate CA -> Website Certifikat.
- **App-signering**: App Store tjekker signaturer før installation.
- **Øvelse**: Ændring af Trust Store

---
