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

# Autentifikation på hjemmesider

<!--
Talking points:
- Dette emne handler om hvordan man verificerer brugere på web og services
- Fokus på forskellige metoder: Basic auth, cookies og bearer tokens
- OAuth2 og OpenIDConnect til delegeret adgang
- OWASP A7 sårbarheder - kritiske sikkerhedsrisici ved autentifikation
-->

Redegør for og vis eksempler på hvordan man kan implementere autentifikation på
hjemmesider samt web services, herunder basic authentication, cookies samt bearer
tokens.
Redegør for de grundlæggende elementer i OAuth2 og forskellen til OpenIDConnect.
Samt forklar hvilke sårbarheder man skal være særligt opmærksomme på i
forbindelse med autentifikation, herunder sikker kommunikation samt opbevaring
af adgangskoder og øvrige sårbarheder som omtalt i OWASP A7 Authentication
Failures.

---

### Authentication & authorization

<!--
Talking points:
- Authentication (autentifikation): Proces der verificerer *hvem* brugeren er - "er du den du siger du er?"
- Authorization (autorisation): Bestemmer *hvad* en verificeret bruger må tilgå - adgangskontrol
- Vigtigt at skelne mellem de to - først verificer identitet, derefter tildel rettigheder
-->

- **Authentication:** _Hvem_ man er
- **Authorization:** _Hvad_ har man adgang til

---

### Http Protocol

<!--
Talking points:
- HTTP er stateless - hver request er uafhængig, serveren husker ikke tidligere interaktioner
- Dette skaber udfordring for authentication - hvordan husker vi hvem brugeren er?
- Tre primære løsninger: Basic Authentication (simpel), Cookies (lagrer session), Bearer tokens (selvstændige tokens)
-->

- Stateless
  - Udfordring ift. auth
- Metoder:
  - Basic Authentication
  - Cookies
  - Bearer tokens

---

### Basic Authentication

<!--
Talking points:
- Brugernavn og password sendes med i *hver* HTTP request i Authorization headeren
- Base64 encoding - ikke kryptering, bare encoding (kan nemt decodes)
- Fordel: Ekstremt nem at implementere
- Ulemper: Passwords i klartekst over netværket, clients skal gemme passwords
- Konklusion: Bør ikke bruges i moderne applikationer pga. sikkerhedsrisici
-->

- Credentials sendes med i hver request
  - Base64 encoded
- Fordel: nem!
- Ulemper
  - Passwords står i klartekst
  - Clienter skal gemme klar tekst passwords

**Bør ikke bruges**

---

### Cookies

<!--
Talking points:
- Cookies er små tekstfiler som browseren gemmer på brugerens computer
- Hjemmesider placerer dem via Set-Cookie header for at huske information
- Løser HTTP's stateless problem - gør det muligt at vedligeholde sessioner
- Anvendelser: Session management (hvem er logget ind), personalization (præferencer), tracking (brugeradfærd)
-->

- Data der gemmes i tekstfiler `Set-Cookie: <cookie-name>=<cookie-value>`
- Placeres af websider
  - Lagre informationer om brugeren
- Løser stateless problemet
  - Session management
  - Personalization
  - Tracking

---

### Cookies - Egenskaber

<!--
Talking points:
- Sendes automatisk med hver request til samme domæne - både fordel og risiko
- Ulempe: Automatisk afsendelse åbner for CSRF (Cross-Site Request Forgery) attacks
- Max størrelse: 4KB per cookie
- Lifespan: Session cookies (slettes ved browser luk) vs. Persistent cookies (lever til expiration date)
- Domæne: First party (samme domæne) vs. Third party (anden domæne til tracking)
-->

- Sendes med hver request
  - Ulempe: giver mulighed for CSRF
- Fylder max 4kb
- Lifespan `Set-Cookie: ...; Expires=<date>`
  - Session / Persistant
- Domæne `Set-Cookie ...; Domain=<domain-value>`
  - First party / third party

---

### CSRF

<!--
Talking points:
- CSRF (Cross-Site Request Forgery): Angreb der udnytter at cookies sendes automatisk
- Eksempel: evil.com kan lave request til bank.com som automatisk sender brugerens login-cookie
- Forsvar 1: CSRF Tokens - unik token per session valideres server-side
- Forsvar 2: SameSite Cookie Attribute - Strict (kun same-site requests) eller Lax (tillader nogle cross-site)
-->

- Udnytter at cookies sendes automatisk
  - evil.com kan sende cookies lagret af bank.com
- Forsvar
  - CSRF Tokens
  - SameSite Cookie Attribute med Strict eller Lax `Set-Cookie: ...; SameSite=<value>`

---

### Bearer Tokens

<!--
Talking points:
- Self-contained tokens: Indeholder selv alle nødvendige claims/informationer om brugeren
- Digitally signed: Kan verificeres uden database lookup - server kan validere ægtheden
- Stateless: Server behøver ikke gemme session state - skalerbart i microservices
-->

- Self contained: har informationer om claims
- Digitally signed
- Stateless

---

### JWT Tokens

<!--
Talking points:
- JWT (JSON Web Token): Populær implementering af bearer tokens
- Header: Algoritme brugt til signatur (fx HMAC, RSA) og token type (JWT)
- Payload: Claims som user ID, navn, roller, expiration time (exp)
- Signatur: Sikrer at token ikke er manipuleret
- Lagres typisk i localStorage (ikke cookies) for at undgå CSRF
-->

- Har 3 dele:
  - Header: Indeholder algoritme og type
  - Payload: Indeholder Claims (user id, name, role, expiration, etc,)
  - Signatur
- Stores i localStorage

---

### JWT Tokens

<!--
Talking points:
- **Fordele:** Ingen server state nødvendig, hver request er selvstændig, fungerer perfekt i SPAs (Single Page Applications), mobile apps og microservices arkitektur, immune overfor CSRF da de sendes manuelt
- **Ulemper:** Sårbar overfor Man-in-the-Middle hvis ikke HTTPS bruges, kan ikke invalideres før expiration (kompromitteret token gyldig til udløb)
-->

**Fordele**

- Ingen server state
- Hver request identificeres for sig
- Virker i SPA's, mobile apps og microservices
- Fri for CSRF attacks

**Ulemper**

- Man in the middle attacks
  - Brug altid HTTPS
- Ingen session invalidering

---

### OAuth2

<!--
Talking points:
- OAuth2 er et authorization framework for delegeret adgang
- Lader tredjepartsapps få begrænset adgang til brugerens ressourcer *uden* at dele password
- Eksempel: Photo printing app beder om adgang til dine Google Photos - du godkender uden at give dit Google password
-->

- Delegering af authorization
- Giver tredje parts apps adgang til brugerens beskyttede resourcer
  - Ex Photo app spørger om adgang til Google Photos

---

### OAuth2

<!--
Talking points:
- **Client ID & Secret:** Identificerer applikationen overfor authorization server
- **Redirect URI:** Hvor brugeren sendes tilbage efter godkendelse
- **Flows/Grant types:** Authorization Code (webapps), PKCE (Proof Key for Code Exchange - mobile/SPAs), Client Credentials (server-to-server)
- **AccessToken:** Token der giver adgang til beskyttede ressourcer
-->

**Bestanddele**

- Client ID & Client Secret
- Redirect URI
- Flows / Grant types
  - Authorization Code
  - PKCE
  - Client credentials
  - ...
- AccessToken

---

### OAuth2 vs OpenIDConnect

<!--
Talking points:
- OpenIDConnect (OIDC) bygger ovenpå OAuth2
- Tilføjer authentication lag - OAuth2 er kun authorization
- Muliggør Single Sign-On (SSO) - én login på tværs af flere apps
- **AccessToken** (adgang til ressourcer) + **IDToken** (indeholder brugeridentitet som JWT)
-->

- Overbygning på OAuth2
- Tiløjer authentication
- Giver mulighed for at dele logins mellem apps
- AccessToken & IDToken

---

### OWASP Top 10 - A07:2021

<!--
Talking points:
- A07:2021 hedder "Identification and Authentication Failures" (tidligere Broken Authentication)
- Dækker svage auth-mekanismer og implementeringsfejl
- Fire kritiske områder: Sikker kommunikation (HTTPS), korrekt password opbevaring (hashing), session management (secure cookies), brute force beskyttelse (rate limiting)
-->

**Identification and Authentication Failures**

- Dækker svage autentifikationsmekanismer og implementeringsfejl
- Kritiske områder:
  - Sikker kommunikation
  - Opbevaring af adgangskoder
  - Session management
  - Brute force beskyttelse

---

### Sikker kommunikation

<!--
Talking points:
- **HTTPS obligatorisk:** TLS/SSL krypterer data under transport, forhindrer Man-in-the-Middle attacks hvor angriber aflytter traffik
- Beskytter credentials og tokens mod aflytning
- **Aldrig credentials i URL:** Query parameters logges i browser history, server logs og proxy logs - brug POST request body i stedet
-->

**HTTPS er obligatorisk**

- Beskytter mod Man-in-the-Middle attacks
- Krypterer credentials og tokens under transport

**Aldrig send credentials i URL**

- Logges i browser history og server logs
- Brug POST requests og request body

---

### Opbevaring af adgangskoder

<!--
Talking points:
- **Hash passwords, aldrig klartekst:** Hvis database kompromitteres, er passwords stadig beskyttet
- Moderne algoritmer: bcrypt, Argon2, PBKDF2 - designet til at være langsomme (forhindrer brute force)
- Unikt salt per bruger - forhindrer rainbow table attacks
- Aldrig MD5/SHA1/SHA256 - for hurtige, designet til data integrity ikke passwords
- **Password policies:** Længde vigtigere end kompleksitet, check mod haveibeenpwned database med leaked passwords
-->

**Hash passwords - aldrig klartekst**

- Moderne algoritmer: bcrypt, Argon2, PBKDF2
- Brug unikt salt per bruger
- Aldrig MD5/SHA1/SHA256

**Svage password policies**

- Kræv stærke passwords (længde > kompleksitet)
- Check mod kendte leaked passwords

---

### Session og brute force beskyttelse

<!--
Talking points:
- **Session Management:** Generer nye session IDs efter login (forhindrer session fixation), Secure flag (kun HTTPS), HttpOnly flag (JavaScript kan ikke læse), passende timeout
- **Brute Force beskyttelse:** Rate limiting (max X requests per minut), account lockout efter flere fejl (men pas på DoS), MFA/2FA (Multi-Factor Authentication) gør brute force praktisk umuligt
-->

**Session Management**

- Generer nye session IDs efter login
- Secure og HttpOnly flags på cookies
- Passende session timeout

**Brute Force beskyttelse**

- Rate limiting på login endpoints
- Account lockout efter X fejlede forsøg
- Multi-Factor Authentication (MFA)
