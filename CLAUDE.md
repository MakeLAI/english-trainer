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

**Muutostyönkulku:**
1. Tee muutokset `index.html`-tiedostoon
2. Testaa selaimessa (ei Claude-appissa)
3. Vie GitHubiin → GitHub Pages päivittyy automaattisesti (~2 min)
4. Validointilause: "Päivitin index.html. GitHub Pages -versio on nyt ajantasainen."

---

## 3. SOVELLUKSEN NYKYTILA (18.5.2026)

### Käyttäjän edistyminen
```
XP:       10 551
Taso:     CFO (maksimitaso saavutettu 105 sessiossa)
Tarkkuus: 96%
Vahvat:   99/99 sanaa hallittu
Heikot:   0
Streak:   3 päivää
```
Käyttäjä on hallinnut kaikki 99 sanaa. Sisältöä tarvitaan välittömästi lisää.

### Sanasto — 99 sanaa (tarkistettu 18.5.2026)

| Kategoria | Sanat | Vapaat ID:t |
|---|---|---|
| basics | b1–b15 (15 kpl) | b16+ |
| balance | bs1–bs12 (12 kpl) | bs13+ |
| income | is1–is12 (12 kpl) | is13+ |
| daily | d1–d12 (12 kpl) | d13+ |
| ifrs | i1–i20 (20 kpl) | i21+ |
| controlling | c1–c8 (8 kpl) | c9+ |
| phrases | p1–p20 (20 kpl) | p21+ |

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

### KRIITTINEN — tee heti

**[P1] Sisällön laajennus**
Tavoite: 99 → 150+ sanaa. Kaikki kategoriat tarvitsevat lisää.
Prioriteettijärjestys: controlling (8→20), daily (12→20), balance (12→20), phrases (20→30).

**[P2] Session pituuden standardointi**
Ongelma: sessio vaihtelee (nyt n. 10 sanaa), pitäisi olla aina 15.
Korjaus: `startSession`-funktioon kiinteä 15 sanan sessio.

### TÄRKEÄ — seuraava kehitysvaihe

**[P3] Level-järjestelmän uudistus**
Ongelma: CFO saavutettu 105 sessiossa — liian nopeasti.
- Lisää väli-levelejä: 8 → 15+ tasoa
- Hitaampi XP-kertymä tai korkeammat XP-rajat
- Lisää saavutuksia: 9 → 20+
- Vanhat saavutukset päivitettävä

**[P4] Välilehtien tarkistus**
Kaikki 4 välilehteä luotu projektin alussa, sisältö muuttunut.
Tarkista ja päivitä: Etusivu, Library, Edistyminen, Asetukset.
Ohjeet-osio: tarkempi kuvaus mitä kullakin välilehdellä tehdään.

**[P5] Pitkät sanat mobiilissa**
Jotkut pitkät suomalaiset termit näyttävät oudoilta mobiilissa.
Ratkaisu: tekstin skaalaus tai katkaisu + full teksti tipissä.

**[P6] Kieliasun tarkistus**
Sovelluksessa sekaisin suomea ja englantia.
Tarkista kaikki UI-tekstit: kielioppi, pilkut, johdonmukaisuus.
Päätä: kokonaan suomeksi vai tietoisesti kaksikielinen?

**[P7] Daily insights -parannukset**
Nykyiset sessiolopun vinkit geneerisiä.
Tavoite: personoidut, dataan perustuvat vinkit.

**[P8] Kehitettävää-osion parannus**
Lisää heikon sanan kohdalle muistisääntö (tip näkyviin ilman harjoittelua).

**[P9] Sanoihin porautuminen**
Library: napautus → detaili-näkymä (esimerkkilause, tippi, SRS-status, historia).

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

**Workflow:**
```
@agents/kieltenopettaja.md tuottaa taulukon
  → Käyttäjä hyväksyy
  → Claude Code lisää VOCAB:iin + vie GitHubiin
```

**Roolitiedostot:** `agents/kieltenopettaja.md` · `agents/project-manager.md`

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

**Tilanne (18.5.2026):** 99 sanaa, Controller-taso (10 551 XP), P2–P6 tehty tänään.

**Seuraava tehtävä — P1 Sisällön laajennus:**
`@agents/kieltenopettaja.md` Luo 12 uutta sanaa controlling-kategoriaan (c9–c20).
Käyttäjä hyväksyy taulukon → Claude Code lisää VOCAB:iin.

**Jono sen jälkeen:** P7 (personoidut sessiovinkit) → P8 (tip heikon sanan kohdalla) → P9 (sanoihin porautuminen)

---

*Päivitetty: 18.5.2026 | Sanasto: 99 sanaa | Käyttäjätaso: Controller (10 551 XP)*
