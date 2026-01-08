# OWASP Web Top 10: A1 og A2

<!--
Talking points:
- Emnet dækker OWASP organisationen og de to øverste sikkerhedsrisici
- A1 Broken Access Control: manglende adgangskontrol, inkl. IDOR og CSRF
- A2 Security Misconfiguration: forkerte konfigurationer og default settings
- Automatiske sårbarhedsscannere: værktøjer til at finde sårbarheder automatisk
-->

Redegør for hvad OWASP er og redegør for og vis eksempler på sårbarheder i
følgende kategorier i OWASP Web Top 10 2025, samt forklar hvordan man kan sikre
sig mod disse:
A1 Broken Access Control (inkl. Cross-Site Request Forgery).
A2 Security Misconfiguration
Samt forklar fordele og ulemper ved automatiske sårbarhedsscannere.

---

### Hvad er OWASP?

<!--
Talking points:
- OWASP står for Open Worldwide Application Security Project
- Non-profit organisation fokuseret på web application security
- Top 10 listen rangerer de mest kritiske sikkerhedsrisici baseret på data fra industrien
- Opdateres regelmæssigt (2017, 2021, 2025) for at reflektere nye trusler
- Industri standard som alle udviklere og security professionals kender
-->

**Open Worldwide Application Security Project**

- Non-profit organisation for web security
- Udgiver Top 10 liste over kritiske sikkerhedsrisici
- Opdateres regelmæssigt (senest 2025)
- Industri standard

---

### A01 - Broken Access Control

<!--
Talking points:
- Broken Access Control er #1 på OWASP Top 10 - den mest kritiske sårbarhed
- Opstår når authorization (hvad må man tilgå) fejler eller mangler
- Brugere kan tilgå data eller funktioner de ikke skal have adgang til
- IDOR (Insecure Direct Object Reference): direkte adgang til objekter via ID
- Force browsing: gætte URLs til admin-funktionalitet
- CSRF (Cross-Site Request Forgery): udføre handlinger på vegne af bruger
-->

**Hvad er det?**

- Brugere får adgang til resourcer de ikke burde have
- Manglende eller forkert authorization

**Eksempler:**

- IDOR: `/api/user/123/profile` → `/api/user/124/profile`
- Force browsing til admin sider
- CSRF

---

### A01 - IDOR Eksempel

<!--
Talking points:
- IDOR (Insecure Direct Object Reference): direkte reference til interne objekter
- Horizontal Privilege Escalation: få adgang til andre brugeres data på samme niveau
- Problemet er manglende server-side validering - klienten kan ikke stoles på
- Angriber ændrer simpelthen ID i URL'en og får adgang til andres data
- Løsning: Altid verificer server-side at den indloggede bruger ejer/må tilgå ressourcen
-->

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

<!--
Talking points:
- Vertical Privilege Escalation: få højere privilegier end man skal have (f.eks. blive admin)
- Eksemplet viser client-side access control i Angular - kun skjuler UI elementer
- Problemet: Client-side kode kan manipuleres med browser dev tools eller HTTP requests
- En almindelig bruger kan stadig kalde deleteUser() endpoint direkte
- **Kritisk**: Authorization skal ALTID valideres server-side, aldrig kun client-side
-->

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

<!--
Talking points:
- CSRF (Cross-Site Request Forgery): angreb hvor ondsindet site udfører handlinger på vegne af bruger
- Udnytter at browseren automatisk sender cookies til domænet (bank.com)
- Når bruger besøger evil.com mens indlogget på bank.com, sendes request med valid session cookie
- Kan udføre state-changing handlinger: pengeoverførsler, password ændring, etc.
- Fungerer fordi serveren ikke kan skelne mellem legitime requests og CSRF requests
-->

**Cross-Site Request Forgery**

```html
<!-- På evil.com -->
<img src="https://bank.com/transfer?to=hacker&amount=1000" />
```

- Udnytter at cookies sendes automatisk
- Udfører handlinger som den indloggede bruger

---

### A01 - Forsvar

<!--
Talking points:
- **Server-side validation:** Tillid aldrig klienten - valider adgang ved hver request
- Authentication (hvem er du) OG authorization (hvad må du) skal begge valideres
- Deny by default: Luk alt ned, åbn kun hvad der eksplicit er tilladt
- **CSRF beskyttelse:** CSRF Tokens - unikt, uforudsigeligt token per session valideres
- SameSite Cookie Attribute: Strict (kun same-site) eller Lax (nogle cross-site GET)
- **Logging:** Monitorer for mistænkelig aktivitet som mange failed authorization attempts
- Input validation for at forhindre injection af ugyldige IDs
-->

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

<!--
Talking points:
- A02 Security Misconfiguration: forkert opsætning af sikkerhedsindstillinger
- Ofte simpel at udnytte - angriber finder default konfigurationer
- Default settings ikke ændret: producentens standardindstillinger er sjældent sikre
- Unødvendige features: ekstra angrebsoverflader der ikke bruges
- Default credentials: admin/admin, root/root - første angribere prøver
- Debug mode i produktion: afslører intern systemstruktur og fejl
- Verbose error messages: stack traces med database struktur og kodedetaljer
- Directory listing: ser alle filer på serveren
-->

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

<!--
Talking points:
- ASP.NET Trace.axd: debug endpoint der viser detaljeret runtime information
- Eksponerer stack traces, request/response data, session variables, server paths
- Angriber får indsigt i intern systemstruktur, teknologi stack, database queries
- Kan afsløre sårbarheder, SQL injection points, kodelogik
- **Problem:** Debug/trace funktionalitet glemt aktiveret når man går i produktion
- Burde være disabled i production environment
-->

ASP.NET Trace Information:
`<http://www.domain.com/Trace.axd``>

- Eksponerer detaljeret debug information
- Stack traces synlige for alle
- Intern system information lækket

**Problem:** Debug mode aktiveret i produktion

---

### A02 - Eksempler fortsat

<!--
Talking points:
- **Missing security hardening:** Systemer ikke "hærdet" efter installation
- Cloud permissions: S3 buckets offentligt tilgængelige, overly permissive IAM roles
- App servers: Tomcat, IIS, Apache med default insecure settings
- Security headers mangler: ingen HSTS, CSP, X-Frame-Options
- **Backward compatibility:** Gamle usikre features bibeholdt for ikke at bryde legacy systemer
- SSL/TLS 1.0 stadig aktiveret, svage cipher suites tilladt
- Legacy authentication metoder ikke disabled
-->

Missing security hardening

- Improperly configured permissions på cloud services
- Security settings i app servers, frameworks, libraries ikke sat korrekt
- Server sender ikke security headers

Backward compatibility prioriteret over sikkerhed

- Ældre, usikre konfigurationer bibeholdt
- Legacy features ikke disabled

---

### A02 - Forsvar

<!--
Talking points:
- **Hardening process:** Systematisk sikring af systemer efter installation
- Fjern unødvendige features: mindre angrebsoverflade (remove sample apps, unused services)
- Disable default accounts og skift alle default passwords
- Minimal installation: kun installer hvad der faktisk bruges
- **Security headers:** HTTP headers der instruerer browseren i sikkerhedspolitikker
- HSTS (Strict-Transport-Security): tving HTTPS i 1 år
- CSP (Content-Security-Policy): kun load scripts fra eget domæne
- X-Frame-Options: DENY forhindrer clickjacking
- **Error handling:** Generiske fejlbeskeder til brugere ("Something went wrong")
- Detaljeret logging server-side til debugging, men aldrig eksponeret til brugere
-->

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

<!--
Talking points:
- Automatiserede værktøjer til at identificere sikkerhedssårbarheder
- OWASP ZAP: gratis open-source web app scanner
- Burp Suite: industri-standard penetration testing tool
- Nessus: infrastruktur og network scanning
- **SAST** (Static): analyserer kildekode uden at køre den - finder coding errors
- **DAST** (Dynamic): tester running application - simulerer angreb
- **SCA** (Software Composition Analysis): checker tredjepartsbiblioteker for kendte CVEs
-->

**Hvad er det?**

- Tools der automatisk scanner for kendte sårbarheder
- Eksempler: OWASP ZAP, Burp Suite, Nessus

**Typer:**

- SAST: Static Application Security Testing
- DAST: Dynamic Application Security Testing
- SCA: Software Composition Analysis

---

### Scannere - Fordele

<!--
Talking points:
- **Hastighed:** Automatisk scanning af stor codebase på timer vs. dage/uger manuelt
- **Konsistens:** Samme checks hver gang - ingen menneskelige fejl eller glemte tests
- **CI/CD integration:** Kan køres automatisk ved hver commit/build - shift-left security
- **Coverage:** Checker systematisk for hundredvis af kendte sårbarheder (OWASP Top 10, CVEs)
- **Cost-effective:** Billigere end at ansætte security experts til manuel testing
- **Hyppighed:** Kan køres dagligt/kontinuerligt vs. manual pentest en gang årligt
- **Early detection:** Find sårbarheder tidligt i development cycle - billigere at fixe
- **Dokumentation:** Genererer automatisk rapporter med detaljer og remediation
-->

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

<!--
Talking points:
- **False positives:** Mange falske alarmer - rapporterer sårbarheder der ikke eksisterer, spild af tid
- **False negatives:** Misser komplekse sårbarheder og context-specific issues
- **Business logic flaws:** Kan ikke detektere logiske fejl (fx "kan købe varer for negativ pris")
- **Business logic:** Forstår ikke applikationens formål og workflow
- **Configuration:** Kræver ekspertise at konfigurere korrekt - ellers dårlige resultater
- **Aggressive scanning:** Kan overload servere, crashe systemer eller corrumpere data
- **Begrænsning til kendte:** Finder kun kendte vulnerability patterns fra signature database
- **Zero-days:** Nye, ukendte sårbarheder opdages ikke
- **Supplement:** Skal kombineres med manuel testing, ikke en komplet erstatning
-->

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

<!--
Talking points:
- **Kombineret approach:** Ingen enkelt metode er nok - brug flere lag af sikkerhed
- **Scannere:** Automatiske scannere til kontinuerlige baseline checks i CI/CD
- **Manuel pentest:** Eksperter finder komplekse sårbarheder og business logic flaws
- **Code reviews:** Peer review og security-focused code review
- **Architecture reviews:** Sikkerhedsanalyse af system design før implementation
- **Defense in depth:** Lag-på-lag sikkerhed - hvis et lag fejler, fanger andet lag det
-->

**Kombineret approach:**

- Scannere til continuous baseline checks
- Manuel penetration testing
- Code reviews
- Security architecture reviews

**Defense in depth!**
