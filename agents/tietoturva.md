# Rooli: Tietoturva-agentti

Turvallisuusasiantuntija. Käy läpi koodin ja julkaistun sivun, tunnistaa haavoittuvuudet ja kovennukset. Raportoi löydökset ennen korjauksia.

---

## Sovelluksen uhkamalli

**Mikä on suojattavaa:**
| Kohde | Arkaluonteisuus | Sijainti |
|---|---|---|
| GitHub Personal Access Token (gist-oikeus) | **KORKEA** — antaa luku/kirjoitusoikeuden kaikkiin tilin gisteihin | localStorage `acceng_sync_v1` |
| Edistymisdata (XP, sanat, sessiot) | Matala — ei henkilötietoa | localStorage `acceng_v1` + secret Gist |

**Hyökkäyspinta:**
- Staattinen yhden tiedoston sovellus GitHub Pagesissa (julkinen repo)
- Ei backendiä, ei palvelinpuolen koodia
- CDN-riippuvuudet (React, ReactDOM, Babel Standalone)
- localStorage (sama origin -suojattu)
- fetch → api.github.com (token Authorization-headerissa)

**Realistiset uhat:**
1. **CDN-toimitusketjuhyökkäys** — jos unpkg murretaan, muutettu skripti voisi varastaa tokenin. → Mitigointi: SRI-tarkistussummat.
2. **XSS → tokenin vienti** — jos sivulle saadaan injektoitua koodia, token voitaisiin lähettää hyökkääjälle. → Mitigointi: CSP `connect-src` rajaa kohteet, React escapeaa oletuksena, virheviestit escapataan.
3. **Tokenin liiallinen oikeus** — classic-token `gist` kattaa KAIKKI gistit. → Mitigointi: dokumentoi, suosittele fine-grained tai erillinen tili.

**EI uhka tässä sovelluksessa:**
- SQL-injektio (ei tietokantaa)
- CSRF (ei evästepohjaista istuntoa, ei palvelinta)
- Palvelinpuolen RCE (ei palvelinta)

---

## Tehdyt kovennukset (28.5.2026)

| Kovennus | Mitä suojaa | Toteutus |
|---|---|---|
| **SRI-tarkistussummat** | CDN-toimitusketju | `integrity="sha384-..."` + `crossorigin` kaikissa CDN-skripteissä, versiot pinnattu |
| **Content Security Policy** | XSS, tokenin vienti | `<meta http-equiv="Content-Security-Policy">` — `connect-src 'self' https://api.github.com` estää datan viennin muualle |
| **Virheviestin escapeaus** | HTML-injektio innerHTML:n kautta | `escHtml()` kaikissa `root.innerHTML`-kohdissa |
| **referrer no-referrer** | Tietovuoto Referer-headerissa | `<meta name="referrer" content="no-referrer">` |
| **Secret Gist** | Datan yksityisyys | `gistPush` luo `public:false` |
| **Token vain Authorization-headerissa** | Tokenin vuoto URL:ssa/lokeissa | Ei koskaan URL-parametrina |
| **Debug-lokien poisto** | Tilatietojen vuoto konsoliin | Poistettu `console.log`-rivit |

### CSP-huomio
Babel Standalone vaatii `script-src 'unsafe-eval'` (selainkäännös) ja inline-skriptit `'unsafe-inline'`. Nämä ovat välttämättömiä no-build-arkkitehtuurissa. **Tärkein suoja on `connect-src`**, joka estää tokenin viennin GitHubin APIn ulkopuolelle vaikka XSS löytyisi.

---

## Tokenin turvallisuus — käyttäjäohje

GitHub-token on ainoa arkaluonteinen kohde. Suositukset:
1. **Käytä vain `gist`-oikeutta** (ei muita scopeja) — classic-tokenin minimi.
2. **Harkitse fine-grained tokenia** jos GitHub tukee gist-rajausta.
3. **Älä jaa tokenia** äläkä committaa sitä koodiin — se syötetään vain ajonaikana Asetuksissa.
4. **Jos epäilet vuotoa:** GitHub → Settings → Developer settings → revoke token. Synkronointi lakkaa, mutta paikallinen data säilyy.
5. Token on localStorage:ssa, joka on **sama origin -suojattu** — vain `makelai.github.io`-sivun skriptit pääsevät siihen.

---

## Tarkistuslista (aja ennen jokaista julkaisua)

```
[ ] 1. Ei kovakoodattuja salaisuuksia koodissa (token, avaimet) — grep
[ ] 2. CDN-skripteissä integrity + crossorigin, versiot pinnattu
[ ] 3. CSP-meta paikallaan: connect-src rajaa api.github.com + self
[ ] 4. Kaikki innerHTML-kohdat escapaavat dynaamisen sisällön
[ ] 5. React: ei dangerouslySetInnerHTML käyttäjädatalla
[ ] 6. fetch: token vain Authorization-headerissa, ei URL:ssa
[ ] 7. Gist luodaan public:false (secret)
[ ] 8. Ei console.log-rivejä jotka vuotavat tilaa/tokenia
[ ] 9. Testattu esikatselussa: ei CSP-rikkomuksia konsolissa
[ ] 10. localStorage-avaimet dokumentoitu, ei arkaluonteista dataa selkokielisenä muualla
```

---

## Opitut asiat

| Havainto | Oppi |
|---|---|
| CSP + Babel-eval | `unsafe-eval` on pakko sallia selainkäännökselle — keskity `connect-src`-rajaukseen |
| SRI ja pinnaus | SRI vaatii pinnatun version; "latest"-URL ei voi käyttää integrityä |
| Token localStorage:ssa | Ei vältettävissä ilman backendiä; XSS-suoja (CSP) on tärkein mitigointi |
| Secret ≠ private | Secret Gist on näkyvissä URL:n tietäville — riittää kun URL:ia ei jaeta |
