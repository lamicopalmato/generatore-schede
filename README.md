# Schede Esercizi

**Applicazione desktop per fisioterapisti** — gestione pazienti, creazione schede di esercizi personalizzate ed esportazione PDF professionale.

> Desktop application for physiotherapists to manage patients, create custom exercise sheets and export professional PDFs.

---

## Funzionalità

- **Gestione pazienti** — archivio completo con anagrafica e storico schede
- **Schede esercizi** — editor visuale con blocchi esercizio configurabili (serie, ripetizioni, note, foto)
- **Libreria esercizi** — raccolta riutilizzabile di esercizi personalizzati
- **Esportazione PDF** — stampa nativa ad alta qualità direttamente dall'app
- **Logo e profilo medico** — intestazione personalizzabile con logo dello studio e dati del professionista
- **Temi colore** — 6 palette colore (Blu, Verde, Viola, Acqua, Ambra, Bordeaux)
- **Google Drive** — backup e sincronizzazione automatica del database
- **Sincronizzazione immagini** — le foto aggiunte alla cartella workspace appaiono nell'app in tempo reale, senza riavvio
- **Multi-piattaforma** — Windows, macOS (Intel e Apple Silicon), Linux

---

## Download e installazione

Scarica il pacchetto per il tuo sistema operativo dalla pagina [**Releases**](../../releases/latest):

| Sistema operativo | File |
|---|---|
| Windows | `.exe` (installer NSIS) |
| macOS Intel | `.dmg` (x64) |
| macOS Apple Silicon | `.dmg` (arm64) |
| Linux | `.AppImage` |

### Windows
Esegui il file `.exe` e segui il wizard di installazione.

### macOS
Apri il file `.dmg`, trascina l'app nella cartella Applicazioni.
> L'app non è notarizzata Apple: al primo avvio vai su **Impostazioni di sistema → Privacy e sicurezza** e consenti l'esecuzione.

### Linux
Rendi eseguibile il file e avvialo:
```bash
chmod +x Schede-Esercizi-*.AppImage
./Schede-Esercizi-*.AppImage
```

---

## Build dal sorgente

### Requisiti
- [Node.js](https://nodejs.org/) 20+
- Python 3 e build tools C++ (per compilare `better-sqlite3`)

```bash
# Clona il repository
git clone https://gitea.lamicopalmato.cloud/lamicopalmato/generatore-schede.git
cd generatore-schede

# Installa le dipendenze (compila automaticamente better-sqlite3)
npm install

# Avvia in modalità sviluppo
npm start

# Genera il pacchetto per il sistema corrente
npm run dist
```

### Build per architettura specifica (macOS)
```bash
npx electron-builder --mac --arm64   # Apple Silicon
npx electron-builder --mac --x64    # Intel
```

---

## Stack tecnologico

| Componente | Tecnologia |
|---|---|
| Runtime | [Electron](https://www.electronjs.org/) 30 |
| Database | [better-sqlite3](https://github.com/WiseLibs/better-sqlite3) |
| Cloud backup | [Google Drive API v3](https://developers.google.com/drive) |
| Packaging | [electron-builder](https://www.electron.build/) |
| Frontend | HTML · CSS Custom Properties · JavaScript vanilla |

---

## Struttura del progetto

```
generatore-schede/
├── main.js              # Processo principale Electron (IPC, DB, Drive, watchers)
├── preload.js           # Context bridge renderer ↔ main
├── renderer/
│   └── index.html       # Tutta la UI (single-file app)
├── assets/              # Icone dell'applicazione
└── .github/workflows/   # Pipeline CI/CD (Gitea Actions)
```

---

## CI/CD

Il workflow `.github/workflows/release.yml` si attiva al push di un tag `v*` e produce automaticamente i pacchetti per tutte le piattaforme, pubblicandoli come release sia su questo repository che su [GitHub](https://github.com/lamicopalmato/generatore-schede/releases).

---

## Licenza

Distribuito sotto licenza **MIT**. Vedi il file [`LICENSE`](LICENSE) per i dettagli.
