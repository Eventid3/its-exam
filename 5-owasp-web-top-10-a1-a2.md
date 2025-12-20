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
- Path Traversal: `file=../../../etc/passwd`
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

**Problem:**

- Ingen validering af om requester ejer resourcen
- Kun URL obfuscation som sikkerhed

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

### A02 - Cloud & CORS eksempler

**Fejlkonfigureret CORS:**

```javascript
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```

- Tillader alle domæner at læse brugerdata

**Cloud misconfigurations:**

- S3 Buckets med public access
- Eksponerede admin panels og dashboards

---

### A02 - Forsvar

**Hardening**

- Fjern alt unødvendigt
- Disable default accounts
- Ændr alle default passwords

**Security headers**

```
Strict-Transport-Security: max-age=31536000
Content-Security-Policy: default-src 'self'
X-Frame-Options: DENY
```

**Best practices**

- Regular updates og patching
- Separate environments (dev/staging/prod)
- Generic error messages til brugere

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
