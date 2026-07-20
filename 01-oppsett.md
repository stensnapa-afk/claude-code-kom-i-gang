# 01 — Oppsett

Mål for dette kapittelet: du har Claude Code installert, er logget inn, og har kjørt din første økt i et ekte prosjekt.

Beregn ti minutter.

## Systemkrav

| | |
|---|---|
| **Operativsystem** | macOS 13+, Windows 10 (1809)+, Ubuntu 20.04+, Debian 10+ |
| **Maskinvare** | 4 GB RAM, x64 eller ARM64 |
| **Nettverk** | Påkrevd — Claude Code kjører modellen i skyen, ikke lokalt |
| **Konto** | Claude-abonnement (Pro/Max/Team/Enterprise) *eller* API-nøkkel fra Claude Console |

Du trenger **ikke** Node.js hvis du bruker den anbefalte installasjonsmetoden under.

## Steg 1 — Installer

### Anbefalt: native installer

Denne gir automatiske oppdateringer og krever ingen Node.js.

**Windows (PowerShell):**

```powershell
irm https://claude.ai/install.ps1 | iex
```

**macOS, Linux, WSL:**

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

> **Sidenote om `curl | bash`:** du er i ferd med å kjøre et skript fra internett uten å lese det. Det er greit her fordi domenet er Anthropics eget — men det er nøyaktig mønsteret du *ikke* skal godta ukritisk ellers. Vil du se skriptet først, dropp pipen: `curl -fsSL https://claude.ai/install.sh` og les det. Samme prinsipp kommer tilbake i [kapittel 05](05-vanlige-fallgruver.md).

### Alternativer

Disse fungerer, men oppdaterer seg **ikke** selv — du må oppgradere manuelt.

```bash
# macOS — Homebrew
brew install --cask claude-code

# Windows — WinGet
winget install Anthropic.ClaudeCode

# Alle plattformer — npm (krever Node.js 22 eller nyere)
npm install -g @anthropic-ai/claude-code
```

### Ekstra for Windows-brukere

Installer [Git for Windows](https://git-scm.com/download/win). Claude Code bruker Unix-verktøyene som følger med (`grep`, `sed`, `awk` med flere) når de finnes. Uten dem faller den tilbake på PowerShell, som fungerer, men gir deg færre av eksemplene i dokumentasjonen gratis.

## Steg 2 — Verifiser installasjonen

```bash
claude --version
```

Du skal få noe i retning av `2.1.215 (Claude Code)`.

Får du «command not found», er som regel ikke installasjonsmappa lagt til i PATH ennå. Lukk terminalen, åpne en ny, og prøv igjen. Hjelper ikke det:

```bash
claude doctor
```

`doctor` kjører en ren lesesjekk av oppsettet ditt — installasjon, PATH, konfigurasjonsfiler, nettverk — uten å starte en økt. Det er første kommando å nå etter når noe er rart.

## Steg 3 — Logg inn

Innloggingen skjer automatisk første gang du starter Claude Code. Du kan også trigge den eksplisitt:

```bash
claude login
```

Du får velge mellom to måter:

**1. Claude-abonnement** — Pro, Max, Team eller Enterprise. Nettleseren åpner seg, du godkjenner, ferdig. Bruken din trekkes fra abonnementets kvote. Dette er det de fleste vil ha.

**2. Claude Console (API)** — du betaler per token mot API-et i stedet. Passer hvis du allerede har et Console-oppsett eller vil ha bruken på et firmaprosjekt med eget budsjett.

Innloggingen lagres kryptert lokalt (Keychain på macOS, filrettighetsbeskyttet ellers) og fornyes automatisk. Du skal ikke måtte gjøre dette igjen med det første.

Vil du bytte konto senere, kjør `/login` inne i en økt.

### For CI og skript

Trenger du å kjøre Claude Code uten en nettleser tilgjengelig — for eksempel i en byggejobb:

```bash
claude setup-token
```

Den gir deg et langtidstoken (ett år) som du legger i miljøvariabelen `CLAUDE_CODE_OAUTH_TOKEN`. Behandle det som et passord: hemmelighetsstore, aldri i git.

## Steg 4 — Din første økt

Ikke lag et tomt testprosjekt. Gå til noe ekte du allerede jobber med — Claude Code er nesten ubrukelig uten kode å se på, og overraskende god med.

```bash
cd sti/til/prosjektet
claude
```

Første gang i en ny mappe blir du spurt om du stoler på den (workspace trust). Det er en reell sikkerhetsgrense: den avgjør om prosjektets egne konfigurasjonsfiler får lov til å påvirke økten. Si ja til dine egne prosjekter, tenk deg om ved kode du nettopp lastet ned fra en fremmed.

Prøv å spørre om noe du allerede vet svaret på — det er den beste måten å kalibrere tillit på:

```text
Forklar hva dette prosjektet gjør, basert på koden.
```

```text
Hvor håndteres innlogging?
```

Legg merke til at du ikke trenger å fortelle den hvilke filer den skal se på. Claude Code leter selv.

## Steg 5 — Lag en CLAUDE.md

Fortsatt inne i økten:

```text
/init
```

Claude leser gjennom prosjektet og skriver en `CLAUDE.md` i rota: hva prosjektet er, hvordan det bygges og testes, hvilke konvensjoner som gjelder. Fila lastes automatisk inn ved hver framtidige økt i mappa.

Les gjennom det den skrev og rett opp det som er feil. Dette er det viktigste enkeltgrepet i hele guiden — en god `CLAUDE.md` er forskjellen mellom en assistent som gjetter og en som vet. [Kapittel 03](03-claude-md-mal.md) går gjennom hva som bør stå der og hvorfor.

Commit fila. Den hører hjemme i git, sammen med koden den beskriver.

## Andre måter å starte på

```bash
claude                          # vanlig interaktiv økt
claude -p "oppsummer README"    # ett spørsmål, svar, avslutt (headless)
claude -c                       # fortsett forrige økt i denne mappa
claude -r                       # velg blant tidligere økter
claude --permission-mode plan   # start i planleggingsmodus (se kapittel 02)
```

`-p` er nyttig i skript og pipelines. `-c` er den du kommer til å bruke oftest når du kommer tilbake til noe dagen etter — hele samtalen er der fortsatt.

---

**Neste:** [02 — Daglig bruk](02-cli-bruk.md)
