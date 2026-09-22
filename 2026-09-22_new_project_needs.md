# Nytt projekt: vad vi behöver fastställa, dokumentera och validera

**Snapshot:** 2026-09-22  
**Status:** Beskrivning av Johan och coding agentens nuvarande gemensamma arbetssätt  
**Giltighet:** Detta är en situationsbild av hur vi arbetar tillsammans idag. Det är inte en universell metod, en standard för andra projekt eller ett påstående om hur andra coding agents arbetar.

## 1. Utgångspunkt

När vi startar ett nytt projekt börjar vi inte med kod. Vi börjar med att avgöra vad projektet faktiskt är, vem som har beslutanderätt, vilka tillgångar som redan är auktoritativa och vilka egenskaper som inte får förändras.

Vårt arbetssätt bygger på en tydlig uppdelning:

- Johan är **Project Owner** och ansvarar för syfte, forskningsriktning, teori, prioriteringar, godkända gränser och slutliga tekniska beslut.
- Coding agenten ansvarar för **inspection, spårbar implementation, validering, dokumentation och tydlig rapportering av det som faktiskt observerats**.
- Arkitektur och accepterade beslut styr implementationen. Kod får inte tyst skapa en ny arkitektur.
- Ett påstående får inte vara större än evidensen som stödjer det.
- När authority eller nödvändig evidens saknas ska resultatet vara `UNKNOWN` eller `STOP`, inte en rimlig gissning.

Målet med projektstarten är därför att skapa en tillräckligt tydlig och reproducerbar grund för att en första implementation ska kunna genomföras utan att identiteter, kontrakt eller scope behöver uppfinnas under arbetets gång.

---

## 2. Tre kunskapsklasser

All information vid projektstart ska behandlas som en av tre klasser.

| Klass | Betydelse | Hantering |
|---|---|---|
| **Måste vara känt före implementation** | Beslut som endast Project Owner eller en godkänd authority kan fatta | Dokumenteras och fryses innan kod skrivs |
| **Kan upptäckas genom repository inspection** | Observerbara tekniska fakta i kod, data, tester, artefakter och deployment | Inspekteras, verifieras och rapporteras av coding agenten |
| **UNKNOWN / STOP** | Information som varken är beslutad eller säkert kan härledas | Markeras uttryckligen; implementation stoppas om uppgiften beror på svaret |

Denna uppdelning hindrar repository-fakta från att misstas för projektbeslut och hindrar projektönskemål från att beskrivas som redan implementerade kontrakt.

---

## 3. Vad Project Owner behöver definiera

Följande kan inte säkert uppfinnas genom kodinspektion. Det behöver definieras eller godkännas av Project Owner innan berörd implementation börjar.

### 3.1 Syfte och observerbart slutresultat

Project Owner behöver beskriva:

- vilket problem projektet ska undersöka eller lösa;
- vem som ska använda resultatet;
- om resultatet är forskning, experiment, demonstration, intern runtime eller publik tjänst;
- vilket konkret utfall som betyder att den första fasen är färdig;
- vad projektet uttryckligen **inte** ska göra.

Ett mål bör kunna omvandlas till observerbara acceptance criteria. Formuleringar som ”gör systemet bättre” är inte tillräckliga utan en definierad egenskap som kan mätas eller inspekteras.

### 3.2 Authority och source of truth

Project Owner behöver ange vilken källa som är auktoritativ när flera versioner finns. Det kan vara:

- ett särskilt repository och en commit;
- en namngiven databas;
- ett manifest med verifierad SHA-256;
- ett accepterat Architecture Decision-dokument;
- en specifik runtime;
- en fryst CSV, JSON, SQLite-fil eller modellartefakt;
- ett existerande API-kontrakt.

Om samma komponent förekommer i flera kataloger måste prioriteten definieras. ”Den nyaste filen” är inte i sig ett säkert authority-kontrakt.

### 3.3 Scope och tillåtna mutationer

Project Owner behöver fastställa:

- vilka mappar och filer som får ändras;
- vilka system som endast får läsas;
- om nya filer får skapas;
- om runtime, frontend, offline builders, tester, dokumentation eller deployment ingår;
- om uppgiften är analys, design, implementation, migration, paketering eller deployment;
- om externa tjänster eller nätverksanrop får användas;
- vilka angränsande förbättringar som är förbjudna även om de verkar praktiska.

Scope ska vara en teknisk skrivgräns, inte bara en ämnesbeskrivning.

### 3.4 Invariants

Invariants är egenskaper som måste vara oförändrade efter arbetet. Project Owner behöver ange dem när de är affärs-, forsknings- eller arkitekturbeslut.

Typiska invariants i vårt arbete är:

- dokument-ID får inte regenereras;
- frysta SHA-256-identiteter får inte ändras;
- kandidatordning eller Top-K får inte förändras;
- normalisering och representation får inte blandas;
- en runtime får inte börja träna eller bygga artefakter;
- publika endpoint-kontrakt får inte brytas;
- ett experiment får inte bli en produktionsdependency;
- historiska artefakter får inte skrivas om;
- en lokal utvecklingsregel får inte försvaga remote admission.

Varje invariant ska senare kopplas till en verifieringsmetod.

### 3.5 Identitetsmodell

Project Owner behöver godkänna vad som utgör identitet och vad som endast är metadata.

Det ska framgå:

- vilket fält som är primär identitet;
- vad identiteten är beräknad från;
- om identiteten avser rå källa, normaliserad text, representation, artefakt eller deployment;
- om publikt ID och artefakt-ID är samma sak;
- vilka identiteter som är externa kontrakt;
- hur lineage bevaras mellan källa, transformerad representation, artefakt och runtime;
- vilka hashvärden som får räknas om och vilka som ska bevaras exakt.

En hash är inte meningsfull utan en definierad bytekälla och kanonisk serialisering.

### 3.6 Data- och representationsgränser

Project Owner behöver definiera vilka representationer som hör till projektet och hur de får användas, exempelvis:

- rå text kontra normaliserad text;
- Human, Universal och Alien representation;
- etiketter, profiler, vektorer och binära närvaromönster;
- källdata kontra navigationsmetadata;
- publikt innehåll kontra restricted eller redacted material;
- operativ corpus kontra historiskt arkiv;
- offline-mellanlagring kontra runtimeartefakt.

Det behöver också vara klart om transformationen är reversibel, förlustbringande eller endast giltig inom en definierad kontraktsgräns.

### 3.7 Acceptance criteria

Project Owner behöver fastställa vad som måste observeras för `PASS`. Kriterierna bör omfatta:

- funktionellt resultat;
- skyddade identiteter;
- oförändrade kontrakt;
- tillåtna och förbjudna bieffekter;
- nödvändiga testfall;
- förväntat felbeteende;
- deployment- eller paketeringskrav;
- om oberoende eller byte-identisk rebuild krävs.

Acceptance criteria ska beslutas före implementation när de påverkar arkitekturen. Tester som skrivs efteråt får inte definiera om målet för att få implementationen att se korrekt ut.

### 3.8 Stop conditions

Project Owner bör i förväg ange händelser som ska avbryta arbetet, exempelvis:

- authority kan inte identifieras;
- en fryst SHA-256 avviker;
- implementationen kräver en icke godkänd API-förändring;
- en källa eller registrerad artefakt saknas;
- två påstått auktoritativa kontrakt motsäger varandra;
- en migration skulle kräva nya identiteter;
- scope måste utökas till runtime eller deployment;
- data behöver uppfinnas eller rekonstrueras utan deterministisk grund.

Ett korrekt `STOP` är ett valideringsresultat, inte ett misslyckande att leverera.

---

## 4. Vad coding agenten behöver inspektera

När målet och authority-gränserna är givna ska coding agenten inspektera den faktiska arbetsytan innan implementation.

### 4.1 Repository och arbetsyta

Agenten behöver verifiera:

- faktisk project root;
- repository-status och eventuell dirty worktree;
- lokala instruktioner som `AGENTS.md`, skills eller projektspecifika regler;
- branch, commit och tagg när Git är en del av authority;
- vilka filer som redan är modifierade av användaren;
- om kopior, äldre runtimeversioner eller release candidates finns;
- vilka sökvägar som är utvecklingskällor respektive deploymentkopior.

Befintliga användarändringar ska bevaras. Avsaknad av Git ska rapporteras eftersom diff- och provenanceförmågan då blir begränsad.

### 4.2 Faktisk arkitektur

Agenten behöver kartlägga det som verkligen finns:

- entry points;
- modulgränser;
- builders kontra runtime;
- frontend och backend;
- endpoints och request/response-format;
- databas- och artefaktladdning;
- konfigurationskällor;
- teststruktur;
- deploymentstruktur;
- externa tjänster och miljövariabler;
- implicit filsystems- eller importberoende.

Dokumentation jämförs med implementation. Avvikelsen rapporteras; dokumentationen eller koden får inte tyst antas vara rätt.

### 4.3 Existerande identitetskedja

Agenten behöver spåra identiteter genom den faktiska koden:

```text
källa
  → transformation
  → registrerad representation
  → byggd artefakt
  → manifest
  → runtime
  → publikt resultat
```

För varje steg ska det vara möjligt att svara på:

- vilket ID som används;
- vilket innehåll ID:t binder;
- var hashkontrollen sker;
- om runtime verifierar eller bara vidarebefordrar identiteten;
- om flera poster får dela content identity;
- om deployment laddar exakt den artefakt som dokumentationen anger.

### 4.4 Data och representationer

Agenten behöver inspektera:

- schema, kolumner, datatyper och radantal;
- textkomposition och whitespace-policy;
- ID-kolumn och dupliceringsregler;
- encoding och serialisering;
- maximala storlekar och admission limits;
- relationen mellan rå, normaliserad och kodad text;
- om registrerad metadata räcker för deterministic replay;
- om en runtime läser källa eller endast frysta artefakter.

Små stickprov kan användas för orientering, men kontraktsverifiering ska baseras på hela relevanta artefakten när kravet gäller varje rad.

### 4.5 Tester och tidigare baselines

Agenten behöver identifiera:

- vilka tester som redan är auktoritativa;
- om testerna är unit-, integration-, golden-, regression- eller deploymenttester;
- vilka golden outputs och hashbaselines som finns;
- testernas Python-, Node- eller systemversion;
- om tester bygger om data eller endast läser frysta artefakter;
- om testmiljön har dolda beroenden;
- om det finns tidigare STOP-rapporter som ska bevaras som historisk evidens.

Tester ändras inte för att göra en felaktig implementation godkänd. Om kontraktet ändras med Project Owners godkännande läggs nya fokuserade tester till och den tidigare baselinen bevaras där det är relevant.

### 4.6 Deployment boundaries

Agenten behöver skilja mellan:

- utvecklingsrepository;
- offline buildermiljö;
- runtimepaket;
- frontendpaket;
- frysta artefakter;
- lokala experiment;
- publik deployment;
- interna tjänster.

Agenten ska verifiera:

- vilka filer deployment faktiskt konsumerar;
- var WSGI eller annan startup sker;
- vilka miljövariabler som krävs;
- filstorleks- och hostbegränsningar;
- om statiska resurser och API är same-origin eller cross-origin;
- om lokala absoluta paths har läckt in;
- om ett patchpaket kan innehålla enbart ändrade consumed runtime files.

En lokal fungerande implementation är inte en validerad deployment förrän den faktiska deploymentgränsen har kontrollerats.

---

## 5. Vad coding agenten behöver etablera före kod

Repository inspection producerar fakta. Innan implementation behöver agenten omvandla fakta och Project Owners beslut till ett genomförbart kontrakt.

### 5.1 En explicit source-of-truth-karta

Kartan bör ange:

| Område | Auktoritativ källa | Verifiering |
|---|---|---|
| Arkitektur | Godkänt beslut eller dokument | Besluts-ID/version |
| Källa/corpus | Exakt fil eller databas | SHA-256, schema, radantal |
| Identitet | Angiven kolumn/algoritm | Stickprov plus full audit där nödvändigt |
| Representation | Versionerad vokabulär/mapping | SHA-256 och kontraktsversion |
| Runtime | Exakt modul/entry point | Import- och startup-test |
| Publikt API | Endpointkontrakt | Request/response-test |
| Deployment | Namngiven release/paketstruktur | Paketinspektion och startup |

### 5.2 Scope ledger

En kort scope ledger ska lista:

- **Allowed writes**;
- **Read-only dependencies**;
- **Explicit exclusions**;
- **Tillåtna nya filer**;
- **Stop conditions**.

Det gör det möjligt att avgöra om en upptäckt förbättring hör till uppgiften eller ska rapporteras för en senare fas.

### 5.3 Invariant- och riskmatris

Varje skyddad egenskap ska kopplas till kontroll:

| Invariant | Kontroll före | Kontroll efter | Failure action |
|---|---|---|---|
| Fryst artefakt oförändrad | SHA-256 | SHA-256 | STOP |
| Endpointkontrakt oförändrat | Baseline request | Regression request | Rollback/STOP |
| Ingen runtime-build | Kod-/I/O-inspection | Runtime trace/test | STOP |
| Identiteter bevarade | Full ID-lista/hash | Full jämförelse | STOP |
| Deterministisk output | Build A | Build B | Investigate/STOP |

### 5.4 Implementationsordning och phase gates

Agenten ska beskriva minsta säkra ordning. Ett typiskt flöde är:

```text
Authority verifierad
  ↓
Baseline fryst
  ↓
Minsta implementation
  ↓
Fokuserad validering
  ↓
Golden/regression
  ↓
Deploymentgräns
  ↓
Ny validerad baseline
```

Om ett senare steg är beroende av ett tidigare ska det anges. Parallellt arbete används endast när deluppgifterna är oberoende och inte riskerar att skriva samma filer.

---

## 6. Fail-closed-beteende

Fail-closed betyder i vårt arbetssätt att systemet inte fortsätter med en antagen eller degraderad tolkning när ett bindande kontrakt inte kan verifieras.

Det ska tillämpas på exempelvis:

- saknad eller felhashad artefakt;
- okänd representationsversion;
- okända tokens när vokabulären är sluten;
- felaktigt schema eller radantal;
- identitetskonflikt;
- unsupported legacy-format;
- saknad acknowledgement eller terms version;
- otillåten local path eller remote source;
- oförenligt manifest;
- inkomplett lineage;
- payload som inte kan knytas till registrerad deployment.

Fail-closed får inte reduceras till ett generiskt fel som döljer rotorsaken. Publika fel kan vara återhållsamma, men interna loggar och valideringsrapporter ska skilja exempelvis identity failure, admission failure, artifact failure och contract failure.

Ingen fallback får införas utan ett uttryckligt kontrakt. ”Försök med en annan representation” är en beteendeförändring, inte bara robusthet.

---

## 7. Test- och valideringsmodell

Vi skiljer mellan flera sorters verifiering eftersom de svarar på olika frågor.

### 7.1 Source validation

Verifierar att rätt källa används:

- filidentitet;
- schema;
- radantal;
- ID-kolumn;
- encoding;
- metadata- och textkomposition.

### 7.2 Contract validation

Verifierar gränser:

- obligatoriska fält;
- tillåtna versioner;
- limit exakt vid och över gränsen;
- okänd identitet;
- fel representation;
- fail-closed-respons.

### 7.3 Golden-equivalence validation

Verifierar att en refaktorering eller extraktion bevarar observerbart beteende:

- samma IDs;
- samma ranking;
- samma score/overlap när dessa ingår i kontraktet;
- samma serialisering;
- samma response shape;
- samma frysta SHA-256.

### 7.4 Runtime validation

Verifierar att den riktiga runtimegränsen fungerar:

- startup;
- artifact loading;
- health;
- endpoints;
- inga dolda builders;
- read-only-beteende;
- fel vid saknad dependency.

### 7.5 Deployment validation

Verifierar paketet, inte bara arbetskopian:

- exakt innehåll i ZIP/tar;
- filstorlekar och SHA-256;
- inga lokala experiment eller stora oavsiktliga filer;
- korrekt target root;
- startup i hostmiljön;
- smoke tests efter upload;
- oförändrade befintliga endpoints.

### 7.6 Reproducibility validation

När determinism är ett krav ska två rena builds köras från samma inputs. Byte-identitet används där vi kontrollerar hela byggkedjan. För externa modellruntime eller andra icke-byte-stabila beroenden fryser och verifierar vi i stället den producerade artefakten och dokumenterar generationens provenance.

Vi kräver inte en starkare sorts reproducerbarhet än systemet faktiskt kan stödja.

---

## 8. Från initial idé till första validerade baseline

### Steg 1 — Idé och intention

Project Owner beskriver frågan, syftet, användaren och den önskade första demonstrerbara egenskapen. I detta skede är detaljer tillåtna att vara öppna, men de markeras som öppna.

### Steg 2 — Projektklassificering

Vi fastställer om arbetet är:

- analys;
- offline experiment;
- deterministic builder;
- runtime;
- frontend;
- API;
- deployment;
- migration;
- eller en kombination med separata faser.

Detta avgör vilka kontrakt som behöver frysas först.

### Steg 3 — Authority freeze

Project Owner namnger source of truth och godkänner identitetsmodell, scope, invariants och stop conditions. Saknas authority för en kritisk komponent blir resultatet `STOP` före kod.

### Steg 4 — Repository inspection

Coding agenten inventerar kod, data, artefakter, tester och deployment. Observerade fakta dokumenteras separat från rekommendationer. Konflikter återförs till Project Owner.

### Steg 5 — Baseline capture

Före mutation fångas relevanta baselines:

- fil-SHA-256;
- schema och radantal;
- endpointresultat;
- ranking eller golden output;
- testresultat;
- package inventory;
- runtime- och språkversioner.

Baseline behöver vara proportionerlig. Vi hashar inte hela världen, bara det som är skyddat eller nödvändigt för att styrka förändringen.

### Steg 6 — Kontrakts- och implementationsplan

Agenten beskriver:

- exakt vilka filer som ändras;
- dataflöde före och efter;
- vilka interfaces som bevaras;
- implementationsordning;
- testmatris;
- rollback- eller stopvillkor.

Vid arkitekturfas inväntas uttryckligt godkännande innan implementation.

### Steg 7 — Minsta koherenta implementation

Endast den minsta sammanhängande ändringen genomförs. Ingen adjacent cleanup, redesign eller migration inkluderas utan nytt scope.

### Steg 8 — Fokuserad validering

Det nya beteendet provas först med små, precisa tester inklusive boundary- och failure cases. Ett fel undersöks innan bred regression körs.

### Steg 9 — Regression och identity audit

Existerande relevanta tester körs. Skyddade hashes, IDs, rankings och kontrakt jämförs med baseline. Om ett skyddat värde ändrats utan godkännande blir resultatet `STOP`.

### Steg 10 — Freeze av första validerade baseline

När acceptance criteria är uppfyllda fryses:

- implementationens version eller commit;
- artefaktmanifest;
- testresultat;
- kända begränsningar;
- deploymentgräns;
- rapportens resultat `PASS`, `NEEDS REVIEW` eller `STOP`.

Först därefter blir implementationen bas för nästa fas.

---

## 9. Minimala dokument och artefakter

Ett litet projekt behöver inte många separata dokument. Följande information måste däremot finnas i någon auktoritativ form för att arbetet ska kunna fortsätta reproducerbart.

### 9.1 Project baseline

Ett dokument, exempelvis `PROJECT_BASELINE.md`, bör minst innehålla:

- objective;
- non-goals;
- Project Owner;
- source of truth;
- scope;
- invariants;
- identitetsmodell;
- representationskontrakt;
- acceptance criteria;
- stop conditions;
- första fasens status.

### 9.2 Source inventory

`SOURCE_INVENTORY.md` eller motsvarande bör ange:

- input paths;
- funktion och authority;
- schema;
- radantal eller filstorlek där relevant;
- SHA-256;
- om filen är mutable, frozen eller generated;
- licens eller åtkomstgräns när det är relevant.

### 9.3 Baseline manifest

Ett maskinläsbart manifest, exempelvis `baseline_manifest.json`, bör innehålla identiteter för de skyddade inputs och outputs som runtime eller rebuild är beroende av. Kanonisk JSON-serialisering ska definieras om manifestets egen hash används som kontrakt.

### 9.4 Deterministisk entry point

Det ska finnas ett dokumenterat kommando eller script för den operation projektet utför:

- build;
- validate;
- run;
- eller reproduce.

Kommandot ska ange nödvändig språkversion, dependencies och konfiguration. En notebook ensam är tillräcklig endast om dess execution order och miljö är låsta och validerade.

### 9.5 Tester och validation report

Minst ett fokuserat test ska skydda projektets centrala kontrakt. En kort `VALIDATION.md` ska ange:

- exakt vad som kördes;
- observerat resultat;
- hashes eller golden outputs;
- kända begränsningar;
- om resultatet är `PASS`, `NEEDS REVIEW` eller `STOP`.

### 9.6 Deployment boundary

Om projektet ska köras utanför utvecklingsmiljön behövs en minimal `DEPLOYMENT.md` eller release manifest med:

- consumed runtime files;
- artifact paths;
- miljövariabler;
- startup;
- health/smoke tests;
- filstorleksbegränsningar;
- rollback;
- vad som uttryckligen inte ska finnas i produktion.

### 9.7 Architecture decisions när beslut blir bindande

Ett separat decision log behövs när ett val påverkar framtida faser, exempelvis identitetsmodell, runtimegräns, representation eller API. Små lokala detaljer behöver inte göras till permanenta Architecture Decisions.

---

## 10. Responsibility matrix

| Fråga | Project Owner | Coding agent |
|---|---|---|
| Varför projektet finns | Definierar | Förtydligar konsekvenser |
| Forsknings- och produktgräns | Beslutar | Verifierar att implementationen följer den |
| Source of truth | Godkänner authority | Lokaliserar, hashar och kontrollerar |
| Scope | Godkänner | Upprätthåller och rapporterar scope pressure |
| Invariants | Beslutar | Översätter till kontroller och regressioner |
| Identitetsmodell | Godkänner | Spårar faktisk implementation och validerar |
| Arkitektur | Beslutar efter rekommendation | Inspekterar och föreslår minsta koherenta struktur |
| Kod | Granskar/auktoriserar fas | Implementerar inom godkänt scope |
| Tester | Godkänner acceptance meaning | Skapar/kör verifieringar utan att flytta målet |
| Empiriska claims | Godkänner slutlig formulering | Begränsar rapporten till observerad evidens |
| Deployment | Auktoriserar | Paketerar, inventerar och smoke-testar |
| Unknown/STOP | Ger ny authority eller accepterar stopp | Identifierar tidigt och improviserar inte |

---

## 11. När vi lämnar något som UNKNOWN eller STOP

### UNKNOWN

Används när en uppgift inte är verifierad men arbetet kan fortsätta utan att anta svaret.

Exempel:

- framtida hosting är inte beslutad;
- en optional artifact kommer senare;
- en historisk fil saknar provenance men ligger utanför runtime;
- en visuell detalj saknar slutligt beslut.

`UNKNOWN` ska dokumentera vilken evidens som saknas och vilken del av resultatet som därför inte kan hävdas.

### STOP

Används när fortsatt arbete skulle kräva att agenten uppfinner authority, förändrar ett skyddat kontrakt eller riskerar irreversibel skada.

Exempel:

- två olika databaser påstås vara den auktoritativa källan;
- identitetsfältet är inte definierat;
- en fryst artefakt har fel hash;
- output kan inte rekonstrueras deterministiskt från tillgängliga inputs;
- en frontendändring kräver en icke godkänd backendändring;
- en deploymentpatch skulle behöva inkludera okända eller oregistrerade artefakter;
- validering kräver att historiska baselines skrivs över.

En STOP-rapport ska bevaras när den dokumenterar ett verkligt kontraktsgap. När gapet senare löses skapas en ny kompletterande validering; den historiska STOP-rapporten skrivs inte om till ett skenbart PASS.

---

## 12. Praktisk startchecklista

### Project Owner

- [ ] Objective och första leverans definierade.
- [ ] Non-goals definierade.
- [ ] Auktoritativ repository/data/runtime namngiven.
- [ ] Allowed writes och read-only boundaries definierade.
- [ ] Identitet och lineage godkända.
- [ ] Representationer och transformationsgränser definierade.
- [ ] Invariants listade.
- [ ] Acceptance criteria observerbara.
- [ ] Deploymentmål eller uttrycklig frånvaro av deployment angiven.
- [ ] Stop conditions godkända.

### Coding agent

- [ ] Project root och instruktioner verifierade.
- [ ] Git/worktree-status inspekterad.
- [ ] Faktisk arkitektur och entry points kartlagda.
- [ ] Source inventory och hashes verifierade.
- [ ] Schema, radantal, encoding och storleksgränser inspekterade.
- [ ] Identitetskedjan spårad i kod och data.
- [ ] Runtime/build/deploymentgränser separerade.
- [ ] Existerande tester och baselines identifierade.
- [ ] Hidden dependencies och externa tjänster dokumenterade.
- [ ] Scope ledger och implementation order skapade.
- [ ] Unknowns och eventuella STOP redovisade före kod.

### Före första baseline freeze

- [ ] Fokuserade acceptance tests passerar.
- [ ] Failure cases är verifierade fail-closed.
- [ ] Relevanta regressioner passerar.
- [ ] Skyddade SHA-256 och IDs är oförändrade eller uttryckligen migrerade.
- [ ] Deterministic rebuild är verifierad där kontraktet kräver det.
- [ ] Release-/deploymentpaket innehåller endast avsedda filer.
- [ ] Validation report anger exakt vad som observerats.
- [ ] Kända begränsningar och unknowns är kvar i rapporten.

---

## 13. Sammanfattande princip

Vårt nuvarande arbetssätt kan sammanfattas som:

```text
Intent
  → Authority
  → Inspection
  → Contracts
  → Baseline
  → Minimal implementation
  → Focused validation
  → Regression
  → Reproducible release
```

Project Owner avgör vad som ska vara sant och vilka gränser som gäller. Coding agenten fastställer vad repositoryt faktiskt gör, genomför den minsta godkända förändringen och producerar evidens för resultatet.

När de två bilderna inte överensstämmer stannar vi och dokumenterar skillnaden. Vi fyller inte luckan med plausibel kod.

Det är den viktigaste egenskapen i vårt gemensamma arbetssätt idag: **implementationen ska kunna härledas till authority, och varje godkänt resultat ska kunna härledas till observerbar evidens.**
