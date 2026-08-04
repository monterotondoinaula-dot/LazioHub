# Monterotondo — Analisi morfologica DTM 5m + quartieri interattivi

Versione aggiornata: aggiunto layer vettoriale dei 17 quartieri comunali
(da sezioni censuarie ISTAT 2021), cliccabili, con statistiche zonali
calcolate sui 9 raster morfologici.

## File
- `index.html` — la mappa (sostituisce la versione precedente)
- `dati.json` — statistiche comunali aggregate (invariato)
- `quartieri.geojson` — **nuovo**: 17 poligoni quartiere con attributi
  (popolazione/famiglie/edifici ISTAT 2021 + medie zonali di ogni indice morfologico)
- `layers/` — i 9 overlay raster (invariati)

## Come sostituire il file esistente su GitHub
1. Nel tuo repo, dentro `monterotondo-dtm5m/` (o `primilayer_monte_dtm.html`
   se hai mantenuto quel nome), sostituisci il vecchio `index.html` con
   questo nuovo
2. Aggiungi il file `quartieri.geojson` nella stessa cartella (nuovo)
3. `dati.json` e `layers/` restano quelli che hai già — non serve ricaricarli

## Comportamento
- Il layer "Confini quartieri" è visibile di default sopra ogni raster
  (tratteggio verde scuro), disattivabile con la checkbox
- Cliccando su un quartiere: il pannello statistiche laterale mostra i
  dati zonali di quel quartiere invece del dato comunale, con badge
  identificativo e pulsante "torna al dato comunale"
- Cambiando layer raster (es. da Elevazione a Pendenza) con un quartiere
  selezionato, la statistica principale nel pannello si aggiorna di
  conseguenza

## Metodologia zonal statistics
Calcolate con `rasterstats` in Python: per ciascun quartiere e ciascun
indice, media/min/max dei pixel raster ricadenti nel poligono
(riproiettato in EPSG:6875 per coincidere col raster). Per la
geomorfologia (categorica): percentuale di pixel per classe.

Fonte confini: file caricato dall'utente
(`Quartieri_Monteorotondo_def.gpkg`), 17 quartieri con sezioni censuarie
ISTAT 2021 dissolte, CRS originale EPSG:32632.
