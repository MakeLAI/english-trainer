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

## 3. SOVELLUKSEN NYKYTILA (18.5.2026)

### Käyttäjän edistyminen
```
XP:       10 551+
Taso:     CFO (maksimitaso saavutettu)
Tarkkuus: 96%
Streak:   3+ päivää
```

### Sanasto — 163 sanaa (tarkistettu koodista 18.5.2026)

| Kategoria | Sanat | Viimeisin ID | Vapaat ID:t |
|---|---|---|---|
| basics | b1–b23 (23 kpl) | b23 | b24+ |
| balance | bs1–bs20 (20 kpl) | bs20 | bs21+ |
| income | is1–is19 (19 kpl) | is19 | is20+ |
| daily | d1–d22 (22 kpl) | d22 | d23+ |
| ifrs | i1–i29 (29 kpl) | i29 | i30+ |
| controlling | c1–c19 (19 kpl) | c19 | c20+ |
| phrases | p1–p31 (31 kpl) | p31 | p32+ |

**Tarkista aina viimeisin käytetty ID koodista ennen lisäystä.**

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

**Tilanne (28.5.2026):** 163 sanaa, CFO-taso, P1–P9 kaikki tehty. Variant A -teema tuotannossa.

**Seuraava tehtävä — P10 Daily Practice -uudistus:**
"Daily Practice" = automaattinen sekoitettu sessio (eri kysymystyypit automaattisesti)
"Harjoittele" = käyttäjä valitsee tyypin itse

**Jono sen jälkeen:** P11 (kirjoitusharjoitukset) → P12 (puheharjoitukset) → P13 (pelillistäminen) → P14 (synkronointi)

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
[ ] 5. Vasta sitten git push
[ ] 6. Kerro käyttäjälle: hard-refresh (Ctrl+Shift+R), GitHub Pages cache ~1-2 min
```
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

*Päivitetty: 28.5.2026 | Sanasto: 163 sanaa | Käyttäjätaso: CFO | Tehty: P1–P9 + Variant A -teema + P14 Gist-sync + tietoturvakovennus*
