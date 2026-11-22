# Stato Progetto — aonracing.net

> **File di memoria e allineamento per lo sviluppo di aonracing.net.**  
> *Questo file deve essere consultato all'inizio di ogni sessione e aggiornato al termine di ogni modifica significativa.*

---

## 1. Contesto Generale & Repository

- **Progetto:** Sito web ufficiale di [AON Racing](https://aonracing.net/) (esperienze di guida in pista su monoposto Formula Predator's, prove libere e voucher regalo).
- **Repository GitHub:** `https://github.com/aonracing/aonracing.net.git`
- **Branch attivo di produzione:** `main`
- **Ambiente:** Gestito e sincronizzato in modo ibrido multipiattaforma tra **Windows** e **Fedora Linux**.
- **Autore commit Git:** `aonracing <info@aonracing.net>`
- **Hosting:** GitHub Pages con dominio personalizzato `aonracing.net` (`CNAME` configurato nella root).
- **Stack Tecnologico:**
  - HTML5 semantico
  - Tailwind CSS (via CDN)
  - Vanilla JavaScript puro (zero framework pesanti, zero dipendenze a pagamento)
  - Grafica e stile: Tema scuro/motorsport ad alto contrasto (Brand Red `#e10600`, Brand Green `#00a859`, Dark `#09090b`, Card `#121216`), font *Inter* e *Montserrat* (racing italic).
  - CMS: Sveltia/Decap CMS headless per la gestione autonoma dei contenuti da parte del team (`admin/index.html`).

---

## 2. Struttura del Progetto & File Principali

```
├── .antigravity/
│   └── rules.md               # Regole di continuità operativa dell'agente
├── admin/
│   ├── config.yml             # Schema e collezioni CMS Sveltia
│   └── index.html             # Pannello di amministrazione web
├── assets/                    # Foto e video ufficiali compressi (WebP / WebM / MP4)
├── archivio_extra/            # Loghi vettoriali e asset fotografici secondari
├── data/
│   └── site.json              # File JSON sorgente dati (testi, pacchetti, circuiti, FAQ, contatti)
├── CNAME                      # aonracing.net
├── google12ce367aae20db2e.html# Verifica Google Search Console
├── index.html                 # Single page application completa del sito
├── robots.txt                 # Direttive per i crawler web
├── sitemap.xml                # Mappa del sito per l'indicizzazione SEO
├── STATO_PROGETTO.md          # Questo file di stato
└── .gitignore                 # File esclusi da Git (*.bak, *.backup.html, ecc.)
```

---

## 3. Tracciamenti, Analytics & GDPR Attivi

### Cloudflare Web Analytics
- **Token:** `196a2a8e40864a06824dc30286fbecb3`
- **Posizione:** In fondo a `index.html`, appena prima della chiusura `</body>`.
- **Caratteristiche:** Cookie-free, completamente anonimo, non traccia indirizzi IP né dati personali. Conforme al GDPR senza necessità di consenso preventivo.

### Google Tag (G-R63X5YEYKT) & Google Consent Mode v2
- **ID Misurazione:** `G-R63X5YEYKT` (Google Analytics 4 e predisposizione conversioni Google Ads).
- **Sequenza rigida in `<head>`:**
  1. **Consent Mode Default:** Tutti i permessi partono bloccati (`denied`):
     ```javascript
     gtag('consent', 'default', {
       'ad_storage': 'denied',
       'ad_user_data': 'denied',
       'ad_personalization': 'denied',
       'analytics_storage': 'denied',
       'wait_for_update': 500
     });
     ```
  2. **Controllo immediato storage:** Lettura sincrona di `localStorage.getItem('aon_cookie_consent')`. Se l'utente ha già salvato preferenze, invoca `gtag('consent', 'update', ...)` prima dell'inizializzazione del tag.
  3. **Script async Google Tag:** `https://www.googletagmanager.com/gtag/js?id=G-R63X5YEYKT`
  4. **Configurazione:** `gtag('js', new Date()); gtag('config', 'G-R63X5YEYKT');`

### Banner Cookie & Gestione Consenso (GDPR / Garante Privacy)
- **Banner Ancorato in Basso (`#cookie-banner`):**
  - Pulsante **&times;** di chiusura in alto a destra (equivale a rifiuto dei cookie non necessari).
  - Testo sintetico e chiaro con link alla Privacy Policy.
  - **3 pulsanti con uguale peso visivo e dimensione:** *"Accetta tutti"*, *"Rifiuta"* e *"Personalizza"*.
- **Modale Centro Preferenze (`#cookie-prefs-modal`):**
  - Categoria *Tecnici & Necessari* (Sempre Attivi).
  - Toggle indipendente per *Statistiche (Google Analytics)*.
  - Toggle indipendente per *Marketing (Google Ads)*.
  - Pulsanti rapidi: *"Rifiuta tutti"*, *"Accetta tutti"*, *"Salva preferenze"* e *"Reimposta scelta iniziale"*.
- **Footer del sito:**
  - Link/pulsante persistente **"Preferenze Cookie"** per revocare o modificare la scelta in qualsiasi momento.
  - Modale informativa **"Privacy & Cookie Policy"** aggiornata e conforme.

---

## 4. Modifiche, Bugfix & Changelog

### Cronologia Commit di questa Sessione:
| Commit | File Coinvolti | Descrizione Sintetica |
|---|---|---|
| `357201f` | `index.html` | **GDPR & Consent Mode v2:** Implementazione Google Consent Mode v2 (`default: denied`), lettura storage `aon_cookie_consent`, banner conforme Garante Privacy con 3 pulsanti paritetici, modale preferenze con toggle indipendenti e pulsante "Preferenze Cookie" nel footer. |
| `7055f38` | `index.html` | **Mobile Bugfix FOUC:** Risolto micro-lampo da smartphone con regole CSS native `display: none` / `is-active`, inizializzazione immediata (`DOMContentLoaded`), safe-area padding per iOS/Android e pulsante "Reimposta scelta iniziale". |
| `8d4e5f6` | `.antigravity/rules.md`, `STATO_PROGETTO.md` | **Documentazione & Continuità:** Creazione regole permanenti per Antigravity e stato progetto dettagliato per sincronizzazione Windows / Linux. |

### Dettaglio Interventi:
1. **Implementazione GDPR e Consent Mode v2:**
   - File: `index.html`
   - Inserita la logica di blocco preventivo dei cookie con aggiornamento dinamico al click dell'utente.
   - Creata la UI racing dark per banner, centro preferenze e policy.
2. **Fix Lampo Mobile (FOUC — Flash of Unstyled Content):**
   - File: `index.html`
   - *Sintomo:* Su mobile il banner compariva per un secondo e spariva da solo.
   - *Causa:* L'utente aveva già accettato i cookie durante i test precedenti. A ogni reload, nei millisecondi prima che la CDN di Tailwind venisse interpretata, il browser mobile disegnava l'HTML grezzo del banner; non appena Tailwind e lo script leggevano il consenso già salvato, il banner veniva nascosto, generando un micro-lampo ingannevole. Inoltre l'inizializzazione dipendeva dal completamento della chiamata asincrona `site.json`.
   - *Risoluzione:*
     - Aggiunta regola CSS nativa in `<style>`: `#cookie-banner { display: none; }` e `#cookie-banner.is-active { display: block !important; }`.
     - Inizializzazione del banner resa istantanea (`DOMContentLoaded`), indipendente da `site.json`.
     - Aggiunto supporto safe-area per la barra inferiore dei browser iOS Safari e Android Chrome.
     - Aggiunto il tasto *"Reimposta scelta iniziale"* nel centro preferenze per consentire il re-test immediato.

---

## 5. Stato dei Lavori, Problemi Aperti & Prossimi Step

- [x] Allineamento repository Git e verifica remota GitHub.
- [x] Configurazione Google Tag `G-R63X5YEYKT` + Consent Mode v2.
- [x] Banner conforme Garante Privacy con 3 pulsanti di pari rilievo.
- [x] Pannello personalizzazione preferenze cookie.
- [x] Link "Preferenze Cookie" nel footer e aggiornamento Privacy Policy.
- [x] Risoluzione flash/scomparsa anomala su dispositivi mobile.
- [x] Creazione regole continuità `.antigravity/rules.md`.
- [x] Verifica assenza di conflitti in `.gitignore`.

### Prossimi Step Concordati (lato piattaforma Google Ads)
1. **Verifica dati GA4:** Monitorare l'arrivo delle visite in tempo reale su Google Analytics accettando i cookie dal banner.
2. **Collegamento Google Ads:** Nel pannello di Google Ads (quando verranno create le prime campagne), collegare la proprietà GA4 `G-R63X5YEYKT` per importare automaticamente il pubblico e i dati di conversione.
3. **Eventi di Conversione Dedicati (Opzionale):** Associare eventi personalizzati (es. `generate_lead`) sui clic diretti a WhatsApp o sul download del voucher per ottimizzare il ritorno sull'investimento pubblicitario.
