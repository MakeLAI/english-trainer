# CLAUDE.md — Accounting English Trainer
> Projektin ohjeistus Claude Codelle. Päivitetty 18.5.2026.
> Lue tämä kokonaan ennen kuin teet mitään muutoksia.

---

## 1. PROJEKTIN KUVAUS

Henkilökohtainen englannin sanastoharjoittelusovellus suomalaiselle controllerille/kirjanpitäjälle. Tarkoitus on kehittää ammatillista englantia niin, että käyttäjä pystyy kommunikoimaan töissä paremmin englanniksi.

**Tuotantoversio:** https://makelai.github.io/english-trainer
**Repository:** github.com/MakeLAI/english-trainer
**Tiedosto:** yksi `index.html` — ei build-prosessia, ei npm

**EI julkinen sovellus.** Henkilökohtainen käyttö. Ei rekisteröitymistä, ei serveridataa, ei analytiikkaa.

---

## 2. TEKNINEN STACK

```
React 18         — UI (ladataan unpkg CDN:stä)
Babel Standalone — JSX-transpilaus selaimessa
Tailwind Play    — CSS (Play CDN)
localStorage     — tietojen tallennus (per laite, per selain)
GitHub Pages     — hosting (Public repo, Free tier)
```

**Tärkeä rajoite:** Ei backendiä, ei npm, ei build-vaihetta. Kaikki yhdessä `index.html`-tiedostossa. Apostrofit JSX-merkkijonoissa escapataan: `machine\'s` tai käytetään double quoteja.

### ⚠️ BABEL-SUDENKUOPPA — KLASSINEN JSX-AJONAIKA PAKOLLINEN

**Älä käytä `<script type="text/babel">`-automaattikäännöstä.** Babel Standalonen react-preset käyttää nykyään "automatic"-JSX-ajonaikaa, joka lisää käännettyyn koodiin rivin:
```js
import { jsx as _jsx } from "react/jsx-runtime";
```
Tämä `import`-lause kaataa koko sovelluksen (musta ruutu) virheellä **"Cannot use import statement outside a module"**, koska CDN-setupissa ei ole moduulijärjestelmää. `data-presets`/`data-type`-attribuutit EIVÄT korjaa tätä.

**Oikea tapa (käytössä index.html:ssä):** koodi on inert-skriptissä `<script type="text/jsx-source" id="app-src">`, ja erillinen ajuriskripti kääntää sen manuaalisesti pakottaen klassisen ajonajan:
```js
var out = Babel.transform(src, {presets:[['react',{runtime:'classic'}]]}).code;
(0,eval)(out);
```
`runtime:'classic'` → `React.createElement`, EI import-lauseita. **Tätä rakennetta ei saa muuttaa takaisin automaattikäännökseen.**

**Diagnoosi jos musta ruutu palaa:** avaa konsoli (F12). "Cannot use import statement outside a module" = ajonaika-ongelma. Tarkista käännetty skripti: jos se alkaa `import {...}`, klassinen ajonaika ei ole päällä.

**Muutostyönkulku:**
1. Tee muutokset `index.html`-tiedostoon
2. Testaa selaimessa (ei Claude-appissa)
3. Vie GitHubiin → GitHub Pages päivittyy automaattisesti (~2 min)
4. Validointilause: "Päivitin index.html. GitHub Pages -versio on nyt ajantasainen."

---

## 3. SOVELLUKSEN NYKYTILA (1.8.2026)

### Käyttäjän edistyminen
```
XP:       10 551+
Taso:     CFO (maksimitaso saavutettu)
Tarkkuus: 96%
Streak:   3+ päivää
```

### Sanasto — 250 sanaa (tarkistettu koodista 1.8.2026)

| Kategoria | Sanat | Viimeisin ID | Vapaat ID:t |
|---|---|---|---|
| basics | b1–b33 (33 kpl) | b33 | b34+ |
| balance | bs1–bs32 (32 kpl) | bs32 | bs33+ |
| income | is1–is31 (31 kpl) | is31 | is32+ |
| daily | d1–d35 (35 kpl) | d35 | d36+ |
| ifrs | i1–i42 (42 kpl) | i42 | i43+ |
| controlling | c1–c34 (34 kpl) | c34 | c35+ |
| phrases | p1–p43 (43 kpl) | p43 | p44+ |

**Tarkista aina viimeisin käytetty ID koodista ennen lisäystä.**

**Laajennushistoria:** 163 → 250 sanaa (1.8.2026), 87 uutta sanaa 7 erässä. Jokainen tulkinnanvarainen termi verkkotarkistettu (Finlex/IFRS/KPA-lähteet) ennen lisäystä. Samassa yhteydessä korjattiin 5 päällekkäistä englanninkielistä käännöstä (i3, i4, p9, p22, d29), jotka olisivat aiheuttaneet monivalinnassa kaksi identtistä vaihtoehtoa.

**Vaihtoehtoiset hyväksyttävät vastaukset:** `ALT_ANSWERS`-taulukko (index.html, VOCAB-taulukon jälkeen) — synonyymit kirjoitusharjoitukseen. Lisää tähän, älä VOCAB-riviin.

**Duplikaattitarkistus ennen jokaista sanastolisäystä (pakollinen):**
```
[ ] 1. Ei duplikaatti-ID:itä
[ ] 2. Ei duplikaatti-englanninkielisiä termejä KOKO sanastossa (ei vain uusissa)
[ ] 3. Ei duplikaatti-suomenkielisiä termejä KOKO sanastossa
[ ] 4. Tarkistus tehdään selaimessa VOCAB-taulukkoa lukemalla — ei pelkkää tekstihakua (apostrofit ja escapoinnit vääristävät tekstihakuja)
```

### Prepositiot — erillinen kevyt harjoittelumoduuli (lisätty 2.8.2026, sisältö valmis 2.8.2026)

**70 lausetta** (pr1–pr70), rakennettu 5 erässä (1 moottorin todennus + 4 sisältöerää). Data: `PREP_SENTENCES`-taulukko VOCAB:in jälkeen index.html:ssä.

| Kategoria | Lauseet | Viimeisin ID |
|---|---|---|
| business | pr1–pr6, pr17–pr30 (20 kpl) | pr30 |
| time | pr7–pr11, pr31–pr45 (20 kpl) | pr45 |
| place | pr12–pr14, pr46–pr57 (15 kpl) | pr57 |
| general | pr15–pr16, pr58–pr70 (15 kpl) | pr70 |

**Tarkista aina viimeisin käytetty ID koodista ennen lisäystä — ID-numerointi ei ole yhtenäinen kategorioittain** (esim. business jatkuu pr17:sta pr30:een, ei suoraan pr7:sta).

**Rakenne:** `{id, cat, diff, sentence (lause jossa '___' merkitsee aukkoa), answer, fi (suomennos kontekstiksi), tip}`.

**Arkkitehtuuri — TÄRKEÄÄ ymmärtää ennen laajentamista:**
- Täysin **erillinen tila sanastosta**: `state.preps` (peilaa `state.words`-rakennetta), `state.prepTotalCorrect/Wrong`, `state.prepSessions`. EI omaa XP:tä/tasoa/saavutuksia (kevyt seuranta -päätös) — vain tarkkuus % ja hallittujen lauseiden määrä.
- Sama Leitner-SRS-moottori (BOX_INTERVALS) uudelleenkäytettynä, mutta `onFinishPrep()` (App-komponentissa) EI koske `words`/`xp`/`achievements`-kenttiin.
- Kaksi harjoitustyyppiä: `prepQuiz` (valitse listalta, häiriövaihtoehdot `COMMON_PREPS`-poolista) ja `prepWrite` (kirjoita itse, normalisointi samalla `normalizeAns()`-funktiolla kuin sanaston kirjoitustehtävässä).
- Oma komponentti `PrepPractice` (Practice-komponentin rinnalla). `App()`:ssa `isPrepMode(practMode)` päättää kumpaa komponenttia renderöidään.
- Etusivulla kaksi selkeää painiketta: **"Sanasto →"** (pääpainike) ja **"Prepositiot →"** (toissijainen, outline-tyyli). EI omaa alapalkin välilehteä — pidetään 5-välilehtirakenne.
- **Kaikki muutokset tähän moduuliin pitää heijastaa myös Ohjeet-välilehdellä** (käyttäjän nimenomainen vaatimus 2.8.2026) — GuideView:ssa oma "Prepositioharjoittelu"-kortti Leitner-kortin jälkeen.
- StatsView:ssä oma "Prepositiot kategorioittain" -osio (sama malli kuin sanaston kategoriapalkit, mutta `PREP_CATS`/`PREP_SENTENCES`-pohjainen).

**Laajennettaessa:** lisää `PREP_SENTENCES`-taulukkoon, tarkista viimeisin ID koodista, aja sama duplikaattitarkistus kuin sanastolle (selaimen kautta). Ei tarvitse koskea muualle — data-taulukko riittää.

### Sanastorakenne
```javascript
{
  id:   'b1',
  fi:   'kirjanpito',
  en:   'bookkeeping',
  cat:  'basics',
  diff: 1,             // 1=helppo, 2=keskitaso, 3=vaikea
  exFi: 'Esimerkkilause suomeksi.',
  exEn: 'Example sentence in English.',
  tip:  'KEYWORD = selitys. Työkonteksti. Standardi tai ero muihin termeihin.'
}
```

### Tip-tyyli (vakioitu)
```
"KEYWORD = suomenkielinen selitys = konteksti.
Esimerkki kokouksessa tai sähköpostissa: "Lause tähän."
Standardi (esim. IFRS 9) tai ero samankaltaiseen termiin."
```

### Sanastolaatuvaatimukset
Jokainen sana tarkistettu vähintään kahdesta lähteestä:
IFRS Foundation, Cambridge Business English Dictionary,
Longman Business English Dictionary, PWC, KPMG, Aalto, TEPA, BDO, ACCA

---

## 4. KOODIN RAKENNE

```
index.html
├── <head>           — meta-tagit, CDN-linkit
├── <style>          — globaalit tyylit
└── <script type="text/babel">
    ├── Icon-komponentit
    ├── BOX_INTERVALS = [0,1,3,7,14,30]
    ├── SK = 'acceng_v1'          — localStorage-avain
    ├── CATEGORIES                — kategorioiden metadata
    ├── LEVELS                    — XP-tasot (päivitettävä)
    ├── ACHIEVEMENTS              — saavutukset (päivitettävä)
    ├── VOCAB = [...]             — SANASTO — muuta tänne
    ├── Helper-funktiot
    ├── localStorage-funktiot
    ├── UI-komponentit (Pill, TopBar, KCard, CatPill, Dots, Field, Nav)
    ├── Flashcard-komponentti
    ├── Quiz-komponentti
    └── App() — näkymät: home, modes, practice, library, stats, settings
```

**Surgical edit -periaate:** Muuta VAIN tarvittava kohta. Ei full rewrite pieniin muutoksiin. Raportoi tarkalleen mitä muutit ja missä kohdassa.

---

## 5. KEHITYSBACKLOG

### TEHTY ✓

| Koodi | Tehtävä |
|---|---|
| P1 | Sisällön laajennus: 99 → 163 sanaa |
| P2 | Session pituuden standardointi (15 sanaa) |
| P3 | Level-järjestelmän uudistus (8 → 15+ tasoa, hitaampi XP) |
| P4 | Välilehtien tarkistus ja päivitys |
| P5 | Pitkät sanat mobiilissa (skaalaus/katkaisu) |
| P6 | Kieliasun tarkistus ja johdonmukaistaminen |
| P7 | Daily insights -parannukset (personoidut vinkit) |
| P8 | Tip heikon sanan kohdalla ilman harjoittelua |
| P9 | Sanoihin porautuminen Libraryssä (detaili-näkymä) |

### TULEVA KEHITYS

**[P10] Daily Practice -uudistus**
- "Daily Practice" = automaattinen sekoitettu sessio (eri kysymystyypit)
- "Harjoittele" = käyttäjä valitsee tyypin itse

**[P11] Kirjoitusharjoitukset**
Uusi harjoitustyyppi: käyttäjä kirjoittaa englanninkielisen termin.
Vaatii tekstikentän + tarkistuslogiikan.

**[P12] Puheharjoitukset**
Web Speech API — toimii mobiiliselaimessa ilman backendiä.
Vaatii merkittävän arkkitehtuurimuutoksen.

**[P13] Pelillistäminen ja UX-uudistus**
Vaatii UX-agentin perustamisen Claude-projektiin.
- Visuaalinen uudistus mobiilille
- Enemmän animaatioita
- Streak-muistutukset

**[P14] Käyttäjätili ja laitteiden välinen synkronointi**
Nykyinen localStorage ei synkronoidu laitteiden välillä.
Vaihtoehdot: GitHub progress.json (helpompi) tai Firebase/Supabase (täysi backend).

---

## 6. ARKKITEHTUURI

Kaikki toimii Claude Codessa. PM-rooli on siirtynyt käyttäjälle.

| Rooli | Kuka | EI tee |
|---|---|---|
| **Project Manager** | Käyttäjä | — |
| **Claude Code** | Tämä sessio | Ei lisää sanastoa itsenäisesti ilman hyväksyntää |
| **Kieltenopettaja** | `@agents/kieltenopettaja.md` | Ei muokkaa koodia, ei vie GitHubiin |
| **UX-agentti** | `@agents/ux-agent.md` | Ei muuta sisältörakennetta ilman sisältökartan hyväksyntää |
| **Tietoturva-agentti** | `@agents/tietoturva.md` | Käy läpi koodin & sivun, korjaa haavoittuvuudet; raportoi ennen muutoksia |

**Workflow — sanasto:**
```
@agents/kieltenopettaja.md tuottaa taulukon
  → Käyttäjä hyväksyy
  → Claude Code lisää VOCAB:iin + vie GitHubiin
```

**Workflow — visuaalinen uudistus:**
```
@agents/ux-agent.md tuottaa sisältökartan (välilehti + osiot)
  → Käyttäjä hyväksyy kartan
  → UX-agentti tuottaa muutoslistan
  → Käyttäjä hyväksyy
  → Claude Code toteuttaa surgical editinä + tarkistuslista + vie GitHubiin
```

**Roolitiedostot:** `agents/kieltenopettaja.md` · `agents/project-manager.md` · `agents/ux-agent.md` · `agents/tietoturva.md`

---

## 7. OPPIMISALGORITMI (Leitner SRS)

```
5 boksia, intervallit: 0, 1, 3, 7, 14, 30 päivää
Oikein → box + 1
Väärin → box = max(0, box - 1)
Box >= 3 = "hallittu" (mastered)
nextReview = tänään + BOX_INTERVALS[box]
```

---

## 8. TÄRKEIMMÄT OPITUT ASIAT

1. **Diagnosoi oireet, älä ratkaisuja.** Anna oireet, anna asiantuntijan diagnosoida.
2. **Tarkista ID:t ennen lisäystä.** Duplikaatti-ID rikkoo SRS:n.
3. **Testaa mobiililla** jokaisen merkittävän muutoksen jälkeen.
4. **Älä tyhjennä selaimen sivustodataa** — poistaa myös localStorage:n. Exporttaa ensin.
5. **window.storage on Claude-spesifinen.** GitHubissa käytetään localStorage:a.
6. **Private repo + GitHub Pages vaatii maksullisen tilin.** Käytä Public-repoa.
7. **Kieltenopettaja ei koskaan muokkaa koodia.**
8. **Apostrofit JSX:ssä:** `machine\'s` tai käytä double quoteja.
9. **Surgical edit aina** — ei full rewrite pieniin muutoksiin.
10. **Visuaalinen uudistus ≠ rakenteen muutos.** Teema vaihtuu, sisältörakenne pysyy. Ks. UX-agentti.
11. **Sisältökartta ennen redesignia.** Jokaisen välilehden sisältö dokumentoitava ja hyväksyttävä ennen koodia.

---

## 9. GCAO+V PROMPTIMALLI

```
Goal:      [Mitä haluat saavuttaa]
Context:   [Nykytila, viittaa koodiin suoraan]
Action:    [Toimintaverbi + tarkat ohjeet]
Output:    [Lopputuloksen muoto]
Validointi:[Mitä tarkistetaan — ID:t, käynnistys, muutetut rivit]
```

---

## 10. MALLIEN KÄYTTÖ

| Malli | Käytä kun |
|---|---|
| Haiku 4.5 | Mekaaniset: sanojen lisäys, formatointi |
| Sonnet 4.6 | Normaali kehitys: koodimuutokset, analysointi |
| Opus 4.6 | Vaikeat: arkkitehtuurimuutos, bugi jota Sonnet ei ratkaise |

---

## 11. ALOITA SEURAAVA SESSIO TÄSTÄ

**Tilanne (2.8.2026):** 250 sanaa + 70 prepositiolausetta, CFO-taso, Variant A -teema tuotannossa, Gist-sync + tietoturvakovennus + fiksumpi harjoitussessio (painotettu otanta + kategoriakohtaiset häiriövaihtoehdot) käytössä.

**Tehty:** P1–P9 (alkuperäinen sisältö/laatu), P11 (kirjoitusharjoitus), P14 (Gist-sync), sanasto 163→250, uusi prepositiomoduuli (70 lausetta, oma kevyt seuranta).

**Jäljellä backlogista:** P12 (puheharjoitukset, Web Speech API — iso arkkitehtuurimuutos) → P13 (pelillistäminen/UX-uudistus, ks. `agents/ux-agent.md` ja Visual Redesign Protocol luku 12 ENNEN aloitusta).

**Mahdollinen jatko prepositiomoduulille:** laajenna `PREP_SENTENCES`-taulukkoa (ks. yllä), tai harkitse omaa mini-saavutusta/streakiä jos kevyt seuranta alkaa tuntua riittämättömältä — kysy käyttäjältä ensin, "kevyt seuranta" oli tietoinen valinta.

---

## 12. VISUAL REDESIGN PROTOCOL

**Käytä tätä aina kun teema tai layout muuttuu merkittävästi.**

### Kolme pakollista vaihetta

**Vaihe 1 — Sisältökartta (ENNEN koodia)**
UX-agentti toimittaa taulukon: välilehti | osiot järjestyksessä | muuttuuko?
Käyttäjä hyväksyy. Vasta sen jälkeen koodataan.

**Vaihe 2 — Muutoslista**
UX-agentti listaa: mitkä komponentit muuttuvat, mitkä värit/fontit vaihtuvat.
Käyttäjä hyväksyy. Vasta sen jälkeen pushataan.

**Vaihe 3 — Tarkistuslista ennen pushia**
```
[ ] Kaikki 5 välilehteä paikallaan (Etusivu/Sanasto/Edistyminen/Ohjeet/Asetukset)
[ ] Jokaisen välilehden sisältö vastaa hyväksyttyä sisältökarttaa
[ ] localStorage-avain: acceng_v1 — muuttumaton
[ ] BOX_INTERVALS = [0,1,3,7,14,30] — koskematta
[ ] VOCAB-taulukko — koskematta
[ ] Testattu mobiililla (390px)
[ ] Apostrofit escapattu JSX:ssä
```

### Miksi tämä on tärkeää
Visual Redesign 18.5.2026 ilman sisältökarttaa → stats väärällä välilehdellä,
Ohjeet-välilehti katosi, Kategoriat duplikoitui → 3 erillistä korjauspushia.
Sisältökartta + tarkistuslista olisivat estäneet kaikki nämä.

---

## 13. DATA SAFETY & DEPLOYMENT PROTOCOL ⚠️ (LUE ENNEN JOKAISTA PUSHIA)

**Tärkein sääntö koko projektissa: käyttäjän edistyminen ei saa KOSKAAN kadota, eikä rikkinäinen päivitys saa mennä tuotantoon.** Tämä luku syntyi koska dataa hävisi ja sovellus mustaruutuili useamman päivityksen aikana.

### A. Deployment — pakollinen verifiointi ennen pushia
Selain-Babel tarkoittaa, että syntaksi-/ajovirheet näkyvät VASTA ajossa → rikkinäinen push mustaruutuilee tuotannon hiljaa. Siksi:
```
[ ] 1. Lataa muutos PAIKALLISEEN esikatseluun (ei vain lue koodia)
[ ] 2. Varmista: #root dataset.mounted==='1' JA lapsia on
[ ] 3. Konsolissa NOLLA virhettä (vain Babel-varoitus sallittu)
[ ] 4. Jos koskettaa harjoitus-/synkronointi-/asetuslogiikkaa: testaa se polku esikatselussa
[ ] 5. Nosta APP_VERSION **ja päivitä sen päivämäärä todelliseen julkaisupäivään** (tarkista oikea päivä — älä kopioi vanhaa)
[ ] 6. Vasta sitten git push
[ ] 7. Kerro käyttäjälle: hard-refresh (Ctrl+Shift+R), GitHub Pages cache ~1-2 min
```
**APP_VERSION on muodossa `'numero · YYYY-MM-DD'`.** Numero ja päivämäärä päivitetään AINA yhdessä — väärä päivämäärä tekee versioleimasta hyödyttömän cache-diagnostiikassa.
**Älä koskaan palauta `<script type="text/babel">`-automaattikäännöstä** (ks. luku 1 Babel-sudenkuoppa). Vain manuaalinen `runtime:'classic'`.

### B. Data — kerroksittainen suoja edistymiselle
1. **hydrated-suoja:** auto-push EI saa lähettää mitään ennen kuin käynnistyslataus on valmis. Tyhjän 0 XP:n työntäminen pilveen oikean datan päälle = pahin mahdollinen vika.
2. **XP-regressiosuoja:** auto-push EI saa hiljaa lähettää tilaa, jonka XP on pienempi kuin pilven viimeisin. XP:n lasku (nollaus) vaatii käyttäjän painaman "(pakota)"-napin.
3. **Varmuuskopiot monessa kerroksessa:** paikallinen paras-XP-snapshot localStorage:ssa + pilven `progress.prev.json` (edellinen versio).
4. **Avainerottelu:** `acceng_v1` = edistyminen, `acceng_sync_v1` = synkronointiasetukset. "Nollaa synkronointi" -nappi koskee VAIN jälkimmäistä. Älä koskaan vahingossa kytke nollausta edistymisavaimeen.
5. **Ennen nollaus-/destruktiivisia synkronointitestejä:** exporttaa .json-varmuuskopio ensin.

### Miksi tämä on tärkeää
Tässä projektissa: auto-push olisi voinut ylikirjoittaa 17 684 XP:n nollalla (estetty hydrated-suojalla); localStorage tyhjeni laitteelta (estetty Gist-syncillä); Babel automatic runtime mustaruutuili kaiken (estetty klassisella ajonajalla). Jokainen näistä olisi vältetty noudattamalla tätä protokollaa.

---

*Päivitetty: 2.8.2026 | Sanasto: 250 sanaa | Prepositiot: 70 lausetta | Käyttäjätaso: CFO | Tehty: P1–P9 + Variant A -teema + P11 kirjoitusharjoitus + P14 Gist-sync + tietoturvakovennus + fiksumpi harjoitussessio + prepositiomoduuli*
