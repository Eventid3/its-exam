# Autentifikation på hjemmesider

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

- Authentication: hvem man er
- Authorization: hvad har man adgang til

---

### Http Protocol

- Stateless
  - Udfordring ift. auth
- Metoder:
  - Basic Authentication
  - Cookies
  - Bearer tokens

---

### Basic Authentication

- Credentials sendes med i hver request
  - Base64 encoded
- Fordel: nem!
- Ulemper
  - Passwords står i klartekst
  - Clienter skal gemme klar tekst passwords

**Bør ikke bruges**

---

### Cookies

- Data der gemmes i tekstfiler
- Placeres af websider
  - Lagre informationer om brugeren
- Løser stateless problemet
  - Session management
  - Personalization
  - Tracking

---

### Cookies

- Sendes med hver request
  - Ulempe: giver mulighed for CSRF
- Fylder max 4kb
- Lifespan
  - Session / Persistant
- Domæne
  - First party / third party

---

### CSRF

- Udnytter at cookies sendes automatisk
  - evil.com kan sende cookies lagret af bank.com
- Forsvar
  - CSRF Tokens
  - SameSite Cookie Attribute med Strict eller Lax

---

### Bearer Tokens

- Self contained: har informationer om claims
- Digitally signed
- Stateless

---

### JWT Tokens

- Har 3 dele:
  - Header: Indeholder algoritme og type
  - Payload: Indeholder Claims (user id, name, role, expiration, etc,)
  - Signatur
- Stores i localStorage

---

### JWT Tokens

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

- Delegering af authorization
- Giver tredje parts apps adgang til brugerens beskyttede resourcer
  - Ex Photo app spørger om adgang til Google Photos

---

### OAuth2

**Bestanddele**

- Client ID & Client Secret
- Redirect URI
- Flows / Grant types
  - Authorization Code
  - PKCE
  - Client credentials
  - ...

---

### OAuth2 vs OpenIDConnect

- Overbygning på OAuth2
- Tiløjer authentication
- Giver mulighed for at dele logins mellem apps

---

### OWASP Top 10 - A07:2021

**Identification and Authentication Failures**

- Dækker svage autentifikationsmekanismer og implementeringsfejl
- Kritiske områder:
  - Sikker kommunikation
  - Opbevaring af adgangskoder
  - Session management
  - Brute force beskyttelse

---

### Sikker kommunikation

**HTTPS er obligatorisk**

- Beskytter mod Man-in-the-Middle attacks
- Krypterer credentials og tokens under transport

**Aldrig send credentials i URL**

- Logges i browser history og server logs
- Brug POST requests og request body

---

### Opbevaring af adgangskoder

**Hash passwords - aldrig klartekst**

- Moderne algoritmer: bcrypt, Argon2, PBKDF2
- Brug unikt salt per bruger
- Aldrig MD5/SHA1/SHA256

**Svage password policies**

- Kræv stærke passwords (længde > kompleksitet)
- Check mod kendte leaked passwords

---

### Session og brute force beskyttelse

**Session Management**

- Generer nye session IDs efter login
- Secure og HttpOnly flags på cookies
- Passende session timeout

**Brute Force beskyttelse**

- Rate limiting på login endpoints
- Account lockout efter X fejlede forsøg
- Multi-Factor Authentication (MFA)
