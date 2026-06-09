# Generatore Schede

**Applicazione desktop per fisioterapisti** — gestione pazienti, creazione schede di esercizi personalizzate, versioning, sincronizzazione cloud ed esportazione PDF professionale.

> Desktop application for physiotherapists to manage patients, build versioned exercise sheets with photos and custom tables, and export print-ready PDFs.

---

## Funzionalità

### Editor scheda
- **Layout A4 WYSIWYG** — anteprima identica alla stampa, con zoom auto-fit, controllo densità (Compatta · Media · Ariosa) e posizione tabella generale (prima/dopo note).
- **Blocchi esercizio configurabili** — titolo, descrizione, fino a 4 metriche libere (Serie, Reps, Carico, Recupero o qualsiasi etichetta), foto multiple per esercizio.
- **Tabelle 100% custom** — colonne, righe e intestazioni completamente personalizzabili per ogni esercizio + una tabella generale opzionale (es. programma settimanale).
- **Foto esercizi** — picker con anteprima dalla cartella di lavoro o upload da file, ridimensionamento automatico.
- **Riordino e duplicazione** — sposta esercizi su/giù, aggiungi nuovo esercizio da libreria con un click.

### Pazienti e schede
- **Anagrafica pazienti** — nome, data di nascita, sport/area trattamento, stato (OK / Attenzione), note.
- **Schede multiple per paziente** — codice automatico `SA-YYYY-NNNN`, date emissione/validità.
- **Cronologia versioni** — snapshot automatico ad ogni salvataggio (max 20 per scheda), ripristino con un click.
- **Auto-save** — salvataggio in background dopo 6 secondi di inattività.
- **Guard chiusura** — avviso e opzione "Salva ed esci" se ci sono modifiche non salvate.

### PDF e stampa
- **Esportazione nativa** — Chromium `printToPDF`, salvataggio in `workspace/generated/` con timestamp.
- **Multi-pagina** — margini ripetuti su ogni pagina, numero di pagina automatico (`Pagina X di Y`), `break-inside: avoid` su esercizi e footer, intestazioni tabella ripetute.
- **Firma e dati studio** — sezione finale con linea firma, dati clinico + studio (toggle on/off).

### Workspace e backup
- **Cartella di lavoro scelta dall'utente** — database SQLite + `images/` + `logo/` in qualsiasi path locale. Nessuna cartella creata prima del consenso esplicito.
- **Google Drive sync** — backup ZIP completo (DB + immagini + logo), sync automatico all'avvio e alla chiusura, conflict resolution timestamp-based.
- **Watcher cartelle** — modifiche a `images/` e `logo/` rilevate in tempo reale.
- **Import database** — copia di `.db` esistente o ripristino da backup Drive.

### UI/UX
- **Comando palette ⌘K** — ricerca trasversale su pazienti, schede, esercizi e comandi.
- **Tema chiaro/scuro + 12 accent color** (Indigo, Cobalt, Emerald, Teal, Amber, Coral, Crimson, Violet, Rose, Lime, Slate, Graphite).
- **Logo studio caricabile** — visualizzato nella scheda stampata.
- **Scorciatoie tastiera** — `⌘S` salva, `⌘P` esporta PDF, `⌘K` palette, `N` nuovo esercizio, `[` `]` toggle pannelli.

### Multi-piattaforma
Windows · macOS (Intel + Apple Silicon) · Linux

---

## Download e installazione

Scarica il pacchetto dalla pagina [**Releases**](../../releases/latest):

| Sistema operativo | File |
|---|---|
| Windows | `.exe` (installer NSIS) |
| macOS Intel | `.dmg` (x64) |
| macOS Apple Silicon | `.dmg` (arm64) |
| Linux | `.AppImage` |

### Windows
Esegui il file `.exe` e segui il wizard.

### macOS
Apri il `.dmg`, trascina l'app in Applicazioni.
> L'app non è notarizzata Apple: al primo avvio vai su **Impostazioni di sistema → Privacy e sicurezza** e consenti l'esecuzione.

### Linux
```bash
chmod +x Generatore-Schede-*.AppImage
./Generatore-Schede-*.AppImage
```

---

## Primo avvio

1. **Scegli la cartella di lavoro** — qualsiasi directory locale. Verranno create `images/` e `logo/` solo dopo il passo successivo.
2. **Scegli come iniziare**:
   - **Nuovo database** — crea un DB vuoto nella cartella.
   - **Importa `.db`** — copia un database SQLite esistente.
   - **Da Google Drive** — autentica e ripristina da backup remoto (richiede credenziali OAuth 2.0 — vedi sotto).
3. **Compila Studio + Clinico** in `Inspector → Studio` (intestazione + firma).
4. **Aggiungi pazienti** dalla sidebar e inizia a creare schede.

### Google Drive (opzionale)
1. [Google Cloud Console](https://console.cloud.google.com/) → API e servizi → Credenziali → crea client OAuth 2.0 di tipo **App per computer**.
2. In app: `Impostazioni → Area di lavoro → Drive credenziali` → incolla Client ID + Client Secret.
3. `Collega Google` → autorizza nel browser.
4. La sync è automatica all'avvio e alla chiusura. Sync manuale dal pulsante toolbar.

---

## Build dal sorgente

### Requisiti
- [Node.js](https://nodejs.org/) 20+
- Python 3 e build tools C++ (per compilare `better-sqlite3`)
- ImageMagick + (su macOS) `iconutil` e `sips` per `npm run gen-icon`

```bash
# Clona il repository
git clone https://github.com/lamicopalmato/generatore-schede.git
cd generatore-schede

# Installa le dipendenze (compila better-sqlite3 automaticamente)
npm install

# Avvia in modalità sviluppo
npm start

# Genera l'icona (squircle macOS HIG + Windows ICO)
npm run gen-icon

# Genera il pacchetto per il sistema corrente
npm run dist
```

### Build per architettura specifica (macOS)
```bash
npx electron-builder --mac --arm64   # Apple Silicon
npx electron-builder --mac --x64     # Intel
```

---

## Stack tecnologico

| Componente | Tecnologia |
|---|---|
| Runtime | [Electron](https://www.electronjs.org/) 30 |
| Database | [better-sqlite3](https://github.com/WiseLibs/better-sqlite3) (WAL mode) |
| Cloud backup | [Google Drive API v3](https://developers.google.com/drive) + ZIP via `adm-zip` |
| Packaging | [electron-builder](https://www.electron.build/) |
| Frontend | HTML · CSS Custom Properties · JavaScript vanilla |
| PDF export | Chromium `printToPDF` |

---

## Struttura del progetto

```
generatore-schede/
├── main.js              # Processo principale Electron (IPC, DB, Drive, watchers, close guard)
├── preload.js           # Context bridge renderer ↔ main
├── renderer/
│   ├── index.html       # Markup UI
│   ├── app.js           # Logic + binding backend (~1900 righe)
│   └── styles.css       # Design system, temi, print
├── assets/
│   ├── logo.png         # Logo monocromatico (silhouette)
│   ├── icon.png         # App icon 512×512 (Apple HIG)
│   ├── icon.icns        # macOS multi-size
│   ├── icon.ico         # Windows multi-size
│   └── icon.svg
├── scripts/
│   └── gen-icon.js      # Generatore icona da logo.png (squircle + composite)
└── .github/workflows/   # Pipeline CI/CD
```

---

## Schema database

```sql
patients(id, name, birthDate, notes, createdAt)
schede(id, patientId, title, data, createdAt, updatedAt)
   -- data = JSON { subtitle, exercises[], footerNote, generalTable, code, emittedAt,
   --              validUntil, density, tablePosGeneral, showSign, ... }
exercise_library(id, name, reps, notes, createdAt)
scheda_versions(id, schedaId, label, data, createdAt)   -- snapshot pre-save
app_settings(key, value)                                 -- doctor, studio, ecc.
```

---

## CI/CD

Il workflow `.github/workflows/release.yml` si attiva al push di un tag `v*` e produce i pacchetti per tutte le piattaforme, pubblicandoli come release.

---

## Licenza

Distribuito sotto licenza **MIT**. Vedi [`LICENSE`](LICENSE).
