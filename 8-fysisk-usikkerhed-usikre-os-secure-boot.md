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

# 8 FYSISK (U)SIKKERHED OG (U)SIKRE OS – SECURE BOOT

---

### Secure Boot - Formål

<!--
Talking points:
- Hovedformål: Sikre at kun trusted, umodificeret software køres under boot
- Opbygger Chain of Trust fra hardware til operativsystem
- **Forhindrer tampering:** Malware kan ikke modificere boot process
- **Rootkits og bootkits:** Malware der loader før OS og skjuler sig fra detection
- Secure Boot detekterer og blokerer sådan malware før den kan starte
- Beskytter mod Evil Maid attacks (fysisk adgang til device)
-->

- Sørge for at kun **trusted** software bliver kørt ved boot
- Opbygger en **Chain of Trust**
- Forhindrer tampering, rootkits og bootkits (malware der indlæses før OS)

---

### Secure Boot - Opbygning

<!--
Talking points:
- **Root of Trust:** Hardware chip (TPM eller Secure Enclave) der er immutable
- Kan ikke modificeres selv med fysisk adgang - burned into silicon
- **UEFI Firmware:** First software to run, verificerer bootloader
- db (database) indeholder public keys af trusted bootloaders
- **Bootloader:** GRUB (Linux) eller Windows Boot Manager verificeres af UEFI
- Bootloader verificerer derefter kernel signaturer
- **Kernel og drivers:** OS kernel og signed drivers verificeres før load
- **Hvert led verificerer det næste:** Broken chain = boot failure
- Kontrollen overgives kun hvis signatur er valid
-->

- **Root of Trust**: Hardwaren starter processen (Immutable)
- UEFI Firmware tjekker signaturer mod en database af godkendte nøgler (**db**)
- UEFI tjekker bootloaderen (f.eks. GRUB eller Windows Boot Manager)
- Bootloaderen tjekker OS-kernel og drivere
- Hvert led verificerer det næste, før kontrollen overgives

---

### Secure Boot - Kryptografi

<!--
Talking points:
- Baseret på **asymmetrisk kryptering** (PKI - Public Key Infrastructure)
- Developer signerer kode med private key (holdes hemmeligt og sikkert)
- Public key certificate lagres i firmware's NVRAM (non-volatile memory)
- **Boot process:** Koden hashes (SHA-256), signatur verificeres med public key
- Signatur er kryptografisk bundet til koden - ændring = invalid signatur
- Signaturen ligger embedded i executable filen (PE header på Windows)
- Hvis hash ikke matcher signatur, boot process afbrydes med error
- Samme princip som HTTPS certificates og code signing generelt
-->

- Bruger **Digitale Signaturer** (Asymmetrisk kryptering)
- Udvikleren signerer koden med en **Private Key**
- En **Public Key** (certifikat) er lagret i hardwarens/firmwarens NVRAM
- Ved boot hashes koden, og signaturen verificeres
  - Signaturen ligger sammen med den eksekverbare fil
- Hvis hashværdien ikke matcher, afbrydes boot-processen

---

### Secure Boot - Eksempel

**Bootloader -> OS-Kernel**

1. Bootloader kigger på OS-Kernel signaturblock og finder certifikatet -> `K_pub-kernel, sig`
2. Verificerer signeren ved at kigge i _allowed database_
3. Bootloader udregner sit eget hash af OS-Kernel koden -> `hash1`
4. Bootloader dekrypterer signaturen -> `hash2 = D(sig, K_pub-kernel)`
5. Bootloader sammenligner de to hashes

---

### Secure Boot - Udfordringer

<!--
Talking points:
- **Vendor Lock-in:** Hardware har kun Microsoft's public keys by default
- Gør det svært at installere Linux distros der ikke er Microsoft-signeret
- Users må disable Secure Boot eller manually enroll Linux keys
- **Key Management:** Hvem kontrollerer signing keys? Microsoft vs HP/Dell?
- Centralisering af trust hos få vendors
- **Revocation (DBX - blocked database):** Hvis private key lækkes, skal public key blacklistes
- Firmware updates til DBX er risikable - forkert update kan "bricke" device
- Catch-22: Skal bruge secure boot til at update secure boot safely
- **Firmware bugs:** UEFI firmware er kompleks software med bugs
- Vulnerability i UEFI kan kompromittere hele Chain of Trust
-->

- **Vendor Lock-in**: Kan gøre det svært at installere alternative OS (f.eks. Linux)
- **Key Management**: Hvem ejer nøglerne? (Microsoft vs. Hardware producent)
- **Revocation (DBX)**: Hvis en nøgle bliver lækket, skal den "blacklistes". Dette er svært at opdatere sikkert uden at "bricke" enheden.
- **Sårbarheder i firmware**: Hvis selve UEFI har en bug, falder hele korthuset.

---

### Secure Boot - iPhone Case (Chain of Trust)

<!--
Talking points:
- iPhone's Secure Boot er en af de mest sikre implementeringer
- **Bootrom:** Burned into A-series chip silicon, kan ikke opdateres eller modificeres
- Immutable code - selv Apple kan ikke ændre det
- **LLB (Low Level Bootloader):** First updateable component, verificeres af Bootrom
- **iBoot:** Second stage bootloader, verificeres af LLB
- **Kernel:** iOS kernel, verificeres af iBoot før execution
- **System Software & Apps:** Verificeres via code signing
- **Princippet:** Trust kaskaderer nedad - hvert led tjekker signatur på næste led
- Hvis ét led fejler verification, stopper boot processen helt
-->

- **Bootrom (Hardware Root of Trust)**: Første led, indkodet i chippen (Immutable).
- **LLB (Low Level Bootloader)**: Verificeres af Bootrom via signaturtjek.
- **iBoot**: Det næste lag, der verificeres af LLB.
- **Kernel**: Selve kernen af iOS, som verificeres af iBoot.
- **System Software & Apps**: Slutningen af kæden.
- **Princip**: Hvert led tjekker signaturen på det næste, før det får lov at køre.

---

### Chain of Trust - Generelt

<!--
Talking points:
- Chain of Trust er fundamentalt princip i IT sikkerhed - ikke kun boot
- **Hardware Root of Trust:** Alle chains starter i trusted hardware (TPM, Secure Enclave)
- Fysisk sikkerhed er critical - hvis hardware kompromitteres, falder alt
- **Browser TLS/SSL eksempel:** Root CA i browser trust store → Intermediate CA → Website cert
- Hver CA verificerer næste led med digital signatur
- **App signing:** Apple App Store tjekker developer signatur før installation
- Code Signing certificates på macOS/Windows
- **Øvelse erfaringer:** Trust Store manipulation - tilføj custom Root CA
- Viste hvordan man kan MITM HTTPS ved at kontrollere trust anchors
- Demonstrerede vigtigheden af at beskytte Root of Trust
-->

- **Hardware Root of Trust**: Fundamentet skal være fysisk sikkert.
- **Browser-eksempel**:
  - Root CA (i browseren) -> Intermediate CA -> Website Certifikat.
- **App-signering**: App Store tjekker signaturer før installation.
- **Øvelse**: Ændring af Trust Store

---
