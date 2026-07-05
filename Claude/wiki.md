# Wiki — lukepasax.github.io

## Progetto principale: Archivio Personale

App PWA hostata su GitHub Pages, dominio custom **`archive.lucapasini.com`** (sottodominio dedicato dal 2026-07-05 — prima era sull'apex `lucapasini.com`, ora occupato dalla dashboard personale, vedi `H:\Progetti\Claude\wiki.md`). Archivia documenti personali su Google Drive con classificazione automatica via Claude API.

### File
- `index.html` — app completa (single-file HTML+CSS+JS), rinominata da `archivio-personale.html` quando è diventata la pagina principale del sottodominio.
- `manifest.json` — PWA manifest (`start_url: "/"`)
- `sw.js` — service worker minimale (no cache, solo installabilità)
- `CNAME` — `archive.lucapasini.com`

### OAuth
Il progetto GCP "Archivio Personale" ha una whitelist di origini JavaScript autorizzate per l'OAuth Google — deve includere `https://archive.lucapasini.com` (aggiornata il 2026-07-05 dopo il cambio di sottodominio).

### Stack
- **Frontend**: HTML/CSS/JS vanilla, DM Mono + DM Serif Display (Google Fonts)
- **AI**: Claude API (`claude-sonnet-4-6`) chiamata direttamente dal browser
- **Storage**: Google Drive API v3 (multipart upload, PATCH per rinomina/spostamento)
- **Auth**: Google Identity Services OAuth2 — scope `drive` (completo)
- **PDF rendering**: PDF.js 3.11.174 (CDN) per convertire PDF → immagini JPEG prima di passarle a Claude

### Configurazione Google OAuth
- **Client ID**: `587675640690-djilvb3nn1o446kctj6pvg9lu8drh1v2.apps.googleusercontent.com`
- **Progetto GCP**: "Archivio Personale" — account `eldargaming80@gmail.com`
- **Origini autorizzate**: `https://claude.ai`, `https://lukepasax.github.io`, `https://lucapasini.com`
- **Utenti di test**: `eldargaming80@gmail.com`

### Struttura Google Drive
Root: **"Archivio Personale"** — ID: `1nl1khW2hu99KjgkIgJUHrhuUreXnMBxa`

| Categoria | Folder ID |
|-----------|-----------|
| Spese Sanitarie | `1Qe67w_CBtnPd_Fr49_pk6zLXs2y0P9Tb` |
| Certificati Medici | `1CvbncUmpBvz57ex90zkhf8MKK_lKPCqm` |
| Buste Paga | `1tX_wxHNsICi3HVWdQZH4mAG7dBAE8Qdl` |
| Documenti d'Identità | `1zOKVoJNHm1g7sM4QtVRqLaMV2SHfyi-a` |
| Dichiarazione dei Redditi | `16CTZkDW_8O-hyKWTPbrvnuTfgq5e_7h0` |
| Assicurazioni | `1GLeCUEE4ppD7I8yq1vVdBqgqHU516L7h` |
| Bollette & Utenze | `1lwIewc4TotMVkMb8GmoROfFsUkpmzyaL` |
| Banca & Finanze | `1dclOLYrKCKl_SDGjByuMhz0bJS7JeCoV` |
| Contratti | `1CmnmpcHQqDYYxjKxHgOaPUGAPA6yy3RU` |
| Altro | `1cQ5ANxiVjxn5tjyJWqhjT9fNaxh8jdvb` |

Ogni categoria ha sottocartelle per anno (`2024/`, `2025/`, ecc.) create automaticamente dall'app al momento del caricamento.

### Flusso "Archivia"
1. Utente seleziona file (immagine o PDF)
2. Se immagine: compressa a max 1600px, qualità 0.82
3. Se PDF: convertito in immagini JPEG via PDF.js (una per pagina)
4. Claude API analizza e restituisce JSON `{categoria, nomeFile, descrizione, certezza}`
5. Pannello di revisione: utente può modificare categoria e nome file
6. OAuth Google (popup una tantum, token valido ~1h)
7. Crea sottocartella anno se non esiste (`getOrCreateYearFolder`)
8. Upload multipart su Drive

### Flusso "Riclassifica"
1. Utente sceglie filtro: Tutto / Categoria / Anno
2. App lista tutti i file in Archivio Personale (itera categorie → sottocartelle anno → file)
3. Per ogni file: scarica da Drive, converte se PDF, manda a Claude
4. Mostra lista proposte con vecchio path → nuovo path
5. Utente seleziona quali applicare → PATCH Drive (rinomina + sposta)

### API key
Salvata in `localStorage` con chiave `anthropic_api_key`. Mai nel codice sorgente.

### Tema chiaro/scuro
- Bottone ☀️/🌙 nell'header — togola `body.light`
- Preferenza salvata in `localStorage("archivio_theme")`
- CSS custom properties su `:root` (dark) e `body.light` (light)

### driveGet
Timeout 15s con AbortController. Lancia errore su `!res.ok` con corpo della risposta per debug.

### Versioning
Versione visibile nell'header (es. `v17`). Incrementare ad ogni modifica prima del commit.
