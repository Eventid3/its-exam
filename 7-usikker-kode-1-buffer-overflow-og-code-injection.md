# (U)SIKKER KODE #1/2 – BUFFER OVERFLOW OG CODE INJECTION

Forklar principperne i buffer overflow og code injection – tegn gerne en stack til at
underbygge forklaringen.
Beskriv to metoder, der bliver brugt til at forhindre code injection på stacken.
Oprids hvordan ROP kan omgå disse metoder.
Inddrag praktiske erfaringer fra øvelser.

---

### Stack

- LIFO datastruktur
- Holder variabler or returadresser for programmer
- Stack pointer (SP)
- Stack frames

**CPU registre**

- EAX, EBX, EDX: General purpose registre
- ESP: Stack pointer (SP)
- EBP: Base pointer (BP)
- PC/IP: Program counter/instruction pointer
  - Der hvor programmet er nået til i execution

---

### Function prologue & epilogue

**Function Prologue**

- Kald af functionen `foo()`
- Returadressen for execution gemmes
- Plads til `foo`'s stack frame allokeres
- Stack pointeren rykkes til toppen af `foo()`'s stack frame

**Function Epilogue**

- Efter endt execution rykkes SP og BP til `main()`'s stack
- Den gemte returaddresse rykkes ind i IP
  - Her er vulnerabilitien
- Returværdi rykkes ind i et register (typix A/EAX registeret)

---

### Buffer Overflow

---

### Code Injection

---

### Forsvar - BIOS

- Indstilling af NX-bit
- Forhindre at stacken er til code execution

---

### Forsvar - ASLR

## **Address Space Layout Rancomization**

- ...

---

### Return Oriented Programming

- return to libc attack...

---

### Øvelser fra kurset

- Bruger lang string til at finde buffer overflow
- Får placeret returaddresse på 'malicious code' i retur addressen fra...
