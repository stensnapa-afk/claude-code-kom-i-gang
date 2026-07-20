# Claude Code — kom i gang

En nybegynnervennlig guide til å installere, forstå og bruke [Claude Code](https://code.claude.com/docs), Anthropics agent-verktøy for terminalen.

Guiden er skrevet på norsk, forutsetter ingen forkunnskaper om AI-verktøy, og er bevisst holdt **smal**: du får ett anbefalt startsett i stedet for en katalog over alt som finnes. Når du har fått noe til å fungere, er det lett å utvide selv.

## Hvem er dette for?

- Du er utvikler, IT-drift eller hobbyist og har hørt om Claude Code, men ikke prøvd det.
- Du har installert det, men bruker det som en chat og aner ikke hva `CLAUDE.md`, skills eller MCP er.
- Du skal hjelpe en kollega i gang og vil ha noe å sende dem.

Er du allerede varm i trøya, er `03` og `05` sannsynligvis de eneste kapitlene som gir deg noe nytt.

## Innhold

| Kapittel | Handler om |
|---|---|
| [01 — Oppsett](01-oppsett.md) | Installere CLI-en, logge inn, kjøre `claude` for første gang |
| [02 — Daglig bruk](02-cli-bruk.md) | Naturlig språk vs. slash-kommandoer, permission modes, hvordan avbryte og rette kurs |
| [03 — CLAUDE.md-mal](03-claude-md-mal.md) | Ferdig mal du kan kopiere, med forklaring på hvorfor hvert felt finnes |
| [04 — Skills og MCP](04-skills-og-mcp.md) | Anbefalt startsett: tre skills og fire MCP-servere |
| [05 — Vanlige fallgruver](05-vanlige-fallgruver.md) | Det nybegynnere faktisk snubler i |

## Den korteste versjonen

Hvis du bare vil i gang og lese resten siden:

```bash
# 1. Installer (Windows PowerShell)
irm https://claude.ai/install.ps1 | iex

# 1. Installer (macOS / Linux / WSL)
curl -fsSL https://claude.ai/install.sh | bash

# 2. Gå til et prosjekt du allerede har, og start
cd sti/til/prosjektet
claude

# 3. Første gang: logg inn i nettleseren når den åpner seg
# 4. Første kommando inne i Claude Code:
/init
```

`/init` leser gjennom kodebasen din og skriver en `CLAUDE.md` — prosjektets bruksanvisning til Claude. Det er det enkeltgrepet som gir størst forskjell på kvaliteten av svarene du får.

## Et ord om tillit

Claude Code kan lese, skrive og slette filer og kjøre kommandoer på maskinen din. Det er hele poenget — og hele risikoen. Guiden tar derfor sikkerhet på alvor underveis, ikke som et vedlegg til slutt. Særlig [kapittel 05](05-vanlige-fallgruver.md) er verdt å lese før du skrur av spørsmålene.

## Versjon og gyldighet

Skrevet mot Claude Code **v2.1.x** (juli 2026). Claude Code oppdateres ofte. Er du i tvil om noe her fortsatt stemmer, er den offisielle dokumentasjonen fasit:

- Dokumentasjon: <https://code.claude.com/docs>
- Kommandoreferanse: <https://code.claude.com/docs/en/commands>

Inne i verktøyet får du alltid din egen versjons sannhet med:

```text
/help
/status
```

## Bidra

Finner du noe som er feil eller utdatert — åpne en issue eller en PR. Konkrete rettelser med lenke til dokumentasjonen er de letteste å slå sammen.

## Lisens

[MIT](LICENSE). Bruk fritt, også internt på jobb.
