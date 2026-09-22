# Vårt gemensamma utvecklingsarbetssätt

## Prompten bakom dokumentet

> Hur skulle du beskriva hur vårat gemensamma arbetssätt har utvecklats och vilka principer vi följer. ta med min prompt i svaret, och dokumentera i en md fil

## Sammanfattning

Vårt arbetssätt har utvecklats från en serie enskilda koduppgifter till en tydlig, fasindelad ingenjörsmetod. Varje förändring börjar med att vi fastställer systemgränsen, den auktoritativa baslinjen och vilka identiteter som måste bevaras. Först därefter genomförs den minsta ändring som uppfyller målet. Resultatet valideras mot uttryckliga kriterier och arbetet stannar vid den godkända fasgränsen.

Metoden prioriterar reproducerbarhet framför bekvämlighet, observerade fakta framför antaganden och bevarad funktion framför oombedd förbättring.

## Hur arbetssättet har utvecklats

### Från funktion till kontrakt

I början låg fokus främst på om en funktion fungerade. Med tiden flyttades fokus till hela kontraktet runt funktionen:

- vilka indata som är auktoritativa;
- vilken representation som används;
- vilka artefakter som produceras;
- vilka identiteter och manifest som binder ihop stegen;
- vilka delar som får respektive inte får förändras;
- hur samma resultat kan återskapas och verifieras.

En lyckad körning är därför inte tillräcklig. Vi kräver att körningen använder rätt källa, rätt kontrakt och rätt artefaktkedja.

### Från stora lösningar till avgränsade faser

Arbetet delas nu upp i små faser med ett uttalat stoppvillkor. Analys, design, artefaktbyggande, runtime-integration och produktionsdriftsättning behandlas som skilda aktiviteter.

```text
Inventering
    ↓
Design
    ↓
Minimal implementation
    ↓
Lokal validering
    ↓
Regression
    ↓
Godkännande
    ↓
Nästa fas
```

En fas ger inte automatiskt tillstånd att påbörja nästa. Exempelvis innebär en validerad artefakt inte att runtime ska aktiveras, och en lokalt godkänd runtime innebär inte att produktion får uppdateras.

### Från sökvägar till innehållsidentitet

Vi har etablerat att en validerad artefakts identitet hör till dess innehåll, inte till dess historiska sökväg. Filer får organiseras om utan att betraktas som regenererade när deras bytes och SHA-256 förblir oförändrade.

Samtidigt skiljer vi strikt mellan olika slags identitet:

```text
Källdata ──SHA-256──► korpusidentitet

Korpusidentitet + kontrakt + implementation
                         └──► modell- eller artefaktidentitet

Fråga + kandidater ─────────► körningsdata
```

Frågor, kandidater och annan anropsspecifik metadata får inte påverka korpus- eller modellidentitet.

## Våra grundprinciper

### 1. Auktoritativ källa först

Varje uppgift ska ange vilken implementation, datakälla eller artefakt som är source of truth. Jag inspekterar den källan innan jag föreslår eller gör förändringar. Historiska projekt används inte som tyst fallback när en ny validerad baslinje finns.

### 2. Design före implementation när arkitekturen är oklar

När ett beslut kan påverka kontrakt, dataflöde eller flera komponenter börjar vi med analys och design. Designuppgifter producerar inga kodändringar. Implementation börjar först när arkitekturen är tillräckligt bestämd.

### 3. Minsta möjliga ändring

Vi ändrar endast det som krävs för den aktuella fasen. Vi undviker oombedd refaktorering, nya abstraktioner, alternativa ramverk och optimering som inte behövs för acceptanskriterierna.

Minimal ändring betyder inte minimal kontroll. Små ändringar kan fortfarande kräva stark validering när de berör identiteter, säkerhet eller publika kontrakt.

### 4. Återanvänd validerad logik

Matematik, normalisering, matchning och andra godkända algoritmer återanvänds i stället för att skrivas om. Ny kod ska i första hand orkestrera, validera eller serialisera runt den frysta kärnan.

```text
Validerad kärna
      ↓
Tunn adapter eller byggare
      ↓
Nytt avgränsat användningsfall
```

### 5. Separera ansvar och representationer

Olika systemlager hålls självständiga. Retrieval, representation, evidence, semantiska profiler, artefaktbyggande och API-presentation får inte glida ihop bara för att de förekommer i samma demonstration.

Samma princip gäller mellan offline och runtime:

```text
Offline
Korpus → deterministisk byggare → validerad artefakt

Runtime
Validerad artefakt → deterministisk analys → svar
```

Runtime tränar inte, bygger inte om artefakter och gissar inte saknad information.

### 6. Determinism är ett testbart kontrakt

Determinism uttrycks som konkreta regler:

- stabil indatarordning;
- explicit sorteringsordning;
- fast serialisering;
- inga tidsstämplar, UUID:er eller slumpvärden i byggresultat;
- definierad tie-breaking;
- identiska indata ger byte-identiska utdata.

Vi verifierar determinism genom två oberoende byggen och jämför resultatens bytes och SHA-256.

### 7. Fail closed

Om en obligatorisk identitet, mapping, kolumn, manifestrelation eller artefakt saknas ska processen stoppa med ett konkret fel. Den får inte fortsätta med uppskattningar, automatiska ersättningar eller tysta standardvärden.

`unknown` är ett giltigt och ofta korrekt resultat. Det är bättre än en påhittad etikett.

### 8. Bevara original och lineage

Källdata ändras aldrig i stället. Nya representationer skapas som nya artefakter. Ursprungliga ID:n, texter, rader och ordning bevaras när uppgiften kräver det. Tillåtna transformationer, exempelvis maskning av hemligheter, dokumenteras exakt.

Lineage ska kunna följas:

```text
Källa
  ↓ SHA-256
Transformationskontrakt
  ↓ implementationsidentitet
Artefakt
  ↓ SHA-256
Runtime eller nästa byggsteg
```

### 9. Säkerhet utan onödig infrastruktur

Säkerhetsgränser ska vara enkla, centrala och testbara. Exempel är storleksgränser, radgränser, HTTPS-policy, utvecklingsflaggor, schemaanalys, SHA-256-verifiering och deterministisk maskning.

Utvecklingsundantag ska vara uttryckliga och får inte försvaga produktionsgränsen. Cache, lokal filsökväg eller större lokala filer aktiveras genom tydlig konfiguration, inte genom dold speciallogik.

### 10. Produktion är en separat auktoritetsgräns

Lokal implementation, paketering och produktionsdriftsättning är olika beslut. Jag får testa och förbereda inom angiven lokal omfattning, men driftsätter inte utan uttryckligt godkännande.

## Standard för kvalitetssäkring

Valideringen anpassas efter risken men följer en återkommande struktur.

### Identitet

- SHA-256 för källor och genererade artefakter;
- verifierad korpus-, kontrakts- och implementationsidentitet;
- oförändrade ID:n och stabil radordning;
- korrekt lineage mellan artefakter.

### Struktur

- obligatoriska filer, kolumner och fält finns;
- JSON, CSV och manifest kan läsas;
- antal rader och dokument stämmer;
- binära fält innehåller endast `0` och `1`;
- vektorlängder och registerordning stämmer.

### Beteende

- relevanta endpoints och startsekvenser körs;
- förväntade HTTP-statusar verifieras;
- fail-closed-fall testas, inte bara lyckade fall;
- publika schema och tidigare fält bevaras;
- classic och nya interna representationer ger samma offentliga kontrakt när detta krävs.

### Regression

- frysta baslinjer jämförs byte-för-byte eller genom kanonisk JSON;
- andra deployments kontrolleras när en avgränsad deployment ändras;
- algoritmer får inte justeras för att förbättra ett valideringsresultat;
- verkliga avvikelser rapporteras utan att döljas som “förväntade”.

### Reproducerbarhet

- byggkommandon och effektiva kontrakt dokumenteras;
- två oberoende körningar jämförs;
- rapporter skiljer observationer från rekommendationer;
- inga produktionsartefakter regenereras om uppgiften endast gäller analys.

## Hur instruktionerna påverkar mitt beteende

Dina instruktioner har etablerat ett antal praktiska beteenden för mig som agent:

1. Jag börjar med att inspektera den faktiska implementationen och dess data, inte med att anta hur den borde fungera.
2. Jag skiljer på diagnos, design, implementation, validering och deployment.
3. Jag håller angivna filer och projekt read-only när uppgiften kräver det.
4. Jag återanvänder befintlig validerad kod och undviker parallella implementationer.
5. Jag rapporterar exakta blockerare och filnamn i stället för att gissa en lösning.
6. Jag genomför säkra kontroller själv och frågar endast när ett val skulle förändra omfattning, kontrakt eller extern state.
7. Jag lämnar korta arbetsuppdateringar under längre körningar.
8. Jag avslutar vid uppgiftens stoppvillkor även när nästa tekniska steg är uppenbart.
9. Jag dokumenterar både vad som ändrades och vad som uttryckligen lämnades oförändrat.
10. Jag betraktar valideringsrapporter, manifest och identiteter som en del av implementationen, inte som efterhandsdokumentation.

## Den input som förbättrar vårt samarbete mest

Vårt arbete blir snabbast och säkrast när en uppgift innehåller följande delar.

### Mål

Beskriv det enda resultat som fasen ska uppnå. Om målet är analys bör det framgå att inga filer ska ändras. Om målet är implementation bör den önskade slutstatusen vara testbar.

### Auktoritativ baslinje

Ange projekt, fil, artefakt eller tidigare validering som är source of truth. Beskriv om den ska behandlas som fryst, byte-identisk eller endast funktionellt vägledande.

### Tillåten omfattning

Lista vilka filer, mappar och miljöer som får ändras. Detta är särskilt viktigt när lokalt, releasepaket och produktion existerar samtidigt.

### Förbjudna förändringar

Skriv ut vad som inte får ändras, exempelvis algoritm, API-kontrakt, runtime, retrieval, manifest eller produktionsmiljö. Negativ omfattning förhindrar att en korrekt lokal förbättring blir en arkitektonisk regression.

### Identitetsregler

Definiera vilken identitet som är auktoritativ, hur den beräknas och vilka fält som aldrig får påverka den. Ange även om sökvägar får ändras medan filinnehåll måste förbli identiskt.

### In- och utdata

Ange exakta format, obligatoriska fält, filnamn och exempel. För stora dataset är även storleksgränser, radgränser och cachepolicy viktiga.

### Acceptanskriterier

Formulera verifierbara kontroller, exempelvis:

- samma radantal och ordning;
- byte-identisk ombyggnad;
- HTTP 200 för angivna endpoints;
- ingen ändring av frysta svarsfält;
- en bestämd fail-closed-reaktion;
- oförändrade SHA-256 för källartefakter.

### Stoppvillkor

Ange var uppgiften slutar. Ett tydligt stoppvillkor hindrar att analys övergår i implementation eller att en lokal validering övergår i deployment.

## Rekommenderad uppgiftsmall

```markdown
# Objective

Ett konkret och testbart resultat.

# Authoritative baseline

Projekt, filer och artefakter som är source of truth.

# Scope

Filer, miljöer och funktioner som får ändras.

# Required work

Den minsta ordnade listan över nödvändiga steg.

# Constraints

Algoritmer, kontrakt, data och miljöer som inte får ändras.

# Identity contract

Auktoritativa ID:n, hashregler, lineage och ordningskrav.

# Validation

Exakta positiva, negativa och deterministiska kontroller.

# Deliverables

Filnamn, rapporter och förväntat slutresultat.

# Stop condition

Den punkt där arbetet ska avslutas.
```

## Gemensam arbetsfördelning

Du definierar målet, arkitekturgränsen, den godkända baslinjen och vilka risker som är viktigast. Jag omvandlar detta till konkreta kontroller, inspekterar faktisk state, genomför den minsta tillåtna förändringen och producerar verifierbara resultat.

När underlaget är ofullständigt gör jag säkra, lokala och reversibla antaganden. Om ett antagande skulle ändra produktbeteende, publik kontrakt, dataintegritet eller produktion stannar jag och begär riktning.

Detta ger ett samarbete där kod inte bara “fungerar”, utan där vi kan visa varför rätt kod kördes mot rätt data under rätt kontrakt.
