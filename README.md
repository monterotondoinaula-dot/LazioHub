# Monterotondo Hub

Contenitore di mappe civiche su Monterotondo e il Lazio, ispirato nella struttura a
[PalermoHub](https://github.com/SiciliaHub/palermohub) di OpenDataSicilia.it (CC BY-SA 4.0).
Nessun dato o contenuto di PalermoHub è riutilizzato: solo l'approccio (pagina HTML +
Leaflet + dati esportati da QGIS come file .js separato).

## Struttura

```
monterotondo-hub/
├── index.html                     # homepage / elenco mappe
├── base_iframe_template.html      # template per una nuova mappa
├── data/
│   └── <nome_layer>_0.js         # GeoJSON esportato da QGIS2Web
└── legend/                        # eventuali immagini di legenda
```

## Workflow: da QGIS al sito

1. **Prepara il layer in QGIS**
   - Carica i dati sorgente (es. shapefile ISTAT, GeoJSON da Overpass, dati comunali)
   - Pulisci/filtra/unisci i layer come necessario
   - Verifica il CRS: per il web serve **EPSG:4326** (WGS84)

2. **Esporta con il plugin QGIS2Web**
   - Menu `Web > QGIS2Web > Create web map`
   - Motore: **Leaflet**
   - Seleziona i layer, imposta popup con i campi che vuoi mostrare
   - Esporta: genera una cartella con `index.html` + `data/*.js`
   - Ogni file `.js` contiene una variabile tipo `var json_nomelayer_0 = {...GeoJSON...};`

3. **Integra nel template**
   - Copia il file `data/nomelayer_0.js` generato in `data/` del repo
   - Copia il `<script src="data/nomelayer_0.js">` nel template
   - Aggiorna il nome variabile GeoJSON nello script (`json_nomelayer_0`)
   - Aggiorna centro mappa, titolo, fonte dati, popup

4. **Pubblica**
   - Push su GitHub, attiva GitHub Pages sul branch/cartella scelta
   - Ogni mappa è una pagina HTML autonoma, come in palermohub

## Licenza

Il codice di questo repo è rilasciato in CC BY-SA 4.0.
I dati usati mantengono la licenza della fonte originale (va sempre verificata e citata
per singolo dataset, come nel box "Dati:" di ogni pagina mappa).
