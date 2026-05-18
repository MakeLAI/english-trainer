# Rooli: Project Manager

**Huom:** PM-rooli on siirtynyt käyttäjälle. Tämä tiedosto dokumentoi periaatteet ja päätökset
joita Claude Code noudattaa automaattisesti — ei erillistä PM-agenttia enää tarvita.

---

## Workflow (nykyinen)

```
Käyttäjä päättää → @agents/kieltenopettaja.md tuottaa sisällön
  → Käyttäjä hyväksyy → Claude Code lisää koodiin → GitHub
```

### Sanastolisäys
1. Käyttäjä pyytää: `@agents/kieltenopettaja.md Luo X sanaa Y-kategoriaan`
2. Claude Code toimii kieltenopettajan roolissa, tuottaa taulukon
3. Käyttäjä tarkistaa ja hyväksyy
4. Claude Code lisää VOCAB:iin (surgical edit) ja vie GitHubiin

### Koodimuutos
1. Käyttäjä kuvaa tarpeen tai oireen
2. Claude Code diagnosoi ja ehdottaa — käyttäjä hyväksyy
3. Claude Code tekee muutoksen ja raportoi tarkalleen mitä muutti
4. Käyttäjä testaa mobiililla

---

## Priorisointiperiaatteet

1. **Data ensin** — export-data ohjaa päätöksiä, ei olettamukset
2. **Stabiloi ennen kuin laajennat** — korjaa olemassa oleva ennen uusia ominaisuuksia
3. **Sisältö ennen ulkoasua** — lisää sanoja ennen visuaalisia parannuksia
4. **Yksinkertainen ennen monimutkaista** — export/import ennen Firebase-backendiä
5. **Testaa ennen seuraavaa** — jokainen muutos testataan mobiililla

---

## Päätökset jotka on jo tehty — ei avata uudelleen

| Päätös | Perustelu |
|---|---|
| Yksi `index.html` — ei build-prosessia | Henkilökohtainen käyttö, yksinkertaisuus tärkein |
| localStorage, ei backend | Ei julkinen sovellus, ei serveridataa |
| GitHub Pages, Public repo | Free-tili ei tue Private + Pages |
| Tip-tyyli: mnemoniikka + työkonteksti | Tehokkain muistirakenne |
| Kieltenopettaja ei muokkaa koodia | Laadunvalvonta |
| Surgical edit — ei full rewrite | Virheiden minimointi |
| Sanasto tarkistetaan 2+ lähteestä | IFRS-termistön tarkkuus |

---

## Opitut virheet — ei toisteta

| Virhe | Oppi |
|---|---|
| PM diagnosoi ongelman väärin | Anna aina oireet Claude Codelle. "Sovellus antaa virheen X — diagnosoi." |
| localStorage hävisi selaindatan tyhjennyksen yhteydessä | Älä koskaan tyhjennä sivustodataa ilman export-vaihetta ensin |
| ID-konflikteja sanojen lisäyksessä | Tarkista aina viimeisin käytetty ID koodista ennen lisäystä |
| Kieltenopettaja ylitti roolinsa | Pidä roolit selkeinä — sisältö ja koodi erikseen |
| Sisältövirheitä koodissa ilman tarkistusta | Käyttäjä tarkistaa taulukon ennen kuin Claude Code lisää koodiin |
| Kieltenopettaja toimitti yhteenvedon taulukon sijaan | Pyydä aina eksplisiittisesti: "Output: taulukko muodossa fi/en/exFi/exEn/tip" |
