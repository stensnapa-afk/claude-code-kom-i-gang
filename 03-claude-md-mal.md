# 03 — CLAUDE.md-malen

`CLAUDE.md` er prosjektets bruksanvisning til Claude. Den lastes automatisk inn ved starten av hver økt i mappa, uten at du trenger å nevne den.

Dette kapittelet gir deg en ferdig mal du kan kopiere, og — viktigere — forklarer hvorfor hvert felt finnes. Målet er at du skal kunne stryke det du ikke trenger med god samvittighet.

## Hvorfor bry seg?

Uten `CLAUDE.md` gjetter Claude. Den gjetter riktig overraskende ofte, men den gjetter på nytt hver eneste økt, og den gjetter feil på akkurat det som er spesielt ved ditt prosjekt: at testene kjøres med en uvanlig kommando, at `legacy/`-mappa ikke skal røres, at dere har byttet fra én pakkebehandler til en annen men ikke ryddet ferdig.

En `CLAUDE.md` er stedet du skriver ned det én gang.

## Hvor fila kan ligge

Claude Code leser flere filer og **slår dem sammen** — de overstyrer ikke hverandre. Filer nærmere arbeidsmappa lastes sist og veier tyngst ved motstrid.

| Plassering | Gjelder for | Deles via git? |
|---|---|---|
| `~/.claude/CLAUDE.md` | Alle prosjektene dine | Nei — kun deg |
| `./CLAUDE.md` eller `./.claude/CLAUDE.md` | Dette prosjektet | **Ja** — dette er den viktigste |
| `./CLAUDE.local.md` | Dette prosjektet, kun deg | Nei — legg i `.gitignore` |
| `./src/CLAUDE.md` | Undermappa, lastes ved behov | Ja |

Tommelfingerregel:

- **Prosjektfakta** (hvordan bygge, hvilke konvensjoner) → `./CLAUDE.md`, committes.
- **Dine personlige preferanser** (hvordan du liker at Claude formulerer seg) → `~/.claude/CLAUDE.md`.
- **Ditt lokale miljø** (at databasen din kjører på en rar port) → `CLAUDE.local.md`, ikke committes.

Blander du disse, ender du med å tvinge dine private vaner på kollegene dine gjennom git.

## Malen

Kopier dette til `CLAUDE.md` i prosjektrota og fyll ut. Alt er valgfritt — stryk seksjoner som ikke gir mening hos deg.

````markdown
# <Prosjektnavn>

## Hva dette er

<To–tre setninger. Hva løser prosjektet, for hvem? Nevn de viktigste
teknologivalgene: språk, rammeverk, database, hvor det kjører.>

## Kom i gang

```bash
<installer avhengigheter>
<start utviklingsserver>
```

## Kommandoer som gjelder

| Oppgave | Kommando |
|---|---|
| Installere | `...` |
| Kjøre lokalt | `...` |
| Bygge | `...` |
| Teste | `...` |
| Linte / formatere | `...` |

## Struktur

<Kun det som ikke er åpenbart. Ikke gjenta hele filtreet — Claude kan
lese det selv. Skriv det den *ikke* kan se: hva som hører sammen,
hva som er dødt, hvor grensene går.>

- `src/...` — <hva som bor her>
- `legacy/` — <gammelt, skal ikke utvides>

## Konvensjoner

<Vær konkret. "Formater pent" hjelper ingen. "Innrykk med 2 mellomrom"
kan følges.>

- <Navngiving: f.eks. filnavn i kebab-case, komponenter i PascalCase>
- <Feilhåndtering: f.eks. kast typede feil, aldri returner null ved feil>
- <Tester: f.eks. ligger ved siden av koden som `*.test.ts`>
- <Avhengigheter: f.eks. spør før nye pakker legges til>

## Spør alltid først om

<Den viktigste seksjonen. Se forklaring under.>

- Endringer i databaseskjema eller migrasjoner
- Alt som rører autentisering, tilgangsstyring eller hemmeligheter
- Å legge til nye avhengigheter
- `git push`, tagging eller deploy
- Endringer i CI-konfigurasjon

## Ikke gjør

- Ikke commit hemmeligheter, `.env`-filer eller nøkler
- Ikke rediger genererte filer (<list dem: f.eks. `dist/`, `*.generated.ts`>)
- Ikke oppgrader major-versjoner uten at det er avtalt

## Ferdig betyr

<Hva som må være sant før noe kan kalles ferdig hos dere.>

- Bygget går grønt
- Testene kjører
- Endringen er faktisk verifisert, ikke bare antatt riktig
````

## Hvorfor hvert felt finnes

### «Hva dette er»

Claude leser koden, men koden sier ikke hvem den er *for*. Et endepunkt som returnerer brukerdata ser likt ut enten det er et internt admin-verktøy eller en offentlig API — men riktig svar på «bør dette ha rate limiting?» er helt forskjellig. Konteksten flytter kvaliteten på vurderingene, ikke bare på faktaene.

### «Kommandoer som gjelder»

Den vanligste kilden til bortkastet tid. Claude ser en `package.json` og antar `npm test`. Dere bruker `pnpm test:unit` fordi `npm test` starter en integrasjonssuite som trenger en database ingen har kjørende. Uten dette feltet lærer den det på nytt, ved å feile, hver eneste økt.

Skriv den kommandoen som faktisk fungerer på en fersk maskin.

### «Struktur»

Fristelsen er å lime inn hele filtreet. Ikke gjør det — det er den ene tingen Claude trivielt finner ut selv, og det spiser kontekst.

Skriv i stedet det som ikke er synlig i strukturen: at `utils/` egentlig er to ting som burde vært delt, at `api/v1/` er frosset, at mappa som ser midlertidig ut har vært i produksjon i tre år.

### «Konvensjoner»

Presisjon slår høflighet. Sammenlign:

> Skriv ren og lesbar kode.

Dette er umulig å bryte og derfor umulig å følge. Mot:

> Funksjoner over 40 linjer skal deles opp. Ingen default-eksporter. Feil kastes, aldri returneres.

Det andre kan sjekkes. Det første er dekorasjon.

### «Spør alltid først om»

Dette er seksjonen folk hopper over, og den som betyr mest.

Claude Code har et rettighetssystem som spør om tillatelse per verktøy — men det er teknisk. Det vet at `Bash` er `Bash`, ikke at akkurat *denne* migrasjonen er den som ikke kan rulles tilbake. Denne lista er der du oversetter fra teknisk risiko til *din* risiko.

Skriv ned det som er dyrt å angre på hos dere. Er du usikker på hva som hører hjemme her, still spørsmålet: hva ville jeg blitt lei meg for om noen gjorde uten å spørre?

Merk at Claude Code i tillegg beskytter enkelte stier uansett hva som står i `CLAUDE.md` — `.git/`, `.claude/`, `.env*` og lignende. `CLAUDE.md` utvider vernet; den er ikke det eneste laget.

### «Ikke gjør»

Forskjellen fra seksjonen over: «spør først» er ting som *kan* gjøres etter godkjenning. «Ikke gjør» er ting som aldri skal skje. Å blande dem svekker begge.

### «Ferdig betyr»

Uten dette betyr «ferdig» at koden er skrevet. Med det betyr det at den virker. Det er en stor forskjell, og det er billigere å definere den én gang her enn å oppdage den i produksjon.

Vær ærlig om hva dere faktisk krever. Skriver du «100 % testdekning» og ingen mener det, lærer du bare Claude at lista er pynt.

## Praktiske råd

**Hold den under 200 linjer.** Fila lastes i sin helhet ved hver økt og spiser av kontekstvinduet. Vokser den forbi det, er det som regel fordi en prosedyre har sneket seg inn — «slik gjør du en release, steg for steg». Prosedyrer hører hjemme i en *skill* i stedet, som bare lastes når den brukes. Se [kapittel 04](04-skills-og-mcp.md).

**Del opp med `@`-import.** Blir fila stor, kan du dele den:

```markdown
# Kodestandard
@docs/kodestandard.md
```

Stien løses relativt til fila som inneholder `@`.

**Rydd bort motsigelser.** To regler som sier motsatt ting gjør ikke Claude forsiktig — den gjør den vilkårlig. Når du legger til en regel, sjekk om den motsier en gammel.

**Behandle den som kode.** Den skal i git, gjennomgås i PR-er, og oppdateres når prosjektet endrer seg. En `CLAUDE.md` som beskriver fjorårets arkitektur er verre enn ingen, fordi den blir trodd.

**La den vokse fra feil.** Den beste måten å skrive den på er ikke på forhånd. Hver gang Claude gjør noe du måtte rette — spør deg om rettelsen hører hjemme i `CLAUDE.md`. Etter to uker har du en fil som er verdt noe.

## Oppdatere den underveis

Inne i en økt:

```text
/memory
```

Åpner `CLAUDE.md`-filene for redigering. Du kan også bare be om det med ord — «legg til i CLAUDE.md at vi bruker pnpm, ikke npm» — men les alltid over hva som ble skrevet.

---

**Neste:** [04 — Skills og MCP](04-skills-og-mcp.md)
