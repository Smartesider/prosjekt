# SKYPROSJEKT.NO – EKSPERT PROGRAMBESKRIVELSE FOR AI-GENERERING (VS CODE COMPATIBLE)

---

## 1. FORMÅL

**SkyProsjekt.no** er et webbasert prosjektstyringssystem som skal levere full funksjonalitet for prosjekter, kunder, økonomi, tidsregistrering, kommunikasjon, dokumentasjon, varsling og administrasjon — med sterk fokus på sikkerhet, rollebasert tilgang og pålitelighet.

---

## 2. SERVER, PORTER, KATALOG OG URL

| Parameter             | Verdi                                    | Kommentar / LOCK                                                            |
| --------------------- | ---------------------------------------- | --------------------------------------------------------------------------- |
| Backend-port          | 8000                                  | # LOCK: Kun port 8000 for API trafikk. Ingen andre porter for backend.      |
| Server-root katalog   | `/home/skyprosjekt.no/public_html/` | # LOCK: Alle filoperasjoner må begrenses hit og underkataloger.             |
| Webadresse            | `https://skyprosjekt.no/`           | # LOCK: Kun dette domenet tillatt som origin i CORS-policy.                 |
| Frontend API-base-url | `https://skyprosjekt.no/api/v1/`    | # LOCK: Frontend må kun kalle API her. Ingen hardkodede IP-er eller porter. |

---

## 3. KATALOGSTRUKTUR

```
/home/skyprosjekt.no/public_html/
├── index.html               # Landingsside, WOW-faktor, login (v2 fullført)
├── bruker/
│   └── index.html           # Ansattportal, placeholder i v2
├── admin/
│   ├── index.html           # Admin dashboard
│   ├── prosjekter.html      # Prosjektstyring
│   ├── kunder.html          # Kundeadministrasjon
│   ├── økonomi.html         # Budsjett og regnskap
│   ├── tidsregistrering.html# Tidsregistrering
│   └── ...                 # Flere adminsider
└── brukerportal/
    └── index.html           # Kundeinnlogging, rapporter, meldinger
```

---

## 4. TEKNOLOGI

* Backend: **FastAPI (Python 3.11+)**
* Frontend: **Ren HTML5, CSS3, ES6+ JavaScript**
* Kommunikasjon: **REST API med JSON payload**
* Autentisering: **JWT (JSON Web Tokens) + rollebasert tilgang (RBAC) + valgfri 2FA**
* Database: valgfri, men anbefalt PostgreSQL for skalerbarhet
* Hosting: Ubuntu 22.04 LTS eller nyere, Nginx som revers proxy og SSL-terminering

---

## 5. API-SPESIFIKASJONER

### 5.1 Generelle krav

* **Alle API-endepunkter skal være under `/api/v1/`**
* **Kun port 8000 er tillatt for API**
* **Streng CORS-policy:** tillat kun `https://skyprosjekt.no`
* **Alle API-responser må være JSON med Content-Type: application/json**
* **Inputvalidering er obligatorisk på alle endepunkter**
* **Ugyldige kall skal returnere HTTP 400 med strukturert JSON-feilmelding**

### 5.2 Autentisering

* Alle API-kall må inneholde gyldig JWT i `Authorization: Bearer <token>` header
* Token valideres på hver request, sjekker utløp, signatur og brukerrolle
* Roller: `admin`, `ansatt`, `kunde`
* 2FA-støtte via TOTP kan aktiveres per bruker

---

## 6. ROLLEBASERT TILGANG (RBAC)

| Rolle  | Tilgangsområder                                                         | Restriksjoner                                           |
| ------ | ----------------------------------------------------------------------- | ------------------------------------------------------- |
| Admin  | Full tilgang til alle moduler og API                                    | Ingen restriksjoner                                     |
| Ansatt | Prosjektstyring, tidsregistrering, kundeoppfølging                      | Kan ikke administrere brukere eller systeminnstillinger |
| Kunde  | Kun egen brukerportal for å se prosjekter, fremdrift og sende meldinger | Ingen tilgang til backend eller andre kunders data      |

---

## 7. FUNKSJONALITET (V1 og V2 MERKET TYDELIG)

### 7.1 v1 – Grunnleggende funksjoner og valgte tillegg

#### Prosjektstyring

* Opprett, rediger, slett prosjekter
* Hovedmål og delmål med status
* Statusvalg: `Rapportert`, `Feil`, `Pågår`, `Venter kunde`, `Venter leverandør`, `Ferdig`
* Prosent fullført (beregnet automatisk)
* Prosjektnummer: 8-sifret hash (unik)
* Notatfelt for prosjektinfo (historikk med datostempling)
* Knytte bedrifter til prosjekt
* Tidsregistrering per dag/prosjekt, rapport og statistikk
* Budsjettstyring med 70 % varsel
* Varslingsinnstillinger: hvem varsles, hvordan (e-post, SMS, Slack), frekvens

#### Kundeadministrasjon

* Kundenummer: 8-sifret hash
* Bedrift med alle nødvendige felter (navn, org.nr, nettside, telefon, adresse)
* Kontaktpersoner med navn, stilling, epost, telefon
* Notater, import fra epost
* Oppdragsbeskrivelse, tillegg med dato og kilde

#### E-postfunksjonalitet

* Hente fra IMAP, sende, redigere, svare, slette
* Kopiere e-post til kundekort som notat
* Sporing: lesebekreftelse, klikk, antall åpninger

#### Innstillinger

* API-nøkkel for AI-integrasjon (OpenAI)
* SMS (SMStools.com) og SMTP oppsett
* Produkter og priser for tidsregistrering koblet til økonomi

#### Meldingsboks

* Send/motta meldinger mellom kunde og prosjektteam

#### Brukerportal (kunde)

* Vis fremdrift, utførte/igjenstående oppgaver
* Prioritering (Høy/Middels/Lav)
* Meldinger og prosjektinfo
* Responsivt, visuelt tiltalende GUI

#### Dokumenthåndtering

* Last opp, versjonskontroll, historikk

#### Automatiserte varslinger

* Gjentatte påminnelser ved utestående oppgaver og inaktivitet

#### Integrasjon med Google Calendar og Outlook

* Synkroniser milepæler og møter

#### Tidslinjevisning

* Kronologisk oversikt over prosjektendringer og aktivitet

#### Gantt-diagram og drag-drop prosjektplanlegging

* Avhengigheter, kritisk sti og endringer i UI

#### Full PWA

* Offline-støtte, push-notifikasjoner, installasjon

#### Dashboard med KPI

* Fargekodede varsler og oversikt

#### Automatisk sluttkostnadsestimat

* Varsel før overskridelse basert på tidsbruk/priser

#### Versjonskontroll på alle endringer

* Mulighet for rollback

#### REST API for integrasjoner

* Full dokumentasjon og sikker tilgang

#### Digital kundegodkjenning

* Milepæler og endringer signeres via portal

#### Varsling via e-post, SMS, Slack, Teams, push

#### Google Tasks-integrasjon

* Oppgaver synkroniseres

#### Varsling ved inaktivitet

#### Budsjettprognoser

#### Integrasjon med Google Drive og Dropbox

#### Audit-logg

* All brukeraktivitet logges detaljert

#### Ukesrapport via e-post, Slack eller SMS

#### Fiken API-integrasjon

* Autentisering og datautveksling via client\_id, client\_secret, company slug

---

### 7.2 v2 – Sikkerhet, administrasjon, UX og skalering

* Rollebasert tilgang (RBAC) implementert med admin, ansatt, kunde
* Native mobilapp i tillegg til PWA
* Support/ticketsystem for kundehenvendelser
* SLA- og servicenivåoppfølging med varsler
* Avansert rapportbygger for prosjekt/økonomi
* Integrasjon med Slack/Teams for chat
* AI-assistert oppgaveprioritering (planlagt)
* Fleksibel produktkatalog med kampanjestyring
* Push-notifikasjoner i app/nettleser
* Full GDPR-kompatibilitet og sikkerhetssertifisering
* Automatisk backup med versjonskontroll og rollback
* Multitenancy for flere organisasjoner på plattformen
* Faktura- og betalingsmodul (inkl. Fiken og andre) – planlagt videreutvikling

---

## 8. SIKKERHETSREGLER (Ufravikelige låser)

* Backend skal **kun** lytte på port 8000 (# LOCK)
* All API-trafikk må gå via `https://skyprosjekt.no/api/v1` (# LOCK)
* CORS-policy: Kun `https://prosjekt.skycode.no` tillatt (# LOCK)
* Alle filoperasjoner må begrenses til `/home/skyprosjekt.no/public_html/` og underkataloger (# LOCK)
* JWT med roller kreves for alle endepunkter (# LOCK)
* Streng inputvalidering med avvisning av uventede data (# LOCK)
* All destruktiv operasjon må ta backup før endring (# LOCK)
* Automatisk rollback ved feil (# LOCK)
* Logging i JSON-format med tidsstempling for alle operasjoner (# LOCK)
* 2FA-valgfri pålogging for økt sikkerhet (# LOCK)

---

## 9. FRONTEND-KRAV

* Kun statiske HTML/JS-filer
* Alle API-kall skal bruke `fetch` eller `axios` til `/api/v1` på port 8000
* Token lagres kun i `httpOnly` cookie eller `sessionStorage`
* Validering av token og roller i frontend før visning av sensitive sider
* Responsivt, mobilvennlig, høy UX-standard
* Inline feilmeldinger, varsler og API-feilhåndtering
* Login- og logout-funksjoner
* Automatisk token-refresh (om relevant)

---

## 10. BACKUP OG ROLLBACK

* Backup lagres som tidsstemplet filer i `/home/skyprosjekt.no/backups/`
* Maks 5 versjoner per fil/tabell
* Rollback kan trigges automatisk eller manuelt
* Alle endringer i kritiske filer/database logges med bruker, tidspunkt og operasjonstype

---

## 11. TEST OG CI/CD

* Statisk kodeanalyse: flake8, mypy, bandit integrert i pipeline
* Automatisk testing av port, katalog, JSON-format, CORS-policy
* PRs må ikke merges hvis låser brytes
* Test coverage minimum 90 %
* Automatisk bug tracking i GitHub via workflows

---


```

---
