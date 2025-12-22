# 8 FYSISK (U)SIKKERHED OG (U)SIKRE OS – SECURE BOOT

Forklar formålet og principperne i Secure Boot.
Hvordan bruges kryptografi i Secure Boot?
Redegør for nogle af udfordringerne i implementering af Secure Boot.
Eksemplificer for eksempel med hvordan Secure Boot bruges på iPhone.
Perspektiver til Chain of Trust mere generelt i IT sikkerhed og relevante øvelser.

---

### Secure Boot

**Formål**

- Sørge for at kun **trusted** software blive kørt ved boot
- Opbygger en Chain of Trust
- Forhindrer tampering, rootkits og malware

---

### Secure Boot - Opbygning

- UEFI Firmware checker signaturerne af firmwaren selv
  - Første skridt mod Chain of Trust
  - Firmwaren indeholder en db af trusted keys
- UEFI checker signaturen af bootloaderen inden den loades
- Bootloaderen checker signaturen af OS-kernel og kritiske komponenter
- Fortsætter udaf i lag

---

### Secure Boot - Kryptografi

- Signaturen skabt med asymmetrisk kryptering
- Device Manifacturen laver signatur ud fra koden med private key
- Signatur og public key lægges på devicet/firmware
- Signaturen tjekkes under Secure Boot inden koden eksekveres

---

### Secure Boot - Udfordringer

---

### Secure Boot - iPhone

---

### Chain of Trust - Generelt

- Eksempel med certifikater i browser
