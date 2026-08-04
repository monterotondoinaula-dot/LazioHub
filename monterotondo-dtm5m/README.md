# Monterotondo — Analisi morfologica DTM 5m

Mappa interattiva statica (Leaflet), 9 layer morfologici derivati dal
DTM 5m ritagliato sul confine ISTAT comunale (40,80 km²).

## Pubblicare su GitHub Pages
1. Crea un repository e carica tutto il contenuto di questa cartella
   (`index.html`, `dati.json`, `layers/`) nella root
2. Settings → Pages → Source: branch `main`, `/ (root)`
3. Pubblicata su `https://<utente>.github.io/<repo>/`

## Layer inclusi
1. Elevazione
2. Pendenza (algoritmo di Horn)
3. Esposizione
4. Rugosità (TRI)
5. Geomorfologia (5 classi: pianura, versante, cresta, valle, depressione)
6. Curvatura totale (Zevenbergen-Thorne)
7. Stabilità versanti (indice composito 1-5)
8. Costruibilità (indice composito 1-5)
9. Radiazione solare (SRI semplificato)

## Metodologia
- DTM 5m grezzo ritagliato sul confine amministrativo ISTAT (non bounding box)
- Pendenza/esposizione: algoritmo di Horn (1981), standard GDAL/QGIS
- Curvatura: metodo Zevenbergen & Thorne (1987)
- Smoothing gaussiano leggero (sigma=1px) pre-derivate per ridurre rumore
- Stabilità/costruibilità/radiazione: indici compositi semplificati,
  ispirati concettualmente al progetto Palermo Hub ma con formule proprie
  — **stime indicative, non sostituiscono studi geologici o energetici
  certificati**

## Validazione
Le statistiche aggregate (elevazione, pendenza, esposizione dominante)
sono state confrontate con la fonte ufficiale CNR-IRPI via Cruscotto
Italia (comune_kpi) e risultano coerenti:
- Elevazione media: 72,1 m (raster) vs 72 m (ufficiale)
- Pendenza media: 6,9° (raster) vs 6,7° (ufficiale)
- Esposizione dominante: Ovest in entrambi

## Fonti
- DTM: Panza, M. et al. (2026). *5m Resolution Digital Terrain Model
  for Italy*. Zenodo.
- Confine comunale: ISTAT / openpolis/geojson-italy (CC-BY)
- Metodologia ispirata a: Palermo Hub — OpenDataSicilia
  (https://palermohub.github.io/Palerm-DTM-5m/)

## Estensione futura
Per aggiungere le altre 27 analisi del progetto Palermo Hub (TWI,
flow accumulation, bacini idrografici, viewshed, PAI, incroci ISTAT,
ecc.) serve: raster/vettoriali aggiuntivi (uso suolo, PAI Lazio,
sezioni censuarie ISTAT), ed elaborazione idrologica più complessa
(algoritmo D8, pit-filling) tipicamente su QGIS/GDAL headless.
