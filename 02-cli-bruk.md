# 02 — Daglig bruk

Mål for dette kapittelet: du forstår hvordan du snakker med Claude Code, hvordan du styrer hvor mye den får lov til uten å spørre, og hvordan du retter kurs når den er på villspor.

## To måter å si ting på

**Naturlig språk** er hovedgrensesnittet. Du skriver hva du vil ha, på norsk eller engelsk:

```text
Legg til validering av e-postadresse i registreringsskjemaet
```

```text
Hvorfor feiler testen i user.test.ts?
```

```text
Denne funksjonen er blitt for lang. Del den opp.
```

Du trenger ikke fortelle hvilke filer den skal se på — den leter selv. Du trenger heller ikke være høflig eller formell. Vær derimot **spesifikk**: «gjør dette bedre» gir deg noens tolkning av «bedre», mens «denne funksjonen gjør tre ting, del den i tre» gir deg det du ba om.

**Slash-kommandoer** er innebygde funksjoner. De begynner med `/` og kjøres av verktøyet, ikke av modellen:

```text
/help
```

Skriv `/` alene for å se alle kommandoene i akkurat din versjon. Det er en bedre kilde enn noen guide, inkludert denne.

## Kommandoene du faktisk trenger

De fleste kan du ignorere i starten. Disse ni holder lenge:

| Kommando | Hva den gjør |
|---|---|
| `/help` | Oversikt over alt som finnes |
| `/init` | Lager eller forbedrer `CLAUDE.md` for prosjektet |
| `/clear` | Blank ark — ny samtale, glemmer alt så langt |
| `/context` | Viser hvor mye av kontekstvinduet som er brukt |
| `/compact` | Komprimerer lang samtale til et sammendrag, frigjør plass |
| `/memory` | Åpner `CLAUDE.md`-filene for redigering |
| `/permissions` | Se og endre hva Claude får gjøre uten å spørre |
| `/mcp` | Status på MCP-servere, og innlogging til dem |
| `/status` | Modell, innlogging, versjon, forbruk |

To vaner som er verdt å ta tidlig:

**Bruk `/clear` ofte.** Når du går fra én oppgave til en helt annen, start friskt. En samtale som drar med seg feilsøkingen fra i formiddag inn i en ny funksjon gir dårligere svar, ikke bedre — gammel kontekst forvirrer like mye som den hjelper.

**Ikke la konteksten renne over i stillhet.** Sjekk `/context` når økten har vart en stund. Nærmer du deg taket, kjør `/compact` (behold sammendraget) eller `/clear` (start på nytt). Det er billigere å gjøre det bevisst enn å oppdage at den har glemt noe viktig.

## Permission modes — det viktigste avsnittet

Claude Code stopper og spør før den redigerer filer eller kjører kommandoer. *Hvor ofte* den stopper, styres av hvilken modus du er i.

Trykk **Shift+Tab** for å bla gjennom modusene. Statuslinja nederst viser hvilken som er aktiv.

| Modus | Kjører uten å spørre | Bruk når |
|---|---|---|
| **Manual** (`default`) | Kun lesing | Du er ny, eller jobber med noe sensitivt |
| **acceptEdits** | Lesing + filredigering + `mkdir`/`mv`/`cp`/`rm` m.fl. | Du itererer på kode du uansett leser gjennom etterpå |
| **plan** | Kun lesing, og *foreslår* i stedet for å endre | Du vil forstå før du endrer |
| **auto** | Det meste, med en sikkerhetsklassifikator i bakgrunnen | Lange oppgaver du stoler på retningen i |
| **dontAsk** | Kun forhåndsgodkjente verktøy, avviser resten | CI og skript |
| **bypassPermissions** | Alt, uten sjekk | **Kun** isolerte containere og VM-er |

Du kan også starte i en gitt modus:

```bash
claude --permission-mode plan
```

Eller sette en fast standard i `~/.claude/settings.json`:

```json
{
  "permissions": {
    "defaultMode": "acceptEdits"
  }
}
```

### Anbefalt vei gjennom modusene

Ikke start på toppen av lista. En rimelig progresjon:

1. **Begynn i Manual.** Ja, det er mange spørsmål. Poenget er at du ser hva den faktisk har tenkt å gjøre, i en fase der du ennå ikke har kalibrert tilliten.
2. **Gå til acceptEdits** når du merker at du klikker «ja» uten å lese. Da er du reelt sett ikke lenger i Manual — du later bare som. Filredigering er lett å angre med `git diff` og `git checkout`; kommandoer er ikke.
3. **Bruk plan for alt du ikke forstår.** Se under.
4. **Vurder auto** når du har jobbet en stund og vet hva du kan forvente.

`bypassPermissions` hører hjemme i en container. Ikke på maskinen med SSH-nøklene dine. Anthropic er tydelige på dette selv, og modusen gir null beskyttelse mot prompt injection — se [kapittel 05](05-vanlige-fallgruver.md).

### Plan mode fortjener egen omtale

Plan mode er den mest undervurderte funksjonen i Claude Code. Claude leser, undersøker og **skriver en plan** — men rører ikke koden før du har godkjent.

```text
/plan
```

eller Shift+Tab til statuslinja sier `⏸ plan mode on`.

Bruk den når:

- Du er ny i en kodebase og vil vite hva en endring innebærer før den skjer.
- Oppgaven er stor nok til at feil retning er dyrt.
- Du er usikker på om du har formulert deg klart nok.

Når planen er klar, får du valget mellom å godkjenne, la den fortsette å planlegge, eller redigere planen selv (`Ctrl+G` åpner den i editoren din). Å lese en plan tar tretti sekunder. Å rydde opp etter feil retning tar lengre tid enn som så.

### Beskyttede stier

Uansett modus — med unntak av `bypassPermissions` — blir skriving til visse stier aldri automatisk godkjent:

`.git/`, `.claude/`, `.vscode/`, `.idea/`, `.mcp.json`, `.bashrc`, `.zshrc`, `.npmrc`, `.envrc` og en del andre.

Logikken er at dette er filer som styrer *hvordan verktøy og skall oppfører seg*. Får en agent skrive fritt i `.bashrc` eller `.claude/settings.json`, kan den i praksis endre sine egne rammer. Vernet ligger under `permissions.allow` — du kan ikke skru det av med en allow-regel, bare med `bypassPermissions`.

## Å rette kurs

Dette er ferdigheten som skiller folk som får mye ut av Claude Code fra folk som gir opp.

**Avbryt med Esc.** Ser du at den er på vei feil, ikke vent til den er ferdig. Trykk Esc, forklar hva som var galt, og la den fortsette derfra. Konteksten er intakt.

**Vær konkret om hva som var feil.** «Nei» hjelper lite. «Nei — bruk den eksisterende `HttpClient` i stedet for å lage en ny» gjør at neste forsøk treffer.

**Be om å få se før du godtar.** Fungerer i alle moduser:

```text
Vis meg diffen før du gjør noe mer
```

**Rull tilbake med git, ikke med ord.** Har den rotet det til over flere filer, er `git checkout -- .` raskere og sikrere enn å be den om å angre. Dette er en av grunnene til at hyppige commits betaler seg — se [kapittel 05](05-vanlige-fallgruver.md).

**Send en melding mens den jobber.** Du kan skrive mens den holder på; meldingen plukkes opp underveis. Nyttig når du kommer på et forbehold du glemte.

## Å hente tilbake en økt

```bash
claude -c    # fortsett siste økt i denne mappa
claude -r    # velg fra lista over tidligere økter
```

Hele samtalen er der. Du kan gå fra jobb midt i noe og fortsette dagen etter.

## Hva den ikke er god på

Ærlighet sparer deg for skuffelse:

- **Den kan ikke se skjermen din.** «Knappen ser rar ut» er ikke nok. Beskriv, eller koble på Playwright ([kapittel 04](04-skills-og-mcp.md)) så den kan se selv.
- **Den vet ikke hva du ikke har sagt.** Forretningsregler, avtaler fra møter, at kunden hater modaler — ingenting av det står i koden.
- **Den er trygg på seg selv også når den tar feil.** «Ferdig» betyr «jeg gjorde noe», ikke «jeg verifiserte at det virker». Krev bevis: kjørte testen? Hva sa den?

Det siste punktet er alvorlig nok til at det har eget avsnitt i [kapittel 05](05-vanlige-fallgruver.md).

---

**Neste:** [03 — CLAUDE.md-malen](03-claude-md-mal.md)
