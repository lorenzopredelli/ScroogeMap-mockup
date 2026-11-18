# Piano frontend – Pagina `ricerca-ai.html`

## Obiettivi e vincoli
- Realizzare la pagina **`ricerca-ai.html`** curando **solo il frontend** (mockup): nessuna integrazione AI/backend reale, nessuna ricerca effettiva; risultati simulati lato client dopo un finto caricamento.
- **Header** identico a quello delle pagine per utenti registrati (stesso markup/struttura di `mappa.html`: menu `Mappa`, `Budgeting`, `Ricerca AI`, CTA `Impostazioni profilo`).
- **Footer** identico a quello del resto del sito (`<footer>` con i due `<span>` copyright/autori).
- Riutilizzare il più possibile `CSS/style.css` (variabili, `.wrap`, `.card`, `.btn`, `.lead`, `.subnav`/tab, stili nodi `.node-card`/`.tag`/`.details-*`, componenti mappa) ed estendere solo con classi specifiche per la pagina ricerca.
- Coerenza visiva con le pagine esistenti (light/dark, accenti `--gold`/`--teal`, bordi `--line`, card arrotondate).

## File previsti
- `ricerca-ai.html` – nuova pagina.
- `CSS/style.css` – aggiunta stili per intro ricerca, form ricerca, area risultati (stati loading/empty/results), widget mappa e adattamenti layout liste/dettagli nel contesto risultati.
- `assets/trento.pmtiles` – riusato per la vista mappa (widget) come in `mappa.html`, servito via HTTP.
- Dipendenze CDN (solo per vista mappa, come in `mappa.html`): MapLibre GL CSS/JS, `pmtiles`, e (se usato) stile basemap Protomaps; nessuna dipendenza obbligatoria per la vista lista.

## Struttura generale della pagina

### 1) Head e risorse
- `<!DOCTYPE html>`, `<html lang="it">`, meta `charset`/`viewport`.
- `<title>` es. `Ricerca AI – ScroogeMap`.
- Font Google e `CSS/style.css` come nelle altre pagine.
- Se la vista mappa usa la stessa logica di `mappa.html`: includere CSS/JS di MapLibre e `pmtiles` (e inizializzazione mappa al momento opportuno, es. lazy quando si apre la vista mappa o quando arrivano risultati).

### 2) Header (uguale a `mappa.html`)
- Riuso del blocco `<header>` con `nav`: brand verso `index.html`, menu `Mappa` (`mappa.html`), `Budgeting` (`budgeting.html`), `Ricerca AI` (pagina corrente, es. `ricerca-ai.html` o `#` con stato attivo), CTA `Impostazioni profilo` (`impostazioni.html`).
- Stessa struttura/classi per coerenza; eventuale indicazione voce corrente accessibile (es. `aria-current="page"` sul link `Ricerca AI`).

### 3) Footer (uguale al resto del sito)
- Stesso `<footer>` con `.wrap` e i due `<span>`: `© 2026 ScroogeMap · Ingegneria del Software 2026/2027` e autori.
- Layout pagina con `body` a colonna flessibile (`min-height: 100dvh`, `main { flex: 1 }`) per mantenere il footer in basso anche con pochi risultati/stato loading.

### 4) Main
- `<main>` con `.wrap` e sezioni nell’ordine richiesto: intro, ricerca AI, viste (subnav), risultati.

## Sezioni e contenuti

### 1) Sezione intro
- Blocco introduttivo (es. `.ai-search-intro`, analogo a `.budget-intro`/`.settings-intro`).
- **Titolo**: `h1` chiaro e descrittivo (es. `Ricerca AI` o `Confronta i prezzi con la Ricerca AI`).
- **Sottotitolo**: `.lead` che spiega l’azione (es. “Inserisci un prodotto e confronta i prezzi disponibili a Trento”).

### 2) Sezione ricerca AI (form)
- Card (`.card`) con `<form id="ai-search-form" role="search">` (submit intercettato via JS, nessun backend).
- Campo principale: **nome del prodotto** di cui comparare i prezzi a Trento.
  - `label` associato + `input type="search"`/`text` (es. `id="product-query"`, placeholder “Es. latte, benzina, parcheggio…”), `required`.
- Pulsante submit `.btn` (es. “Cerca prezzi”).
- Comportamento mock:
  - su submit valido: salva la query, imposta stato `loading`, mostra la sezione risultati in modalità caricamento; dopo un timeout simulato (es. 1–1.5s) genera/filtra risultati mock coerenti con la query e passa a stato `results` (o `empty` se nessuna corrispondenza).
  - disabilitare/gestire il submit durante il loading per evitare invii multipli.

### 3) Sezione viste (subnav)
- Subnav per selezionare la modalità di visualizzazione risultati: **`Lista`** e **`Mappa`**.
  - Riuso pattern `budgeting.html`: `<nav class="subnav" aria-label="Vista risultati">` con due `<button type="button" class="tab-btn" data-results-view="list|map">` e stato attivo `.is-active` + `aria-pressed`.
  - Vista di default: `Lista` (o come definito); il cambio vista non rilancia la ricerca, cambia solo la presentazione dei risultati correnti.
- La subnav può essere sempre visibile oppure mostrata insieme all’area risultati; deve risultare chiaramente associata ai risultati.

### 4) Sezione risultati
- Contenitore/card area risultati con `aria-live="polite"` per aggiornamenti di stato.
- **Stati UI**:
  1. **Iniziale (idle)**: messaggio guida (es. “Inserisci un prodotto per avviare la ricerca”).
  2. **Loading**: mostrato subito dopo il submit; indicatore di caricamento (spinner/scheletro/placeholder testuale animato o statico) con messaggi di processo (es. “Ricerca in corso…”, “Confronto dei prezzi a Trento…”). Nessun risultato visibile in questo stato.
  3. **Risultati (list view)**: se selezionata la vista `Lista`, mostrare i risultati con la **stessa grafica dei “Nodi disponibili” in `mappa.html`**.
     - Riutilizzare struttura/classi: elenco `.nodes-list` di card `.node-card` con `h3` nome, `.node-address`, `.node-meta` (prezzo, distanza), `.tags` (categorie) e stato selezionato `.is-selected`.
     - Ogni risultato rappresenta un punto vendita/offerta per il prodotto cercato (nome nodo, indirizzo, prezzo del prodotto/offerta, distanza, tag).
     - Dettagli: riusare il pattern di `mappa.html` (vista lista/dettagli alternata nello stesso contenitore con “Torna alla lista”) oppure pannello dettagli associato; il click su un risultato mostra i dettagli (aperto, segnalazioni utenti, altri dettagli + info prezzo/prodotto). Comportamento coerente e interno al widget/area risultati.
  4. **Risultati (map view)**: se selezionata la vista `Mappa`, mostrare una **mappa widget** (non a tutto schermo) con i risultati come **pin**.
     - Contenitore `.card`/wrapper con altezza fissa o `aspect-ratio` (es. 360–420px) e `#ai-results-map` che occupa lo spazio del widget.
     - Logica mappa **come in `mappa.html`**: MapLibre + `pmtiles://assets/trento.pmtiles`, inizializzazione/centro su Trento, **HTML markers** (`.map-pin`) posizionati con `lng/lat`, mappa `id -> marker`, sincronizzazione selezione.
     - Click su pin: seleziona il risultato e mostra i relativi dettagli (in pannello/lista affiancata o nello stesso componente risultati, in modo coerente con la vista lista e fruibile nel widget).
     - Al cambio filtri impliciti/nuova ricerca: aggiornare/rimuovere i pin per riflettere i risultati correnti; `fitBounds`/`easeTo` opzionale per inquadrare i marker.
  5. **Empty**: query senza risultati mock → messaggio “Nessun risultato trovato per \”<prodotto>\” a Trento”.

## Dati mock e logica
- Definire un dataset mock di risultati/nodi (array JS) con campi compatibili con `mappa.html`: `id, name, address, price/priceLabel (prezzo del prodotto cercato), distanceKm, categories, openStatus, openInfo, reports, details, lng, lat`.
- Strategia mock: associare alcune keyword/prodotti a sottoinsiemi di risultati (es. “latte” → alimentari, “benzina” → carburanti, “parcheggio” → parcheggi) con fallback generico; generare/etichettare `priceLabel` in funzione del prodotto cercato.
- Stato applicativo: `{ query, status: 'idle'|'loading'|'results'|'empty', resultsView: 'list'|'map', results: [], selectedId }`.
- `render()`/funzioni dedicate per: stato loading, lista risultati, dettagli, e creazione/aggiornamento marker sulla mappa widget (inizializzare la mappa una sola volta e riutilizzarla).

## Stili CSS da introdurre/estendere
- **Intro/form**: `.ai-search-intro`, layout form ricerca (input + bottone, responsive) riusando stili `.field`/input esistenti.
- **Risultati**: `.results-section`, stati `.is-loading`/placeholder (spinner o animazione semplice con `--gold`/`--teal`), contenitore lista riusando `.nodes-list`, `.node-card`, `.tags`, `.details-*`.
- **Mappa widget**: `.results-map-widget` con altezza/`aspect-ratio` definita, `#ai-results-map` a `width/height: 100%`, bordo/raggio/overflow come card; riuso `.map-pin` e stati selezionati.
- **Layout viste**: gestione visibilità pannelli lista/mappa in base a `data-results-view`/`[hidden]`; su mobile stack verticale e controlli full-width.
- **Tema scuro/responsive**: variabili esistenti, contrasto adeguato per loading, card e mappa.

## Accessibilità e semantica
- `h1` unico per il titolo pagina; `h2` per sezione risultati/intro blocchi se presenti.
- Form con `label`/`aria-label`, stato loading annunciato (`aria-live`, `aria-busy` sul contenitore risultati o testo di stato).
- Subnav viste con `aria-pressed`/stato attivo chiaro; pannelli lista/mappa associabili con `aria-controls` se utile.
- Risultati lista come bottoni/elementi attivabili da tastiera con focus visibile; mappa con `aria-label` e controlli MapLibre accessibili.

## Verifica finale
1. Header uguale a `mappa.html` e footer uguale al resto del sito.
2. Intro con titolo e sottotitolo; form ricerca prodotto con submit funzionante (mock).
3. Subnav viste `Lista`/`Mappa` con switch corretto.
4. Flusso risultati: loading visibile dopo submit, poi risultati in lista (grafica “Nodi disponibili”) o in mappa widget con pin e logica analoga a `mappa.html`.
5. Mappa widget non full-screen, sincronizzata con risultati/selezione; nessun backend (solo mock e, per la mappa, caricamento `assets/trento.pmtiles` via HTTP).
