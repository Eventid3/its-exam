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

# 9 (U)SIKKER KODE #2 – FUZZING

---

### Hvad er fuzzing?

<!--
Talking points:
- Fuzzing: Automated software testing teknik der sender malformed data til program
- Idé: Bombarder program med unexpected inputs og se hvad der crasher
- Edge cases: Grænseværdier og hjørnetilfælde som normale tests misser
- Compiler warnings og static analysis finder kun kendte patterns
- Fuzzing finder runtime bugs der kun opstår med specifikke inputs
- **Black-box:** Ingen kildekode adgang, kun observér output
- **White-box (instrumentation):** Modificer binary til at tracke code coverage
- Bruges både til sikkerhedstestning (find exploits) og kvalitetssikring (stability)
-->

**Fuzzing** er en automatisk testmetode der sender ugyldige, uventede eller tilfældige data til et program for at finde bugs og sårbarheder.

- Finder edge cases og uventede fejl
- Finder fejl som compiler warnings og static analysis ikke finder
- Kan være black-box eller grey/white-box (med instrumentation)
- Bruges i sikkerhedstestning og kvalitetssikring

---

### Hvordan fungerer fuzzing?

<!--
Talking points:
- **Fuzzer (Generator):** Genererer testcases - enten tilfældige eller mutation-based
- Mutation-based: Tag valid input og flip bits, ændre bytes, indsæt special chars
- Generation-based: Kender input format (XML, PDF) og genererer semi-valid filer
- **Target Application:** Det program der testes, køres med fuzzer input
- Typisk i isolated sandbox for sikkerhed
- **Monitor (Analyse):** Observerer programmets opførsel under execution
- Checker for: Segmentation faults, hangs/timeouts, assertions, memory leaks
- Feedback loop: Hvis crash, gem input til reproduction; ellers generer nyt input
- Coverage-guided fuzzing (AFL): Prioriterer inputs der trigger ny code paths
-->

```
    ┌─────────────┐
  ┌>│   Fuzzer    │
  │ │  (Generator)│
  │ └──────┬──────┘
  │        │ Ugyldige/tilfældige inputs
  │        ▼
  │ ┌─────────────┐
  │ │   Target    │
  │ │  Application│
  │ └──────┬──────┘
  │        │ Output/crash
  │        ▼
  │ ┌─────────────┐
  │ │  Monitor    │
  └─│  (Analyse)  │
    └─────────────┘
```

---

### Fuzzing proces

<!--
Talking points:
- **Step 1 - Generer:** Skab testinputs via mutation eller generation
- Tilfældige bytes, extremværdier (MAX_INT, -1, 0), format strings, long strings
- **Step 2 - Eksekvér:** Kør target program med input, typisk tusindvis af gange
- **Step 3 - Monitor:** Brug tools som Valgrind, AddressSanitizer til at detektere issues
- Crash = potentiel sårbarhed, hang = infinite loop/DoS, memory leak = resource exhaustion
- **Step 4 - Analyse:** Reproducér crash, minimér input til smallest crashing case
- Triage: Er det exploitable eller bare harmless crash?
- **Step 5 - Iterate:** Lær fra coverage feedback, generer smartere inputs
-->

1. **Generer testinputs** - tilfældige eller muterede data
2. **Send til target** - kør programmet med input
3. **Monitor opførsel** - observér crashes, hangs, memory leaks
4. **Analyse** - identificer og reproducér bugs
5. **Gentag** - iterér med nye inputs

---

### Fordele ved fuzzing

✓ **Automatisering** - kræver minimal menneskelig intervention
✓ **Skalerbarhed** - kan køre kontinuerligt 24/7
✓ **Finder uventede bugs** - edge cases mennesker overser
✓ **Ingen false positives** - hvis den crasher, er der et problem

---

### Ulemper ved fuzzing

<!--
Talking points:
- **Tidskrævende:** Kan tage timer, dage eller uger at finde subtile bugs
- Simpel fuzzer kan køre millioner af tests uden at finde noget
- **Begrænset scope:** Primært finder crash bugs (segfaults, assertions)
- Misser logiske fejl som "user kan købe varer for $-10"
- Business logic flaws kræver forståelse af applikationen
- **Code coverage problem:** Svært at nå deeply nested funktionalitet
- Hvis input validering blokerer alt invalid input tidligt, når fuzzer aldrig dyb kode
- Kræver intelligent fuzzer eller manual seeding af good inputs
- **False negatives:** Finder ikke alle sårbarheder - mange bugs overleves
-->

✗ **Tidskrævende** - kan tage timer/dage at finde bugs
✗ **Begrænset scope** - finder primært crash-bugs, ikke logiske fejl
✗ **Code coverage** - svært at nå dyb funktionalitet
✗ **Falske negativer** - finder ikke alle sårbarheder

---

### Bugs fundet med fuzzing

<!--
Talking points:
- **Buffer overflows:** strcpy uden length check - fuzzer sender 10000 'A's
- Trigger segmentation fault når stack overskrives
- **Integer overflows:** user_input * 4 kan wrappe rundt til negativ værdi
- malloc(-4) fejler eller allokerer tiny buffer
- Exploitable for heap corruption
-->

**Buffer overflows**

```c
char buf[10];
strcpy(buf, fuzzer_input); // Kan overskrive memory
```

**Integer overflows**

```c
int size = user_input * 4; // Kan wrappe til negativ
malloc(size);
```

---

### Bugs fundet med fuzzing (fortsat)

<!--
Talking points:
- **Format string bugs:** printf(user_input) kan læse stack memory med %x
- Kan også skrive til arbitrary addresses med %n
- **Use-after-free:** Memory frigivet men pointer stadig brugt - dangling pointer
- Valgrind kan også finde dette med memory tracking
- **NULL pointer dereference:** Dereferencing NULL pointer crasher program
- **Assertion failures:** assert(x > 0) fejler hvis fuzzer finder x = -1
- Indikerer brudte invarianter i programlogik
-->

**Format string bugs**

```c
printf(user_input); // Kan læse/skrive memory
```

- **Use-after-free** - brug af frigivet memory
  - Kan også findes med Valgrind
- **NULL pointer dereference** - crash ved NULL access
- **Assertion failures** - brudte invarianter

---

### Relation til øvelser

<!--
Talking points:
- **Buffer overflow øvelser:** Manuelle exploits kunne automatiseres med fuzzing
- Fuzzing ville hurtigt finde strcpy/memcpy bugs ved at sende lange strenge
- Identificerer manglende input validering og bounds checking
-->

**Buffer overflow øvelser**

- Fuzzing ville hurtigt finde ukontrollerede strcpy/memcpy

Vi lavede manuel fuzzing:

1. `>./main AAAAAAAABBBBCCCC`
2. `>./main AAAAAAAABBBBCCCCDDDD`

- Kan identificere manglende input validering
- Kan dermed også bruges at adversaries/attackers

---

### Anti-fuzzing teknikker

<!--
Talking points:
- Anti-fuzzing: Defensive teknikker der gør fuzzing mindre effektiv
- **Checksum validation:** Input skal have korrekt CRC32/MD5 checksum
- Fuzzer skal kende algoritmen for at generere valid checksums
- **Magic bytes:** File format signatures (PNG: 89 50 4E 47, PDF: 25 50 44 46)
- Afvis input der ikke starter med korrekte bytes
- **Kompleks state requirements:** Kræver sekvens af gyldige inputs (login → navigate → action)
- Fuzzer når aldrig dyb funktionalitet uden at følge workflow
- **Timing checks:** Måler tid mellem inputs, detekterer automated fuzzing
- Timing delays
-->

Forsvarsteknikker som gør fuzzing mindre effektivt:

- **Checksum validering** - afviser inputs uden korrekt checksum
- **Magic bytes** - kræver specifikke værdier i input
- **Kompleks state** - kræver rækkefølge af gyldige inputs
- **Timing checks** - detekterer for hurtig input generation
- **Timing delays**
- **Polymorphic code** - lav kode der ser forskelligt ud, men udfører det samme
  - Gør crashes sværer at kategorisere og forhindre reverse engineering

---

### Anti-fuzzing eksempel

<!--
Talking points:
- **Checksum eksempel:** calculate_checksum() skal matche embedded checksum
- Hvis ikke match, return ERROR uden at processe data
- Fuzzer når aldrig den sårbare kode fordi checksum fejler
- Løsning: Fuzzer skal lære checksum algoritme og generere valid checksums
- **Magic byte check:** File skal starte med 0xDEAD hex signature
- Binary format validation - afviser non-conforming inputs tidligt
- Fuzzer skal seeds med valid file headers for at komme forbi
- Kombinationen af anti-fuzzing gør det meget svært at finde bugs
-->

```c
// Checksum anti-fuzzing
if (calculate_checksum(input) != input->checksum) {
    return ERROR; // Afvis input uden videre processing
}

// Magic byte check
if (input[0] != 0xDE || input[1] != 0xAD) {
    return ERROR;
}
```

Fuzzer skal kende disse requirements for at komme videre.

---

### Perspektivering: Security scanners

<!--
Talking points:
- **AVS (Automatiske Vulnerability Scanners):** OWASP ZAP, Burp Suite, Nessus
- **Ligheder:** Begge er automated testing tools der finder sikkerhedsproblemer
- Ingen manuel intervention nødvendig efter setup
- **Forskelle - Protocol awareness:** AVS forstår HTTP, TLS, SQL protokoller
- Fuzzer er mere "dumb" - sender random data uden at forstå kontekst
- **Knowledge base:** AVS bruger database af kendte CVEs og attack patterns
- Checker for specific vulnerabilities (SQL injection patterns, XSS payloads)
- **False positives:** AVS har højere rate af false positives
- Rapporterer potential issues baseret på patterns
- **Filosofi:** AVS tester for hvad vi ved eksisterer (known vulnerabilities)
- Fuzzer tester for ukendt - kan finde 0-days og nye bug classes
-->

**Automatiske sårbarhedsscannere** - AVS:

**Ligheder med fuzzing:**

- Automatiseret testing

**Forskelle:**

- AVS kender til protokoller, typen af angreb, mm.
  - Fuzzer er mere random
- AVS Bruger database med kendte sårbarheder
- AVS har højere risiko for falsk positiver
- Generelt: AVS tester for det vi ved findes, fuzzers tester for det vi ikke ved

---
