---
marp: true
theme: default
paginate: true
---

# 9 (U)SIKKER KODE #2 – FUZZING

Forklar hvordan fuzzing fungerer – tegn gerne en figur.
Beskriv nogle fordele/ulemper ved fuzzing.
Giv eksempler på bugs der kan findes med fuzzing, og relater til relevante øvelser.
Hvad er anti-fuzzing?
Perspektiver til security scanners (automatiske sårbarhedsscannere).

---

### Hvad er fuzzing?

**Fuzzing** er en automatisk testmetode der sender ugyldige, uventede eller tilfældige data til et program for at finde bugs og sårbarheder.

- Finder edge cases og uventede fejl
- Finder fejl som compiler warnings og static analysis ikke finder
- Kan være black-box eller white-box (med instrumentation)
- Bruges i sikkerhedstestning og kvalitetssikring

---

### Hvordan fungerer fuzzing?

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

✗ **Tidskrævende** - kan tage timer/dage at finde bugs
✗ **Begrænset scope** - finder primært crash-bugs, ikke logiske fejl
✗ **Code coverage** - svært at nå dyb funktionalitet
✗ **Falske negativer** - finder ikke alle sårbarheder

---

### Bugs fundet med fuzzing

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

**Buffer overflow øvelser**

- Fuzzing ville hurtigt finde ukontrollerede strcpy/memcpy
- Kan identificere manglende input validering

**Web application security**

- Fuzzing af HTTP requests finder injection flaws
- SQL injection, XSS, command injection

**Binary exploitation**

- Fuzzing finder crash points der kan exploiteres

---

### Anti-fuzzing teknikker

Forsvarsteknikker som gør fuzzing mindre effektivt:

- **Checksum validering** - afviser inputs uden korrekt checksum
- **Magic bytes** - kræver specifikke værdier i input
- **Kompleks state** - kræver rækkefølge af gyldige inputs
- **Timing checks** - detekterer for hurtig input generation
- **Environmental checks** - opdager VM/sandbox miljøer

---

### Anti-fuzzing eksempel

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
