# Rischio da caldo — comuni del Lazio

Pagina statica che mostra il livello di rischio da caldo per il lavoro all'aperto (basso / moderato / alto / emergenza) per i **378 comuni del Lazio**, per oggi, domani e dopodomani. Dato pubblico via [Worklimate](https://github.com/aborruso/worklimate), aggiornato automaticamente ogni giorno.

Fork del progetto originale [PalermoHub/worklimate](https://github.com/PalermoHub/worklimate) (Sicilia), adattato al Lazio.

👉 **Pagina live:** pubblica il repo su GitHub Pages e sostituisci qui il link (es. `https://TUO-USER.github.io/worklimate-lazio/`).

## Cosa fa

- Tabella comune × giorno con il livello di rischio da caldo per fascia lavorativa 12–16.
- Ricerca/filtro per nome comune, ordinamento per comune o provincia.
- Tema chiaro/scuro con toggle (preferenza salvata in `localStorage`).
- Nessun backend: è un unico file HTML con dati incorporati, dati e viste generati staticamente.

## Come funziona

```
scripts/fetch-comuni.mjs   → genera data/comuni-lazio.json (elenco comuni + centroide)
scripts/update-data.mjs    → interroga Worklimate e scrive i dati dentro index.html
.github/workflows/         → esegue update-data.mjs ogni giorno e fa commit/push
```

- **`data/comuni-lazio.json`** — elenco statico dei comuni laziali (nome, provincia, sigla, codice ISTAT, coordinate del centroide), ricavato dai confini amministrativi ISTAT ([openpolis/geojson-italy](https://github.com/openpolis/geojson-italy)), province 56 (Viterbo), 57 (Rieti), 58 (Roma), 59 (Latina), 60 (Frosinone). Va rigenerato **a mano** con `node scripts/fetch-comuni.mjs` solo in caso di fusioni/istituzioni di nuovi comuni.
- **`index.html`** — contiene due marcatori che lo script aggiorna via regex, senza toccare il resto della pagina (HTML/CSS/JS):
  - `const GENERATED_AT = "YYYY-MM-DD"` — data della corsa che ha prodotto i dati (il giorno "oggi" della tabella).
  - `const DATA = [...]` — array `[nome, sigla_provincia, rischio_oggi, rischio_domani, rischio_dopodomani]` per ogni comune.

## Come vengono ricavati i dati

I dati arrivano dagli endpoint pubblici dell'app [Worklimate](https://app.worklimate.it/ordinanza-caldo-lavoro) (nessuna autenticazione richiesta), interrogati da `scripts/update-data.mjs`:

1. **Stazione più vicina** — per ogni comune (nome + centroide lat/lon) si chiama `osm-stazioni.php?osmod=true&place=...&latx=...&lonx=...`, che restituisce l'id della cella griglia meteo (`pgrid`) più vicina.
2. **Rischio per cella** — le celle griglia sono deduplicate (molti comuni condividono la stessa cella) e per ognuna si chiama `osm-stazioni.php?pgrid=...&sys=regular`, che restituisce il livello di rischio (`g1`/`g2`/`g3` = oggi/domani/dopodomani).
3. **Join finale** — ogni comune eredita il rischio della sua cella griglia, producendo le righe `[nome, sigla, g1, g2, g3]`.
4. **Scrittura** — se i comuni con dato completo sono meno dell'80% del totale, lo script si ferma con errore senza scrivere l'HTML (protezione contro API non disponibile/risposte parziali). Altrimenti sostituisce i marcatori `GENERATED_AT` e `DATA` in `index.html`.

Le chiamate sono throttled (100ms tra una richiesta e l'altra) e usano header realistici (`User-Agent`, `Referer`) per comportarsi come il client browser dell'app originale.

⚠️ **Nota**: in fase di test da alcuni ambienti (es. sandbox cloud) l'endpoint Worklimate può rispondere `403`. Da GitHub Actions (IP residenziali/datacenter standard) o da una macchina/rete normale dovrebbe funzionare regolarmente, come già avviene per il progetto Sicilia originale. Verifica il primo run manualmente da GitHub Actions (`workflow_dispatch`) prima di fidarti dello scheduling automatico.

### Automazione

Il workflow [`update-lazio-data.yml`](.github/workflows/update-lazio-data.yml) esegue `scripts/update-data.mjs` ogni giorno alle **05:30 UTC (07:30 CEST)**, prima della fascia di rischio 12–16, e fa commit/push di `data/rischio-oggi.json` e `data/storico-rischio.csv` se i dati sono cambiati. È anche lanciabile a mano da GitHub Actions (`workflow_dispatch`).

## Normativa di riferimento

Nel Lazio è in vigore l'ordinanza anti-caldo della Regione Lazio (2026, presidente Francesco Rocca), che dispone lo stop ai lavori all'aperto in condizioni di esposizione prolungata al sole nella fascia 12:30–16:00 nei giorni di rischio "alto" secondo Worklimate, per i settori più esposti (agricoltura/florovivaismo, edilizia, cave, logistica di piazzale, consegne urbane). Verifica sempre il testo ufficiale più recente sul sito della Regione Lazio, poiché ordinanze e scadenze vengono rinnovate/aggiornate stagionalmente.

## Sviluppo locale

```bash
# rigenerare i dati di rischio (richiede rete, interroga Worklimate)
node scripts/update-data.mjs

# rigenerare l'elenco comuni (solo se cambiano i confini amministrativi)
node scripts/fetch-comuni.mjs
```

Nessuna dipendenza da installare: gli script usano solo `fetch`/`fs` nativi di Node ≥ 18.

## Struttura repo

```
index.html                  pagina pubblicata (dati + UI)
data/comuni-lazio.json      elenco comuni laziali (statico)
scripts/update-data.mjs     job giornaliero: rischio da Worklimate → data/*.json
scripts/fetch-comuni.mjs    job manuale: confini ISTAT → data/comuni-lazio.json
.github/workflows/          automazione GitHub Actions
```

## Licenza
[CC BY 4.0 Attribuzione 4.0 Internazionale](https://creativecommons.org/licenses/by/4.0/deed.it)

Basato su [PalermoHub/worklimate](https://github.com/PalermoHub/worklimate), a sua volta basato sulla CLI [aborruso/worklimate](https://github.com/aborruso/worklimate).
