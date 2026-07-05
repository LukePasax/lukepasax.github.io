# Sessione 2026-06-06 — Deploy e sviluppo Archivio Personale

## Punto di partenza
Handoff da sessione precedente: app `archivio-personale.html` funzionante localmente ma bloccata da CORS. Soluzione: hostarla su GitHub Pages.

## Attività svolte

### Deploy su GitHub Pages
- Caricato `archivio-personale.html` nel repo `lukepasax.github.io`
- Aggiunta origine `https://lucapasini.com` nelle origini OAuth autorizzate su Google Cloud Console (il sito usa un DNS custom)
- Aggiunto `eldargaming80@gmail.com` come utente di test OAuth (app non verificata da Google)

### API key Anthropic
- L'utente ha acquistato $5 di crediti su console.anthropic.com
- La chiave non può essere hardcodata nel codice (GitHub Push Protection la blocca)
- Soluzione: campo input nell'app, chiave salvata in `localStorage`
- Header richiesti: `x-api-key`, `anthropic-version: 2023-06-01`, `anthropic-dangerous-direct-browser-access: true`
- Model aggiornato: `claude-sonnet-4-20250514` → `claude-sonnet-4-6`

### PWA
- Aggiunti `manifest.json` e `sw.js` per installabilità
- Meta tag Apple per iOS
- Su iPhone: Condividi → Aggiungi alla schermata Home

### Bug fix: PDF 400
- PDF protetti/firmati non accettati da Claude come `document`
- Soluzione: PDF.js 3.11.174 per renderizzare ogni pagina come JPEG → inviare come immagini

### Feature: revisione prima del caricamento
- Dopo analisi Claude, pannello con dropdown categoria e campo nome file modificabili
- Caricamento avviene solo dopo conferma utente

### Feature: sottocartelle per anno
- Ogni file caricato va in `Categoria/Anno/` 
- Anno estratto dal nome file (pattern `YYYY-`)
- Cartella anno creata automaticamente se non esiste

### Feature: Riclassifica
- Tab dedicata con filtri: Tutto / Categoria / Anno
- Lista file da Drive (iterando categorie → sottocartelle anno → file)
- Analisi batch con progresso visibile
- Lista risultati con checkbox: vecchio path → nuovo path
- Applica modifiche: PATCH Drive (rinomina + sposta)
- Scope OAuth ampliato da `drive.file` a `drive`

### Nuova categoria
- Creata cartella "Certificati Medici" su Drive via MCP connector
- ID: `1CvbncUmpBvz57ex90zkhf8MKK_lKPCqm`

## Versione finale: v9

## Punto di stop
App completamente funzionante. Utente soddisfatto. Nessun task aperto.
