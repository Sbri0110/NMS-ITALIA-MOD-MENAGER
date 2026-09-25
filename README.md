# NMS ITALIA MOD MENAGER

**Il centro di controllo definitivo per le mod di No Man's Sky.**

> Progetto community non ufficiale. Non affiliato ne' approvato da Hello Games.

---

## Che cos'e'

Un gestore di mod per No Man's Sky pensato per la community italiana. L'obiettivo
non e' copiare file in una cartella: e' capire lo stato del modding e spiegarlo.

L'utente deve poter chiedere:

- cosa ho installato?
- perche' questa mod non funziona?
- quale mod mi sta facendo crashare il gioco?
- cosa e' cambiato dopo l'aggiornamento del gioco?
- come torno a una configurazione che funzionava?

E ricevere una risposta in italiano, comprensibile, con la spiegazione del perche'.

---

## Installazione

Il programma **non richiede l'installazione di .NET** e **non richiede una
connessione a Internet**.

1. scarica `NMSItalia.ModMenager.zip` dalla pagina delle release;
2. scompattalo dove preferisci — **non** dentro la cartella di No Man's Sky;
3. avvia `NMSItalia.ModMenager.App.exe`.

Windows mostrera' un avviso di sicurezza al primo avvio: il programma non e'
firmato digitalmente. "Ulteriori informazioni" → "Esegui comunque".

Guida completa, problemi comuni e disinstallazione:
**[`docs/INSTALLAZIONE.md`](docs/INSTALLAZIONE.md)**

---

## Documenti

| Documento | Cosa contiene |
|---|---|
| [`docs/INSTALLAZIONE.md`](docs/INSTALLAZIONE.md) | come si installa, si avvia e si disinstalla; problemi comuni |
| [`CHANGELOG.md`](CHANGELOG.md) | cosa fa questa versione, e cosa non fa |
| [`SECURITY.md`](SECURITY.md) | come sono trattati gli archivi delle mod e i dati |
| [`PRIVACY.md`](PRIVACY.md) | cosa il programma raccoglie e cosa invia in rete |
| [`docs/FORMATO-MOD-NMS.md`](docs/FORMATO-MOD-NMS.md) | il formato delle mod di No Man's Sky, verificato sul campo |
| [`THIRD_PARTY_NOTICES.md`](THIRD_PARTY_NOTICES.md) | librerie di terze parti e licenze |

---

## Stato del progetto

Il progetto e' in sviluppo attivo, per fasi. Ogni fase si chiude con qualcosa che
**funziona davvero**, non con un cantiere aperto.

| Fase | Contenuto | Stato |
|---|---|---|
| **1 — Foundation** | architettura, dominio, sicurezza, rilevamento gioco, design system, interfaccia | **completata** |
| **2 — Core mod management** | formato, archivi, installer transazionale, database, backup, profili, interfaccia | **completata** |
| **3 — Diagnostics** | conflitti, Safe Mode, verifica completa, ricerca della mod problematica | **completata** |
| 4 — Advanced analysis | EXML, MBIN, compatibilita', update guard | parziale: mancano MBXML e merge assistito |
| 5 — Online | API Nexus (v1 e v3), metadati, aggiornatore del software | parziale: manca l'aggiornatore del software |
| 6 — Polish | asset grafici, accessibilita', installer, documentazione | parziale: mancano installer, localizzazione, CI |

### Cosa funziona oggi

- **Rilevamento automatico** di No Man's Sky su Steam, Xbox PC / Game Pass, GOG e
  percorso personalizzato.
- **Cartella indicata a mano**, per le installazioni che il rilevamento
  automatico non trova. La cartella scelta viene validata come tutte le altre e
  rivalidata a ogni avvio: se è stata spostata o cancellata, il programma lo
  dice invece di ignorarla.
- **Validazione strutturale** di ogni installazione trovata: non basta che la
  cartella esista, deve contenere un gioco.
- **Lettura della versione** del gioco dal manifest del pacchetto o
  dall'eseguibile.
- **Avvio del gioco** con il metodo corretto per la piattaforma: protocollo
  Steam, percorso registrato per Xbox PC / Game Pass, eseguibile per GOG.
  Il metodo viene dichiarato prima dell'avvio, e nessun identificativo è
  scritto nel codice: si legge dall'installazione.
- **Lettura e scrittura dello stato delle mod** (`GCMODSETTINGS.MXML`) con
  scrittura atomica. Il flag `DisableAllMods` e' la base della Safe Mode.
- **Classificazione dei file di mod**: distingue ciò che il gioco legge
  (`.EXML`, `.MBIN`, `LocTable.MXML`) da ciò che non legge più (`.MXML`, `.PAK`).
- **Analisi degli archivi ZIP, 7Z e RAR senza estrarli**, con la validazione di
  sicurezza integrata e il riconoscimento della cartella annidata di troppo.
- **Installazione e rimozione delle mod**, con estrazione sicura in area
  temporanea e spostamento transazionale: ogni operazione è reversibile e un
  errore a metà lascia il gioco esattamente com'era.
- **Safe Mode** tramite il flag `DisableAllMods` del gioco: non sposta e non
  rinomina nulla, quindi il ripristino è garantito.
- **Motore di conflitti a livello di proprietà**: distingue un conflitto reale da
  due mod che semplicemente toccano lo stesso file.
- **Rilevamento dei duplicati** per contenuto, non per nome.
- **Attivazione e disattivazione per singola mod** tramite la proprietà `Enabled`
  della voce nel file di stato del gioco: è il meccanismo nativo, quindi non
  sposta e non rinomina nulla.
- **Analisi dei file `.MBIN`** tramite MBINCompiler, per vedere cosa una mod
  cambia dentro un file di dati binari. Lo strumento non viene distribuito né
  scaricato: si usa se c'è, e la versione viene verificata prima di ogni uso —
  uno strumento di un'altra generazione non fallisce, produce un risultato
  sbagliato senza segnalarlo.
- **Installazione delle mod da Nexus Mods** senza passare dal browser: si
  incolla l'indirizzo della mod, il programma mostra i file pubblicati e li
  scarica. Con un account Premium il download parte direttamente; con un
  account gratuito si incolla il collegamento `.nxm` che il sito genera
  premendo "Mod Manager Download". Il tipo di account è dichiarato prima che
  si provi.
- **Profili**: insiemi di mod attive, applicabili e confrontabili senza toccare i
  file.
- **Backup Center** con copie transazionali e ripristino reversibile.
- **Diagnostica completa** in sola lettura: undici controlli indipendenti su
  installazione, file di stato, mod e conflitti, ognuno con spiegazione e azione
  suggerita.
- **Ricerca della mod problematica** per bisezione: con cinquanta mod servono sei
  avvii del gioco, non cinquanta, e lo stato di partenza viene sempre ripristinato.
- **Integrazione Nexus Mods** tramite API ufficiali, versioni **v1 e v3**, con
  scelta della fonte esposta nelle impostazioni e provenienza dei dati dichiarata.
- **Database locale SQLite** con migrazioni, journal WAL e integrità
  referenziale applicata. Nessun server, nessun account.
- **Dashboard** con lo stato di salute del modding, con spiegazioni.
- **Sicurezza degli archivi**: validazione contro ZIP Slip, path traversal,
  archive bomb, symlink e contenuti eseguibili. I limiti si applicano ai
  **byte reali** durante la scrittura, non ai valori dichiarati dall'archivio.
- **Logging strutturato** su file con rotazione e rimozione automatica dei
  valori sensibili.
- **Interfaccia** con design system completo, tema scuro e asset grafici.

Le funzioni non ancora realizzate sono dichiarate nell'applicazione con la fase in
cui arriveranno. Nessun pulsante finto, nessuna azione simulata.

### Limiti dichiarati di questa versione

- **L'analisi dei `.MBIN` richiede MBINCompiler** della stessa versione del
  gioco, da procurarsi a parte. Il programma lo cerca, ne verifica la versione
  e lo usa se corrisponde; altrimenti dichiara perché non può. Non è un difetto:
  il formato binario viene ricostruito dalla comunità a ogni aggiornamento di No
  Man's Sky, e riscriverlo qui significherebbe duplicare un lavoro mantenuto
  altrove.
- **Il download delle mod tramite API richiede un account Premium**, oppure un
  account gratuito con il collegamento `.nxm` dal sito. Non è una limitazione
  del programma: è Nexus a richiederlo, e il programma lo dichiara nella
  schermata prima che si provi. In ogni caso resta sempre possibile scaricare
  dal browser e installare l'archivio.
- **Gli archivi 7Z e RAR sono verificati solo sulla firma.** Il percorso di
  estrazione è implementato e condiviso con lo ZIP, ma non è ancora stato provato
  su un archivio reale di ciascun formato.
- **L'avvio del gioco è verificato solo su Xbox PC / Game Pass**, la piattaforma
  su cui è stato provato. Steam e GOG sono coperti da test automatici sul comando
  prodotto, ma non provati su un'installazione reale.
- **Nessuna localizzazione** oltre all'italiano, **nessuna pipeline CI**, **nessun
  aggiornatore automatico** del software.

---

## Requisiti

- **Windows 10** o successivo, 64 bit
- **No Man's Sky** su Steam, Xbox PC / Game Pass o GOG

Il pacchetto distribuito è **autosufficiente**: non richiede l'installazione di
.NET e non richiede una connessione a Internet.

---

## Prima di installare una mod: leggere questo

**Le guide in circolazione sono in gran parte obsolete.** Se hai letto che le mod
vanno in `PCBANKS\MODS` e devono essere file `.pak`, quella informazione e'
superata.

Da **Worlds Part II** (No Man's Sky 5.50 e successivi) Hello Games ha riscritto il
sistema di modding:

| | Prima | Adesso |
|---|---|---|
| Dove vanno le mod | `GAMEDATA\PCBANKS\MODS` | **`GAMEDATA\MODS`** |
| Formato | un file `.pak` per mod | una **cartella** per mod |
| Come si applicano | sostituiscono file interi | `.MBIN` sostituisce, `.EXML` **modifica solo le proprieta' dichiarate** |
| Conflitti | la seconda mod sovrascrive la prima | molto ridotti: due `.EXML` con proprieta' diverse convivono |

Conseguenze pratiche:

- una mod installata in `PCBANKS\MODS` **non verra' caricata**;
- una mod `.pak` **probabilmente non funziona**;
- un file `.MXML` **non funziona** (unica eccezione `LocTable.MXML`);
- le mod non aggiornate **prima del 29 gennaio 2025** non funzionano con 5.50+.

La documentazione tecnica completa, con le fonti e le verifiche, e' in
[`docs/FORMATO-MOD-NMS.md`](docs/FORMATO-MOD-NMS.md).

---

## Come e' fatto

Il principio architetturale è la separazione tra **dominio** e **dettagli**.

Le regole del modding — cos'è una mod, come si applica, quando due mod entrano
davvero in conflitto — stanno in un nucleo che non conosce né Steam, né Nexus,
né il formato dei file su disco. Tutto ciò che dipende dall'esterno (le
piattaforme di distribuzione, il formato di No Man's Sky, l'API di Nexus) sta
ai bordi, dietro interfacce.

La conseguenza pratica è concreta: se Hello Games riscrive di nuovo il sistema
di modding — come ha già fatto con Worlds Part II — si sostituisce la parte che
conosce il formato, non tutto il programma.

Il codice sorgente non è pubblico. Questa sezione descrive **come è
organizzato** il programma, non come è scritto.

---

## Sicurezza

Ogni archivio importato e' trattato come **input non affidabile**.

Difese implementate e coperte da test:

- path traversal e ZIP Slip;
- percorsi assoluti e lettere di unita' iniettate;
- Alternate Data Stream;
- nomi di dispositivo riservati da Windows;
- archive bomb (rapporto di compressione, dimensione totale, numero di voci);
- collegamenti simbolici e reparse point;
- collisioni di nome su filesystem non sensibile alle maiuscole;
- contenuti eseguibili, rilevati per estensione **e** per intestazione del file.

Il software **non esegue mai** codice proveniente da una mod.

Dettagli in [`SECURITY.md`](SECURITY.md).

---

## Privacy

Principio **local-first**:

- il software funziona completamente offline per gestione mod, profili, backup e
  diagnostica;
- nessun account obbligatorio;
- nessuna telemetria;
- i dati restano sul computer dell'utente;
- i log non contengono mai token, password o chiavi API, perche' un filtro di
  rimozione e' parte del percorso di scrittura.

---

## Diritti degli autori

Il progetto **non redistribuisce mod di terze parti**. Le mod restano degli
autori che le hanno create, con le loro licenze.

Gli asset grafici del progetto sono originali e non riproducono materiale
protetto di No Man's Sky: nessun artwork, logo, suono o asset estratto dal gioco.

---

## Sviluppato con l'aiuto dell'intelligenza artificiale

Questo progetto e' sviluppato con il supporto di strumenti di intelligenza
artificiale, in modo dichiarato e senza nasconderlo. Le decisioni tecniche, la
verifica del comportamento e la responsabilita' del risultato restano umane.

---

## Community

Il programma è legato al server **NMS Italia** su Discord: è lì che si trovano
supporto, segnalazioni, aggiornamenti e il resto della community italiana di
No Man's Sky.

### **<https://discord.gg/KB2NbjCW6h>**

Il collegamento è raggiungibile anche dall'applicazione, nella schermata
**Impostazioni**. Per una segnalazione di sicurezza, leggi prima
[`SECURITY.md`](SECURITY.md): non va resa pubblica.

---

## Licenza

**Proprietaria — tutti i diritti riservati.** Vedi [`LICENSE`](LICENSE).

L'uso è **gratuito**. Non sono consentiti la modifica, la ridistribuzione, la
vendita né l'ingegneria inversa.

Il codice sorgente **non è distribuito e non è pubblico**: il programma è
disponibile solo in forma compilata.

Le licenze dei componenti di terze parti sono in
[`THIRD_PARTY_NOTICES.md`](THIRD_PARTY_NOTICES.md).
