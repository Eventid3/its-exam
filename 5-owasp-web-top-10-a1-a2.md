# OWASP Web Top 10: A1 og A2

Redegør for hvad OWASP er og redegør for og vis eksempler på sårbarheder i
følgende kategorier i OWASP Web Top 10 2025, samt forklar hvordan man kan sikre
sig mod disse:
A1 Broken Access Control (inkl. Cross-Site Request Forgery).
A2 Security Misconfiguration
Samt forklar fordele og ulemper ved automatiske sårbarhedsscannere.

---

### Hvad er OWASP?

**Open Worldwide Application Security Project**

- Non-profit organisation for web security
- Udgiver Top 10 liste over kritiske sikkerhedsrisici
- Opdateres regelmæssigt (senest 2025)
- Industri standard

---

### A01 - Broken Access Control

**Hvad er det?**

- Brugere får adgang til resourcer de ikke burde have
- Manglende eller forkert authorization

**Eksempler:**

- IDOR: `/api/user/123/profile` → `/api/user/124/profile`
- Force browsing til admin sider
- CSRF

---

### A01 - IDOR Eksempel

**Sårbar kode:**

```
GET /api/user/123/profile
→ Returnerer profil for bruger 123
```

- Angriber ændrer URL til `/api/user/124/profile`
- Får adgang til anden brugers data
- Horizontal Privilege Escalation

**Problem:**

- Ingen server-side validering af om requester ejer resourcen

Løsning:

- Valideringen skal ske server-side inden sensitiv data sendes

---

### A01 - Vertical Privilege Escalation

**Angular**

```typescript
@if (authCtrl.isAdmin) {
  <button (click)="userCtrl.deleteUser()">
}
@if (authCtrl.isAdmin) {
  <section>sensitive information</section>
}
```

- **Kritisk**: requesten skal tjekkes server-side

---

### A01 - CSRF Eksempel

**Cross-Site Request Forgery**

```html
<!-- På evil.com -->
<img src="https://bank.com/transfer?to=hacker&amount=1000" />
```

- Udnytter at cookies sendes automatisk
- Udfører handlinger som den indloggede bruger

---

### A01 - Forsvar

**Server-side validation**

- Valider adgang ved hver request
- Tjek både authentication OG authorization
- Deny by default

**CSRF beskyttelse**

- CSRF Tokens
- SameSite Cookie Attribute (`Strict` eller `Lax`)

**Generelt**

- Log og monitorer mistænkelig aktivitet
- Input validation og sanitization

---

### A02 - Security Misconfiguration

**Hvad er det?**

- Forkert eller ufuldstændig sikkerhedskonfiguration
- Default settings ikke ændret
- Unødvendige features aktiveret

**Eksempler:**

- Default credentials (admin/admin)
- Debug mode i produktion
- Verbose error messages med system info
- Directory listing aktiveret

---

### A02 - Debug Info Eksempel

ASP.NET Trace Information:
`<http://www.domain.com/Trace.axd``>

- Eksponerer detaljeret debug information
- Stack traces synlige for alle
- Intern system information lækket

**Problem:** Debug mode aktiveret i produktion

---

### A02 - Eksempler fortsat

Missing security hardening

- Improperly configured permissions på cloud services
- Security settings i app servers, frameworks, libraries ikke sat korrekt
- Server sender ikke security headers

Backward compatibility prioriteret over sikkerhed

- Ældre, usikre konfigurationer bibeholdt
- Legacy features ikke disabled

---

### A02 - Forsvar

Hardening process

- Fjern alle unnecessary features
- Disable default accounts
- Ændr alle default passwords
- Minimal installation

**Security headers**

```
Strict-Transport-Security: max-age=31536000
Content-Security-Policy: default-src 'self'
X-Frame-Options: DENY
```

**Error handling**

- Generic error messages til brugere
- Detaljeret logging kun server-side
- Disable debug info i produktion

---

### Automatiske sårbarhedsscannere

**Hvad er det?**

- Tools der automatisk scanner for kendte sårbarheder
- Eksempler: OWASP ZAP, Burp Suite, Nessus

**Typer:**

- SAST: Static Application Security Testing
- DAST: Dynamic Application Security Testing
- SCA: Software Composition Analysis

---

### Scannere - Fordele

**Hastighed & konsistens**

- Scanner hele apps på minutter/timer
- Samme checks hver gang
- CI/CD integration

**Coverage & cost**

- Finder mange common sårbarheder
- Billigere end manuelle pentests
- Kan køre ofte

**Early detection**

- Find problemer før production
- Automatisk dokumentation

---

### Scannere - Ulemper

**False positives & negatives**

- Mange falske alarmer
- Misser komplekse sårbarheder
- Business logic flaws ikke detekteret

**Begrænsninger**

- Forstår ikke business logic
- Kræver proper configuration
- Kan crashe systemer ved aggressiv scanning

**Ikke en komplet løsning**

- Supplement til manuel testing
- Finder kun kendte sårbarheder
- Zero-days opdages ikke

---

### Best practice

**Kombineret approach:**

- Scannere til continuous baseline checks
- Manuel penetration testing
- Code reviews
- Security architecture reviews

**Defense in depth!**
