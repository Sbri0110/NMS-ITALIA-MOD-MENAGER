# Sicurezza — NMS ITALIA MOD MENAGER

> Documento di riferimento. Descrive le minacce considerate, le contromisure
> implementate e i limiti dichiarati. Data dell'ultima verifica: 25 settembre 2026.

---

## Principio guida

**Ogni mod importata e' input non affidabile.**

Un archivio scaricato da Internet e' codice e dati di provenienza ignota. Il
software non gli riconosce alcuna fiducia a priori, nemmeno se arriva da una
sorgente nota e nemmeno se l'utente lo ritiene affidabile.

Il secondo principio, meno ovvio: **rifiutare, non correggere.** Un percorso
malevolo non viene "normalizzato" in un percorso sicuro e poi accettato. Viene
rifiutato. La normalizzazione silenziosa e' il modo in cui gli estrattori di
archivi finiscono per avere falle: ogni passo di correzione automatica e' un
punto in cui la difesa puo' essere aggirata in modo imprevisto.

---

## Minacce considerate e contromisure

### Percorsi

| Minaccia | Esempio | Contromisura |
|---|---|---|
| **ZIP Slip** | voce `..\..\..\Windows\System32\evil.dll` | il segmento `..` viene **rifiutato**, non risolto |
| Path traversal con separatori misti | `mod/..\..\evil.dll` | separatori uniformati prima dell'analisi |
| Percorso assoluto iniettato | `/etc/passwd`, `\\server\share\x` | rifiuto dei percorsi che iniziano con separatore |
| Lettera di unita' | `C:\Windows\evil.dll` | rifiuto di qualsiasi `:` nel percorso |
| **Alternate Data Stream** | `mod.txt:hidden.exe` | coperto dal rifiuto di `:` |
| Nomi riservati Windows | `NUL`, `CON`, `COM1`, `NUL.txt` | rifiuto, confronto anche senza estensione |
| Nomi che Windows altera | `mod.txt.`, `mod.txt ` | rifiuto di punto finale e spazi ai bordi |
| Caratteri non validi | caratteri di controllo, `<>|?*` | validazione con le regole del filesystem |
| Percorso troppo lungo | oltre 240 caratteri | rifiuto con spiegazione, invece di un errore oscuro in scrittura |
| Annidamento eccessivo | oltre 32 livelli | rifiuto |

La difesa non si ferma ai controlli sui segmenti. Dopo averli superati, il
percorso assoluto risultante viene verificato essere **effettivamente contenuto**
nella cartella di destinazione, confrontando i percorsi canonici **con il
separatore finale incluso**. Senza il separatore, una cartella `MODS_evil`
risulterebbe contenuta in `MODS` perche' la stringa comincia allo stesso modo.
Questo caso e' coperto da un test dedicato.

### Risorse

| Minaccia | Contromisura |
|---|---|
| **Archive bomb** | limite sul rapporto di compressione (default 200:1) |
| Espansione totale eccessiva | limite sulla dimensione decompressa complessiva (default 2 GiB) |
| Singolo file enorme | limite per singola voce (default 1 GiB) |
| Esaurimento per numero di file | limite sul numero di voci (default 20.000) |
| Archivio ricorsivo | limite di profondita' |

Il controllo sul rapporto di compressione e' la difesa piu' efficace contro le
bomb. Un archivio che dichiara di espandersi di oltre 200 volte non e' una mod
legittima: i contenuti di gioco sono texture e mesh gia' compresse, con rapporti
reali bassi.

Tutti i limiti sono configurabili, ma alzarli e' una scelta esplicita dell'utente.

### Collegamenti e reparse point

I collegamenti simbolici contenuti in un archivio vengono **sempre rifiutati**.
Un symlink puo' puntare fuori dalla destinazione, e scrivere "dentro" di esso
scriverebbe altrove. Non esiste un caso d'uso legittimo per un symlink in una mod
di No Man's Sky.

Rifiutare i symlink *nell'archivio* non basta: se nella cartella di destinazione
esiste gia' un reparse point creato in precedenza, scrivere in una sua
sottocartella scriverebbe in realta' altrove. Per questo ogni componente del
percorso viene controllato anche sul disco. Se l'attributo non e' leggibile, si
assume il caso peggiore e si rifiuta: meglio un rifiuto ingiustificato che una
scrittura fuori destinazione.

### Contenuti eseguibili

Il software **non esegue mai automaticamente** codice proveniente da una mod:
nessun `.exe`, `.dll`, `.bat`, `.cmd`, `.ps1`, `.msi` o altro.

Il rilevamento avviene su due livelli:

1. **estensione** — funziona sui soli nomi, quindi e' utilizzabile senza estrarre;
2. **intestazione del file** — riconosce la firma `MZ` di un eseguibile Windows o
   `ELF` di un eseguibile Linux, scoprendo un eseguibile **rinominato** con
   un'estensione innocua, che il solo controllo sul nome non intercetterebbe.

Un eseguibile rilevato produce un **avviso**, non un blocco: alcune mod
legittime includono un installer o una utility. L'archivio resta installabile,
ma il contenuto eseguibile non verra' mai lanciato.

### L'unico processo esterno che il software avvia

Il programma avvia **un solo** processo esterno: No Man's Sky, quando l'utente
preme il pulsante di avvio. Nessun altro, in nessuna circostanza.

L'invariante e' verificabile leggendo il codice: l'unica chiamata a
`Process.Start` con un eseguibile arbitrario e' in `GameLauncher`, e il valore
che riceve puo' venire solo da tre fonti, tutte costruite dal programma:

| Fonte | Esempio | Da dove viene |
|---|---|---|
| Indirizzo protocollo Steam | `steam://rungameid/275850` | identificativo letto dal file `appmanifest` dell'installazione, accettato **solo** se composto da cifre |
| Percorso registrato Xbox | `shell:AppsFolder\HelloGames.NoMansSky_...!NoMansSky` | nome del pacchetto e identificativo dell'applicazione, letti dal manifest |
| Eseguibile del gioco | `<radice>\Binaries\NMS.exe` | percorso derivato dall'installazione gia' validata, verificato come esistente |

**Nessun percorso arriva dall'utente, da un archivio, da un file di
configurazione o da un sito.** Un archivio di mod non puo' influenzare questo
percorso in alcun modo.

Le altre chiamate a `Process.Start` nel programma aprono cartelle in Esplora
file, e ricevono solo percorsi locali gia' verificati come esistenti.

---

## Gestione delle credenziali

L'integrazione con Nexus Mods (Fase 5) usa **esclusivamente l'API ufficiale**.

- **Nessuna chiave API e' hardcodata nel repository**, in nessuna forma. La
  documentazione di Nexus e' esplicita su questo punto.
- La chiave fornita dall'utente sara' conservata in **Windows Credential Manager**
  o protetta con **DPAPI**, legata all'account utente.
- Per le integrazioni che lo consentono si usera' il **Single Sign-On** di Nexus,
  cosi' l'utente non deve gestire una chiave.
- Il software rispetta i **rate limit** documentati e non aggira le restrizioni
  sul download automatico.

I file sensibili sono esclusi dal controllo di versione tramite `.gitignore`:
`*.pfx`, `*.snk`, `*.key`, `*.pem`, `secrets.json`, `.env`.

---

## Log e dati sensibili

I log non devono contenere **mai** token, password o chiavi API.

La contromisura non e' una raccomandazione a chi scrive il codice, ma un
**filtro applicato nel percorso di scrittura** del logger. Una singola
dimenticanza in un punto qualsiasi del software basterebbe a far finire una
chiave su disco, e i file di log sono esattamente cio' che un utente allega
quando chiede aiuto.

Il filtro rimuove:

- coppie chiave-valore con chiave riconosciuta come sensibile
  (`apikey=`, `token=`, `password=`, `secret=`, ...), in tutte le sintassi
  ricorrenti: assegnazione, JSON, query string;
- intestazioni di autorizzazione (`Authorization: Bearer ...`).

La chiave viene mantenuta e il valore sostituito: sapere che una chiave era
presente e' informazione utile in diagnostica, il suo valore no.

---

## Principio del privilegio minimo

Il software **non richiede privilegi amministrativi** per funzionare.

La dichiarazione nel manifest dell'applicazione e' `asInvoker`: il processo gira
con i privilegi dell'utente che l'ha avviato. Se una singola operazione dovesse
richiedere elevazione, va elevata **quella operazione**, spiegando perche', e il
contesto normale deve essere ripristinato subito dopo.

Nessuna operazione scrive fuori da:

- la cartella di gioco dell'utente (e solo `GAMEDATA` e `Binaries\SETTINGS`);
- la cartella dati dell'applicazione in `%LOCALAPPDATA%`;
- le cartelle temporanee di sistema.

---

## Verifica

Le difese non sono dichiarate: sono **testate**. La suite include test di
regressione di sicurezza per ogni vettore elencato in questo documento.

Ogni nuova tecnica di attacco scoperta diventa un caso di test permanente. I test
di sicurezza non vanno rimossi o indeboliti, nemmeno se un giorno risultassero
scomodi.

La suite di test è interna allo sviluppo: il codice sorgente non viene
distribuito, quindi i test non sono eseguibili da chi scarica il programma. Il
loro esito viene dichiarato in `CHANGELOG.md` a ogni versione, e un test di
sicurezza che fallisce impedisce la pubblicazione della release.

---

## Limiti dichiarati

Il software **non**:

- analizza il contenuto binario di un `.MBIN` senza MBINCompiler sulla versione
  corretta, e in quel caso **lo dichiara** invece di produrre un'analisi parziale
  spacciata per completa;
- esegue codice di mod in alcuna circostanza;
- aggira le protezioni delle piattaforme o i termini di servizio di Nexus Mods;
- effettua scraping al posto delle API ufficiali;
- invia dati a server remoti senza un'azione esplicita dell'utente.

---

## Segnalazioni

Per segnalare un problema di sicurezza, **non aprire una segnalazione
pubblica**. Contatta privatamente la community sul Discord di NMS Italia:

<https://discord.gg/KB2NbjCW6h>

Descrivi il problema in privato e attendi che sia stato valutato prima di
renderlo pubblico. Un problema di sicurezza pubblicato prima che esista una
correzione mette a rischio tutti quelli che usano il programma.
