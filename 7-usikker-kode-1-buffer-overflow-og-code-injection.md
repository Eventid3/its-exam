---
marp: true
theme: default
paginate: true
---

# (U)SIKKER KODE #1/2 – BUFFER OVERFLOW OG CODE INJECTION

<!--
Talking points:
- Buffer overflow er klassisk sårbarhed i C/C++ programmer uden bounds checking
- Code injection udnytter buffer overflow til at få CPU til at køre angribers kode
- Stack-baseret exploit - overskrivning af returadresser
- Forsvarsmekanismer: NX-bit (non-executable stack) og ASLR (address randomization)
- ROP (Return Oriented Programming) omgår disse forsvarsmekanismer
- Praktisk øvelse demonstrerede exploitation process med GDB og shellcode
-->

Forklar principperne i buffer overflow og code injection – tegn gerne en stack til at
underbygge forklaringen.
Beskriv to metoder, der bliver brugt til at forhindre code injection på stacken.
Oprids hvordan ROP kan omgå disse metoder.
Inddrag praktiske erfaringer fra øvelser.

---

### Stack

<!--
Talking points:
- LIFO (Last-In, First-Out): Sidste data på stacken er først ud - som en stak tallerkener
- Stack holder lokale variabler og function call information
- **ESP (Stack Pointer):** Register der peger på toppen af stacken, opdateres ved push/pop
- **EBP (Base Pointer):** Peger på bunden af current function's stack frame - referencepoint
- **EIP (Instruction Pointer):** Peger på næste CPU instruktion - kritisk for exploit
- Stack vokser "nedad" i hukommelse (fra høje til lave adresser)
-->

- **LIFO** datastruktur (Last-In, First-Out)
- Holder lokale variabler og returadresser
- **Stack Pointer (ESP)**: Pejer på toppen af stacken
- **Base Pointer (EBP)**: Referenepunkt for den nuværende stack frame
- **Instruction Pointer (EIP)**: Pejer på næste instruktion der skal eksekveres

---

### Function prologue & epilogue

<!--
Talking points:
- **Prologue:** Setup når function kaldes - gemmer caller's state
- Push caller's EBP på stack så vi kan genskabe det senere
- Sæt EBP = ESP (ny frame starter her)
- Allokerer plads til lokale variabler (sub esp, X)
- **Epilogue:** Cleanup når function returnerer
- mov esp, ebp: Frigiver lokale variabler
- pop ebp: Genskaber caller's base pointer
- **ret instruktion:** Popper returadresse fra stack ind i EIP - CPU hopper til denne adresse
- **Sårbarheden:** Hvis vi kan overskrive returadresse, kontrollerer vi hvor CPU hopper hen!
-->

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

<!--
Talking points:
- Opstår når mere data skrives til buffer end allokeret plads
- C/C++ funktioner som gets(), strcpy(), strcat() laver ikke bounds checking
- Data "flyder over" bufferen og overskriver tilstødende hukommelse på stacken
- **Trin 1:** Overskriver lokale variabler (kan ændre program logik)
- **Trin 2:** Overskriver saved EBP (mindre kritisk)
- **Trin 3:** Overskriver returadresse - KRITISK! Nu kontrollerer angriber execution flow
- Stack layout: [Buffer] [Lokale vars] [Saved EBP] [Return addr] [Function args]
-->

- Opstår når data skrives ud over en buffer's allokerede grænse
- Skyldes manglende "bounds checking" (f.eks. `gets()`, `strcpy()`)
- Overskriver nabo-data på stacken:
  1. Lokale variabler
  2. Gemt Base Pointer (EBP)
  3. **Returadressen (EIP)**

---

### Code Injection

<!--
Talking points:
- Udnytter buffer overflow til at injicere og eksekvere arbitrær kode
- **Shellcode:** Maskinekode der spawner en shell (/bin/sh) eller anden malicious funktionalitet
- Angriber placerer shellcode i bufferen (NOP sled + shellcode)
- Returnaddresse overskrives med adresse til shellcode på stacken
- Når function returnerer (ret), loader CPU returadresse (nu shellcode adresse) ind i EIP
- CPU hopper til shellcode i stedet for legitim return address
- **Resultat:** Angriber får shell med samme privilegier som vulnerable program
- Hvis program kører som root, får angriber root shell!
-->

- Angriber udnytter Buffer Overflow til at placere "shellcode" på stacken
- Returadressen overskrives med adressen på den injicerede shellcode
- Når funktionen slutter (`ret`), hopper CPU'en til shellcoden i stedet for den lovlige kode
- Resultat: Angriber får kontrol over programmet (f.eks. en shell)

---

### Forsvar - NX-bit / DEP

<!--
Talking points:
- NX (No-Execute bit) / DEP (Data Execution Prevention): Hardware-baseret beskyttelse
- CPU markerer memory pages som enten writable ELLER executable, ikke begge
- Stack og heap markeres som writable men non-executable
- Data sections kan skrives til, code sections kan eksekveres
- Når EIP peger på adresse i non-executable region, smider CPU exception
- Program crasher i stedet for at eksekvere shellcode
- **Effekt:** Gør traditionel code injection umulig - shellcode kan ikke køres fra stack
- Men beskytter ikke mod Return Oriented Programming
-->

**Data Execution Prevention (NX / No-Execute)**

- Markerer hukommelsesområder (som stack og heap) som ikke-eksekverbare
- CPU'en nægter at køre kode, hvis EIP peger på en adresse i disse områder
- Gør traditionel Code Injection umulig, da shellcoden på stacken ikke kan køres

---

### Forsvar - ASLR

<!--
Talking points:
- ASLR (Address Space Layout Randomization): Randomisering af memory layout
- Ved hver program start randomiseres base adresser for stack, heap, libraries (libc)
- Stack starter måske på 0xbfff0000 første gang, 0xb7890000 næste gang
- Gør det praktisk umuligt for angriber at gætte korrekte adresser
- **Uden ASLR:** Shellcode altid på 0xbfff1234, libc system() på 0xb7e12345
- **Med ASLR:** Adresser ændres hver gang, angriber må gætte eller "lække" adresse
- Kræver ofte info leak vulnerability for at bypasse
- Kan kombineres med NX for defense in depth
-->

**Address Space Layout Randomization**

- Randomiserer hukommelsesadresser for stack, heap og biblioteker ved hver kørsel
- Gør det svært for angriberen at forudsige, hvor deres shellcode eller systemfunktioner befinder sig
- Kræver at angriberen gætter eller "lækker" en adresse for at ramme rigtigt

---

### Return Oriented Programming (ROP)

<!--
Talking points:
- ROP: Avanceret exploit teknik designet til at omgå NX og ASLR
- I stedet for at injicere ny kode, genbruger eksisterende legitim kode
- **Return-to-libc:** Simpleste form - overskriver returadresse med adresse til system() i libc
- Lægger "/bin/sh" pointer på stacken som argument til system()
- **Gadgets:** Små code snippets (3-5 instruktioner) der ender med ret instruktion
- Eksempel gadget: "pop rdi; ret" eller "mov eax, ebx; pop ebx; ret"
- Ved at kæde mange gadget adresser på stacken kan man bygge helt program
- Hver ret hopper til næste gadget - turing complete exploitation
- Omgår NX fordi vi eksekverer eksisterende code sections (executable)
- Kan omgå ASLR hvis man har info leak til at finde gadget adresser
-->

- Designet til at omgå **NX/DEP** og **ASLR**
- Bruger eksisterende, legitim kode (f.eks. i `libc`) i stedet for injiceret shellcode
- **Return-to-libc**: Hopper direkte til f.eks. `system("/bin/sh")`
- **Gadgets**: Små bidder kode der ender på `ret`. Ved at kæde disse adresser på stacken, kan angriberen bygge et helt program ud af "stumper"

---

### Praktisk øvelse – Stack Overflow

<!--
Talking points:
- Målet var at overtage program execution flow via vulnerable strcpy
- **Sårbarhed:** strcpy(buffer, argv[1]) kopierer uden længde check
- **Fuzzing proces:** Send lange strenge for at finde crash point
- Find offset til returadresse ved at bruge unique pattern (De Bruijn sequence)
- GDB viser at EIP = 0x41424344 - matcher pattern på offset 76
- **Adresse leak:** Brug GDB til at finde buffer start adresse (x/200x $esp)
- **Payload konstruktion:** [76 bytes padding] + [buffer adresse] + [NOP sled] + [shellcode]
- NOP sled (0x90 bytes) giver "landing zone" så præcis adresse ikke kritisk
- **Resultat:** Program hopper til vores shellcode, spawner shell med program's privilegier
-->

- Mål: Overtagelse af eksekveringsflowet via sårbar strcpy().
- Sårbarhed: Manglende check af inputlængde fra `argv[1]` (buffer overflow).
- Proces:
  - Fuzzing: Identificering af "offset" til returadressen (crash ved unik streng).
  - Adresse-læk: Find startadressen for bufferen (typisk via GDB).
  - Payload: Konstruktion af streng: [Padding] + [Ny returaddresse] + [Shellcode].
- Resultat: Programmet returnerer til den injicerede shellcode frem for main.
