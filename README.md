# 🚬 Sigarette Live

**Sigarette Live** è un'applicazione web progressive/standalone per il tracciamento e il monitoraggio personale del consumo di sigarette in tempo reale.

---

## 🎯 Scopo del Progetto
Permettere l'inserimento rapido tramite smartphone (o scorciatoia widget HTTP Shortcuts) di ogni sigaretta fumata, registrando automaticamente la data, l'ora, le coordinate GPS e il trigger/motivo, fornendo statistiche dettagliate e indicatori di progresso giornaliero.

---

## 🏗️ Architettura e Tecnologie

- **Frontend**: Single-Page Application (HTML5, Vanilla JavaScript, CSS custom dark mode).
- **Backend / Database**: Google Sheets accessibile tramite proxy **Google Apps Script (Web App)**.
- **Hosting & CDN**: Pubblicato su **Cloudflare Pages / Workers** ([`sigarette.korradof.workers.dev`](https://sigarette.korradof.workers.dev) e dominio personalizzato [`corradofarina.net`](https://corradofarina.net/)).
- **Librerie**:
  - `Chart.js`: Grafici del consumo giornaliero, orario, media mobile MA7 e confronto settimanale.
  - `Leaflet.js` con cartografia **Google Maps**: Mappa interattiva e clustering geografico dei luoghi in cui si fuma.

---

## ✨ Funzionalità Principali

1. **Registrazione Sigaretta (`+1 Sigaretta`)**: Salva data, ora, latitudine, longitudine e link a Google Maps.
2. **Scelta del Trigger / Motivo**: Popup rapido opzionale per associare la causa (*Caffè/pasto, Stress, Noia, Automatica, X*).
3. **Obiettivo Adattivo Giornaliero**: Ricalcolato automaticamente come ⌊MA7⌋ − 1 (con minimo di 5 sigarette).
4. **Notifiche Browser**: Avviso al raggiungimento di una soglia personalizzata di sigarette giornaliere.
5. **Protezione PIN**: Schermata di blocco con PIN numerico a 4 cifre.
6. **Mappa & Clustering Posizioni**: Mappatura dei punti GPS con distinzione tra consumo a *Casa* e *Fuori Casa*.

---

## 🚀 Ottimizzazioni di Prestazioni Applicate

Per eliminare i tempi di attesa di Google Apps Script (2–4 secondi per richiesta) e rendere l'interfaccia **istantanea (0 ms)**, sono state implementate le seguenti tecniche:

1. **Stale-While-Revalidate Caching (LocalStorage)**:
   - I dati dell'ultimo caricamento vengono salvati nel `localStorage` del browser.
   - All'apertura dell'app, contatori e grafici vengono renderizzati **immediatamente** senza attendere la rete.
   - Il caricamento da Google Apps Script avviene in background in modalità *silent sync*.

2. **Aggiornamento Ottimistico dell'Interfaccia (Optimistic UI)**:
   - Alla pressione del tasto `+1 Sigaretta`, il contatore "Oggi", la barra obiettivo e i grafici si aggiornano **istantaneamente**.
   - Il toast di conferma viene mostrato subito, mentre il salvataggio remoto su Google Sheets prosegue in background.

3. **Instant Unlock PIN**:
   - Se il PIN inserito corrisponde all'ultimo PIN valido verificato, l'app si sblocca **all'istante** dal cache locale verificando la sessione in background.

4. **Resource Hints & DNS Prefetch**:
   - Aggiunti i tag `<link rel="preconnect">` per velocizzare la connessione iniziale ai server CDN e a Google Apps Script.