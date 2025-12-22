---
marp: true
theme: default
paginate: true
---

# (U)SIKKER KODE #1/2 – BUFFER OVERFLOW OG CODE INJECTION

Forklar principperne i buffer overflow og code injection – tegn gerne en stack til at
underbygge forklaringen.
Beskriv to metoder, der bliver brugt til at forhindre code injection på stacken.
Oprids hvordan ROP kan omgå disse metoder.
Inddrag praktiske erfaringer fra øvelser.

---

### Stack

- **LIFO** datastruktur (Last-In, First-Out)
- Holder lokale variabler og returadresser
- **Stack Pointer (ESP)**: Pejer på toppen af stacken
- **Base Pointer (EBP)**: Referenepunkt for den nuværende stack frame
- **Instruction Pointer (EIP)**: Pejer på næste instruktion der skal eksekveres

---

### Function prologue & epilogue

**Function Prologue**

- Gemmer `caller`'s EBP på stacken
- Sætter EBP til nuværende ESP (opretter ny frame)
- Allokerer plads til lokale variabler

**Function Epilogue**

- Frigiver lokal plads (`mov esp, ebp`)
- Genskaber `caller`'s EBP (`pop ebp`)
- `ret` instruktion: Popper gemt returadresse ind i **EIP**
  - **Her ligger sårbarheden!**

---

### Buffer Overflow

- Opstår når data skrives ud over en buffer's allokerede grænse
- Skyldes manglende "bounds checking" (f.eks. `gets()`, `strcpy()`)
- Overskriver nabo-data på stacken:
  1. Lokale variabler
  2. Gemt Base Pointer (EBP)
  3. **Returadressen (EIP)**

---

### Code Injection

- Angriber udnytter Buffer Overflow til at placere "shellcode" på stacken
- Returadressen overskrives med adressen på den injicerede shellcode
- Når funktionen slutter (`ret`), hopper CPU'en til shellcoden i stedet for den lovlige kode
- Resultat: Angriber får kontrol over programmet (f.eks. en shell)

---

### Forsvar - NX-bit / DEP

**Data Execution Prevention (NX / No-Execute)**

- Markerer hukommelsesområder (som stack og heap) som ikke-eksekverbare
- CPU'en nægter at køre kode, hvis EIP peger på en adresse i disse områder
- Gør traditionel Code Injection umulig, da shellcoden på stacken ikke kan køres

---

### Forsvar - ASLR

**Address Space Layout Randomization**

- Randomiserer hukommelsesadresser for stack, heap og biblioteker ved hver kørsel
- Gør det svært for angriberen at forudsige, hvor deres shellcode eller systemfunktioner befinder sig
- Kræver at angriberen gætter eller "lækker" en adresse for at ramme rigtigt

---

### Return Oriented Programming (ROP)

- Designet til at omgå **NX/DEP** og **ASLR**
- Bruger eksisterende, legitim kode (f.eks. i `libc`) i stedet for injiceret shellcode
- **Return-to-libc**: Hopper direkte til f.eks. `system("/bin/sh")`
- **Gadgets**: Små bidder kode der ender på `ret`. Ved at kæde disse adresser på stacken, kan angriberen bygge et helt program ud af "stumper"

---

### Praktisk øvelse – Stack Overflow

- Mål: Overtagelse af eksekveringsflowet via sårbar strcpy().
- Sårbarhed: Manglende check af inputlængde fra `argv[1]` (buffer overflow).
- Proces:
  - Fuzzing: Identificering af "offset" til returadressen (crash ved unik streng).
  - Adresse-læk: Find startadressen for bufferen (typisk via GDB).
  - Payload: Konstruktion af streng: [Padding] + [Ny returaddresse] + [Shellcode].
- Resultat: Programmet returnerer til den injicerede shellcode frem for main.
