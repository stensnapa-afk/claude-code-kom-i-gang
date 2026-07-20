# 04 — Skills og MCP

To måter å utvide Claude Code på. De løser hver sin ting, og forveksles hele tiden:

- **Skills** gir Claude *prosedyrer* — «slik gjør vi en release her».
- **MCP** gir Claude *tilgang* — til GitHub, en database, en nettleser.

Kort sagt: skills lærer den *hvordan*, MCP gir den *hva* å gjøre det med.

Dette kapittelet er bevisst smalt. Det finnes hundrevis av MCP-servere. Du trenger to.

---

# Del 1 — Skills

## Hva en skill er

En mappe med en `SKILL.md` som beskriver en oppgave. Claude laster den enten selv når den er relevant, eller du kaller den med `/navn`.

Det som gjør skills verdt å bry seg om: **innholdet lastes bare når skillen brukes.** En `CLAUDE.md` på 400 linjer koster kontekst i hver eneste økt. En skill på 400 linjer koster ingenting før den trengs.

Regelen er derfor: **fakta i `CLAUDE.md`, prosedyrer i en skill.** Merker du at `CLAUDE.md` har fått en seksjon med nummererte steg, hører den hjemme her i stedet.

## Innebygde skills å prøve først

Disse følger med. Skriv `/` i en økt for å se hvilke din versjon har.

### `/security-review`

Går gjennom endringene dine og ser etter sikkerhetsproblemer — injeksjon, manglende tilgangskontroll, hemmeligheter på avveie.

```text
/security-review
```

Verdt å kjøre før en PR som rører autentisering, brukerdata eller noe utoverrettet. Den finner ikke alt, men den finner det åpenbare, og det åpenbare er det som faktisk blir utnyttet.

### `/code-review`

Gjennomgang av det du har endret så langt. Nyttig som en runde til før du ber et menneske bruke tid.

### `/run` og `/verify`

Starter appen din og sjekker at en endring faktisk gjør det den skal — i den kjørende appen, ikke bare i testene. Dette er direkte motgift mot fallgruve nummer én i [kapittel 05](05-vanlige-fallgruver.md).

Krever Claude Code v2.1.145 eller nyere.

## Lag din første skill

Eksempel: en skill som oppsummerer ukommitterte endringer og flagger risiko.

**1. Lag mappa.** Personlig (alle prosjektene dine):

```bash
mkdir -p ~/.claude/skills/oppsummer-endringer
```

Eller prosjektspesifikk (deles med teamet via git):

```bash
mkdir -p .claude/skills/oppsummer-endringer
```

**2. Skriv `SKILL.md`:**

```yaml
---
description: Oppsummerer ukommitterte endringer og flagger risiko. Bruk når brukeren spør hva som er endret, vil ha en commit-melding, eller ber om en gjennomgang av diffen.
---

## Endringer nå

!`git diff HEAD`

## Instruksjoner

Oppsummer endringene over i to–tre kulepunkter. List deretter risiko du
ser: manglende feilhåndtering, hardkodede verdier, tester som må oppdateres.
Er diffen tom, si at det ikke finnes ukommitterte endringer.
```

**3. Bruk den.** Enten direkte:

```text
/oppsummer-endringer
```

Eller la Claude velge selv ved å spørre om noe som matcher beskrivelsen: «hva har jeg endret?»

### Det som er verdt å legge merke til

**`description` er ikke pynt.** Det er den Claude bruker for å avgjøre om skillen er relevant. Skriv den som en trigger — «Bruk når brukeren ...» — ikke som en tittel.

**Mappenavnet blir kommandonavnet.** `oppsummer-endringer/` gir `/oppsummer-endringer`.

**`` !`kommando` `` kjører før Claude leser.** Linja erstattes med kommandoens output. Det er derfor skillen over faktisk ser den ekte diffen din, i stedet for å gjette ut fra åpne filer.

### Frontmatter-felt som finnes

| Felt | Betydning |
|---|---|
| `description` | Når skillen er relevant. Den viktigste. |
| `name` | Overstyrer mappenavnet som kommandonavn |
| `allowed-tools` | Begrens hvilke verktøy skillen får bruke, f.eks. `Bash(git *)` |
| `disable-model-invocation` | `true` = kun du kan kalle den, Claude velger den aldri selv |
| `context: fork` | Kjør i egen kontekst så hovedsamtalen ikke fylles opp |
| `agent` | Kjør skillen i en bestemt subagent |

Sett `disable-model-invocation: true` på alt som har bivirkninger. En deploy-skill skal du kalle, ikke Claude.

## Hvor skills bor

| Plassering | Gjelder |
|---|---|
| `~/.claude/skills/<navn>/SKILL.md` | Alle dine prosjekter |
| `.claude/skills/<navn>/SKILL.md` | Dette prosjektet, deles via git |

Endringer plukkes opp umiddelbart — du trenger ikke starte økten på nytt.

## Skills fra andre

Det finnes et offisielt marketplace:

```text
/plugin marketplace add anthropics/claude-plugins-official
/plugin install skill-creator@claude-plugins-official
```

`skill-creator` hjelper deg å skrive skills. Samme aktsomhet som for MCP gjelder: en skill er instruksjoner Claude følger, så installer fra kilder du kjenner.

---

# Del 2 — MCP

## Hva MCP er

Model Context Protocol — en åpen standard for å koble Claude til eksterne systemer. En MCP-server eksponerer verktøy Claude kan kalle: «hent issue #42», «kjør denne spørringen», «klikk på den knappen».

Uten MCP jobber Claude med filene dine. Med MCP jobber den med systemene dine.

## Før du legger til noe som helst

To ting som ikke er valgfrie å tenke gjennom:

**Hver server koster kontekst.** Verktøybeskrivelsene lastes inn i hver økt, også når du ikke bruker dem. Femten servere gjør Claude tregere og mer forvirret, ikke smartere.

**Hver server er kode fra noen andre.** Den kjører lokalt eller får tokenet ditt. Anthropic auditerer ikke MCP-servere. Hold deg til offisielle servere fra tjenesteleverandøren selv.

Derfor: **start med to.** Legg til flere når du konkret har savnet noe.

## Anbefalt startsett

De to første dekker de fleste. De to siste når du får bruk for dem.

### 1. Context7 — oppdatert dokumentasjon

Den enkleste å begynne med: ingen innlogging, ingen token, ingen risiko.

```bash
claude mcp add --transport http --scope user context7 https://mcp.context7.com/mcp
```

Løser et reelt problem: modellens kunnskap har en skjæringsdato, og biblioteker endrer seg. Med Context7 slår Claude opp gjeldende dokumentasjon for React, Next.js, Prisma og hundrevis av andre i stedet for å huske feil.

Merk `--scope user` — den er nyttig i alle prosjekter, så den hører hjemme på brukernivå.

### 2. GitHub — issues og PR-er

```bash
claude mcp add --transport http --scope user github https://api.githubcopilot.com/mcp/ \
  --header "Authorization: Bearer YOUR_GITHUB_PAT"
```

Bytt ut `YOUR_GITHUB_PAT` med et [personal access token](https://github.com/settings/tokens). **Gi det minst mulig rettigheter** — er du mest ute etter å lese issues og PR-er, trenger det ikke skriverettigheter.

Dette er GitHubs egen server på remote URL. Den gamle lokale `@modelcontextprotocol/server-github` er ikke lenger den anbefalte veien.

Nå kan du be om ting som «implementer det som beskrives i issue #42 og lag en PR».

`claude mcp add` validerer ikke tokenet. Sjekk med `/mcp` at serveren står som `connected`, ikke `failed`.

### 3. Playwright — la Claude se nettsiden

```bash
claude mcp add playwright -- npx -y @playwright/mcp@latest
```

Claude kan navigere, klikke, fylle skjemaer, lese konsollen og ta skjermbilder. Det gjør en stor forskjell for frontend-arbeid: i stedet for at du beskriver at knappen ser rar ut, kan den se det selv — og verifisere at fiksen faktisk virket i nettleseren.

Legg til denne når du jobber med noe som har et grensesnitt.

### 4. En database-server

Når du vil at Claude skal kunne stille spørsmål mot skjemaet ditt. Bruk leverandørens egen server der den finnes — de fleste sky-databaser har en. Sjekk [Anthropic Directory](https://claude.ai/directory) for gjennomgåtte alternativer.

**Gi en lesebruker, ikke eieren.** Enkelt råd, spart mange for en dårlig dag.

## Scopes — hvem serveren gjelder for

| Scope | Lagres i | Gjelder |
|---|---|---|
| `local` (standard) | Din brukerkonfigurasjon | Kun deg, kun dette prosjektet |
| `project` | `.mcp.json` i prosjektrota | Hele teamet, via git |
| `user` | Din brukerkonfigurasjon | Kun deg, alle prosjekter |

```bash
claude mcp add --scope user ...      # verktøy du vil ha overalt
claude mcp add --scope project ...   # verktøy prosjektet trenger
```

Tommelfingerregel: Context7 og GitHub på `user`. Noe prosjektspesifikt — en database, et internt API — på `project`, så kollegene får det automatisk.

## `.mcp.json` for team

Med `--scope project` havner serveren i `.mcp.json` i prosjektrota, som committes:

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/",
      "headers": {
        "Authorization": "Bearer ${GITHUB_PAT}"
      }
    },
    "playwright": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@playwright/mcp@latest"]
    }
  }
}
```

**Legg aldri tokenet rett inn.** Bruk `${MILJØVARIABEL}` som over, og la hver enkelt sette sin egen. Ellers ligger nøkkelen i git-historikken permanent.

Når en kollega åpner prosjektet, blir de spurt om de godkjenner serverne i `.mcp.json`. Det er en reell sikkerhetsgrense — og det er derfor `.mcp.json` selv er en beskyttet sti som Claude ikke kan endre uten at du sier ja.

## Administrere servere

```bash
claude mcp list              # alle servere og tilkoblingsstatus
claude mcp remove <navn>     # fjern
```

Inne i en økt:

```text
/mcp
```

Viser status og lar deg autentisere mot servere som bruker OAuth (Sentry, Notion, Linear og flere). For de er kommandoen bare:

```bash
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp
```

— og så `/mcp` for å logge inn i nettleseren. Ingen tokens å håndtere manuelt, som er å foretrekke der det er et alternativ.

---

## Oppsummert

**Skills** når du gjentar deg selv. Start med å legge merke til hva du limer inn flere ganger.

**MCP** når du kopierer data inn i chatten fra et annet system. Start med Context7 og GitHub. Legg til Playwright når du jobber med grensesnitt.

Motstå fristelsen til å installere alt på én gang. Et smalt oppsett du forstår slår et bredt oppsett du ikke gjør.

---

**Neste:** [05 — Vanlige fallgruver](05-vanlige-fallgruver.md)
