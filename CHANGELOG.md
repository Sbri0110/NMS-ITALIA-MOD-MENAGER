# Cronologia delle versioni

Il formato segue [Keep a Changelog](https://keepachangelog.com/it/1.1.0/).
Le versioni seguono [Versionamento Semantico](https://semver.org/lang/it/).

---

## 1.1.1 — 25 settembre 2026

Corregge un difetto della 1.1.0 che rendeva inutilizzabili due sezioni.

### Correzioni

- **Le Impostazioni e la sezione delle mod non si aprivano.** Cliccando la voce
  nella barra laterale non succedeva nulla: nessun messaggio, nessun errore nel
  log. La causa era nella composizione dei servizi: due servizi erano registrati
  sotto un'interfaccia ma richiesti dalle view model sotto un'altra — una
  interfaccia derivata nel primo caso, il tipo concreto nel secondo. Il
  contenitore non riusciva a costruire le due view model, e la navigazione
  falliva in silenzio.
- **Il controllo che mancava.** Il difetto era invisibile alla verifica che si
  stava usando — "il programma si avvia e resta vivo" — perche' la dashboard e'
  la sezione predefinita e le altre non vengono costruite finche' qualcuno non
  le apre. Ora c'e' un test che **costruisce ogni sezione dal contenitore vero**
  e verifica che ogni voce dichiarata disponibile porti a una schermata. Sarebbe
  stato l'unico modo di accorgersene prima di pubblicare.
- L'intestazione inviata ai servizi conteneva la versione scritta a mano, ed era
  rimasta a `1.0.0`. Ora la legge dall'assembly: un dato che si puo' derivare
  non va trascritto.

---

## 1.1.0 — 25 settembre 2026

Aggiunge cinque funzioni e corregge il pacchetto. Nessuna funzione della 1.0.0
è stata rimossa o cambiata nel comportamento.

### Avvio del gioco

- **Avvia No Man's Sky dal programma**, con il metodo corretto per la
  piattaforma:
  - **Steam** tramite il protocollo `steam://rungameid/<id>`, così il gioco
    risulta in esecuzione nella libreria, il tempo di gioco viene conteggiato e
    i salvataggi in cloud si sincronizzano. Avviare l'eseguibile diretto
    funzionerebbe, ma Steam non lo registrerebbe come in esecuzione.
  - **Xbox PC / Game Pass** tramite il percorso registrato da Windows
    (`shell:AppsFolder\...`), che è l'unico modo per avviare un gioco
    pacchettizzato: il suo eseguibile non è avviabile direttamente.
  - **GOG** e installazioni indicate a mano tramite l'eseguibile.
- **Nessun identificativo è scritto nel codice**: quello di Steam viene letto
  dal file `appmanifest_*.acf` dell'installazione — e accettato solo se
  composto da cifre — il nome del pacchetto Xbox dal manifest
  dell'applicazione.
- Il metodo di avvio viene dichiarato nella schermata **prima** che si prema il
  pulsante, e quando l'avvio non è possibile il motivo è scritto.

### Cartella del gioco indicata a mano

- Per le installazioni che il rilevamento automatico non trova: giochi su un
  disco esterno, installazioni spostate, copie di prova.
- La cartella scelta viene **validata come tutte le altre** — sceglierla a mano
  non la rende più affidabile — e il percorso viene conservato fra un avvio e
  l'altro.
- Se si sceglie per errore una sottocartella (`GAMEDATA` o `Binaries`), il
  programma risale da solo alla radice.
- Il percorso viene **rivalidato a ogni avvio**: una cartella spostata o
  cancellata viene segnalata con il motivo, non ignorata.
- Un'installazione indicata a mano viene usata per prima, ma se coincide con
  quella trovata da Steam, Xbox o GOG resta l'etichetta della piattaforma: è
  quella che serve per avviare il gioco nel modo corretto.

### Analisi dei file `.MBIN`

- Tramite **MBINCompiler**, per vedere cosa una mod cambia dentro un file di
  dati binari invece di sapere solo che l'ha sostituito.
- Lo strumento **non viene distribuito e non viene scaricato** dal programma:
  si usa se c'è, e quando non c'è lo si dichiara.
- **La versione viene verificata prima di ogni uso.** MBINCompiler è legato
  alla versione del gioco: usarne uno di un'altra generazione non produce un
  errore ma un risultato *sbagliato* senza segnalarlo. Se le versioni non
  corrispondono, la lettura resta disattivata e il motivo viene detto.
- Il confronto è su maggiore e minore: le ultime cifre sono correzioni dello
  strumento, non cambi di formato.
- La conversione avviene su una **copia temporanea**: i file dell'utente non
  vengono mai toccati, e nessuna conversione parte da sola.

### Download delle mod da Nexus Mods

- **Installazione senza passare dal browser**: si incolla l'indirizzo della mod,
  il programma mostra i file pubblicati e li scarica.
  - Con un account **Premium** il download parte direttamente.
  - Con un account **gratuito** Nexus richiede di passare dal sito: si incolla
    il collegamento `.nxm` che il sito genera premendo "Mod Manager Download",
    e il download funziona lo stesso. Il tipo di account è dichiarato nella
    schermata prima che si provi.
- Il file scaricato **non viene installato automaticamente**: passa dalla stessa
  analisi preventiva e dalla stessa transazione di un archivio scelto a mano.
- Il contenuto viene controllato mentre arriva: un collegamento scaduto risponde
  con una pagina web, che viene riconosciuta e rifiutata invece di essere
  salvata come se fosse un archivio.
- Il nome del file viene ripulito: un nome costruito ad arte non può scrivere
  fuori dalla cartella temporanea.

### Aggiornamento del programma

- **Controllo degli aggiornamenti** dalle release di questo repository.
  - **Non avviene da solo**: parte quando si preme il pulsante, oppure
    all'avvio **solo se lo si attiva**. La scelta predefinita è di non
    controllare.
  - La richiesta non contiene nulla che riguardi l'utente: è la stessa che
    farebbe un browser aprendo la pagina delle release.
  - **Non propone mai un ritorno indietro**: se la versione installata è più
    avanti di quella pubblicata, non c'è nulla da proporre.
- **Download verificato.** L'archivio viene scaricato e la sua impronta SHA-256
  confrontata con quella dichiarata nelle note del rilascio. Se non corrisponde,
  **il file viene eliminato e l'aggiornamento non prosegue**. Se il rilascio non
  dichiara un'impronta, il file si scarica ma la schermata lo dice, invece di
  dichiarare una verifica che non è avvenuta.
- **Applicazione assistita.** Il programma scompatta la nuova versione,
  controlla che contenga davvero il programma e che la cartella sia scrivibile,
  poi scrive uno script che attende la chiusura, sostituisce i file e riavvia.
  Lo script **copia e basta**: non esegue nulla di quello che copia.

### Distribuzione

- **Un solo eseguibile nella cartella.** Lo strumento diagnostico del runtime
  (`createdump.exe`) e la documentazione XML delle librerie vengono rimossi
  dalla pubblicazione: chi apre la cartella deve poter capire quale file
  avviare senza chiederlo.
- Il `LEGGIMI.txt` dice esplicitamente qual è l'unico file da avviare, che gli
  altri non vanno aperti, e che l'eseguibile non va spostato fuori dalla
  cartella.

### Correzioni

- L'ordine delle installazioni trovate è esplicito e non dipende più dal valore
  numerico dell'enumerazione delle piattaforme. Non era un difetto visibile, ma
  significava che aggiungere una piattaforma all'enumerazione avrebbe potuto
  cambiare quale installazione viene usata, senza che nulla lo segnalasse.

### Limiti dichiarati

- **L'avvio del gioco è verificato solo su Xbox PC / Game Pass**, la piattaforma
  su cui è stato provato. Steam e GOG sono coperti da test automatici sul
  comando prodotto, ma non provati su un'installazione reale.
- **L'analisi dei `.MBIN` richiede MBINCompiler della stessa versione del
  gioco**, da procurarsi a parte. Non è un difetto: il formato binario viene
  ricostruito dalla comunità a ogni aggiornamento di No Man's Sky, e riscriverlo
  qui significherebbe duplicare un lavoro che esiste già ed è mantenuto.
- **Il download tramite API richiede un account Premium**, oppure un account
  gratuito con il collegamento `.nxm` dal sito. Non è una limitazione del
  programma: è Nexus a richiederlo. Resta sempre possibile scaricare dal
  browser.
- **L'aggiornamento del programma è verificato solo sul percorso di controllo e
  di download**, provato contro la release pubblicata. L'applicazione della
  sostituzione non è ancora stata provata su un aggiornamento reale.
- **Gli archivi 7Z e RAR sono verificati solo sulla firma.** Il percorso di
  estrazione è implementato e condiviso con lo ZIP, ma non è ancora stato
  provato su un archivio reale di ciascun formato.
- **Nessuna localizzazione** oltre all'italiano.

---

## 1.0.0 — 25 settembre 2026

Prima versione utilizzabile. Non è una versione "1.0" di facciata: le
funzioni elencate qui sotto funzionano e sono verificate su
un'installazione reale di No Man's Sky (Xbox PC / Game Pass, versione
7.4.0.0).

### Rilevamento del gioco

- Rilevamento automatico su **Steam**, **Xbox PC / Game Pass** e **GOG**,
  più percorso indicato a mano.
- **Cartella indicata a mano**, per le installazioni che il rilevamento
  automatico non trova: giochi su un disco esterno, installazioni spostate,
  copie di prova. La cartella scelta viene **validata come tutte le altre** —
  sceglierla a mano non la rende più affidabile — e il percorso viene
  conservato fra un avvio e l'altro.
  - Se si sceglie per errore una sottocartella (`GAMEDATA` o `Binaries`), il
    programma risale da solo alla radice.
  - Il percorso viene rivalidato a ogni avvio: una cartella spostata o
    cancellata viene segnalata **con il motivo**, non ignorata.
  - Un'installazione indicata a mano viene usata per prima, ma se coincide con
    quella trovata da Steam, Xbox o GOG resta l'etichetta della piattaforma:
    è quella che serve per avviare il gioco nel modo corretto.
- **Validazione strutturale** di ogni installazione trovata: non basta che
  la cartella esista, deve contenere un gioco.
- Lettura della **versione** dal manifest del pacchetto o dall'eseguibile.
- Segnalazione delle mod collocate nel percorso **obsoleto**
  (`PCBANKS\MODS`), che da Worlds Part II il gioco non legge più.
- **Avvio del gioco** dal programma, con il metodo corretto per la
  piattaforma:
  - **Steam** tramite il protocollo `steam://rungameid/<id>`, così il gioco
    risulta in esecuzione nella libreria e i salvataggi in cloud si
    sincronizzano;
  - **Xbox PC / Game Pass** tramite il percorso registrato da Windows
    (`shell:AppsFolder\...`), che è l'unico modo per avviare un gioco
    pacchettizzato;
  - **GOG** e installazioni manuali tramite l'eseguibile.

  Nessun identificativo è scritto nel codice: quello di Steam viene letto
  dal file `appmanifest_*.acf` dell'installazione, il nome del pacchetto
  Xbox dal manifest dell'applicazione. Il metodo di avvio viene dichiarato
  nella schermata **prima** che l'utente prema il pulsante, e quando
  l'avvio non è possibile il motivo è scritto.

### Gestione delle mod

- **Installazione da archivio** ZIP, 7Z e RAR, con estrazione in area
  temporanea e spostamento transazionale. Un errore a metà lascia il gioco
  esattamente com'era.
- Riconoscimento automatico della **cartella annidata di troppo**, che è
  l'errore di confezionamento più comune.
- **Rimozione** reversibile, con copia del contenuto prima
  dell'eliminazione.
- **Attivazione e disattivazione per singola mod** tramite la proprietà
  `Enabled` del file di stato del gioco. È il meccanismo nativo: non sposta
  e non rinomina nulla.
- **Safe Mode** tramite il flag `DisableAllMods`.
- **Classificazione dei file**: distingue ciò che il gioco legge (`.EXML`,
  `.MBIN`, `LocTable.MXML`) da ciò che non legge più (`.MXML`, `.PAK`).
- **Avviso esplicito** per le mod che non produrranno alcun effetto.

### Diagnostica

- **Verifica completa** in sola lettura: undici controlli indipendenti su
  installazione, versione del gioco, percorso obsoleto, file di stato, Safe
  Mode, mod inefficaci, mod non registrate, voci residue, conflitti,
  contenuti eseguibili e cambio di versione del gioco. Ogni esito dice cosa
  è stato verificato, cosa significa e cosa fare.
- Un controllo che non si può eseguire viene **dichiarato** come non
  eseguito, non omesso.
- **Ricerca della mod problematica** per bisezione: con cinquanta mod
  servono sei avvii del gioco invece di cinquanta. Lo stato di partenza
  viene ripristinato sempre, anche interrompendo la ricerca.
- **Motore di conflitti a livello di proprietà**: due mod che toccano lo
  stesso file ma proprietà diverse **non** vengono segnalate come in
  conflitto. I falsi positivi rendono inutili gli avvisi.
- **Rilevamento dei duplicati** per contenuto, non per nome.
- **Sorveglianza degli aggiornamenti** del gioco: avvisa quando la versione
  cambia e indica quali mod meritano una verifica, con la motivazione.

### Profili, backup e catalogo

- **Profili**: insiemi di mod attive, applicabili e confrontabili senza
  toccare i file.
- **Backup Center** con copie transazionali, ripristino reversibile e
  protezione delle copie che non devono essere eliminate.
- **Catalogo delle mod** su database SQLite, con migrazioni versionate,
  journal WAL e integrità referenziale applicata.
- **Dashboard** con lo stato di salute del modding.

### Sicurezza

- Validazione degli archivi contro **ZIP Slip**, **path traversal**,
  **archive bomb**, **symlink** e contenuti eseguibili.
- I limiti si applicano ai **byte reali** durante la scrittura, non ai
  valori dichiarati dall'archivio: un archivio può mentire sulla propria
  dimensione.
- Nessun file eseguibile contenuto in una mod viene **mai** avviato.
- **Scrittura atomica** di tutti i file di configurazione.
- **Motore transazionale** con journal su disco: ogni modifica è
  reversibile anche dopo un'interruzione.

### Integrazione Nexus Mods

- Solo **API ufficiali**. Nessuno scraping, in nessun caso.
- Supporto di **entrambe le generazioni** dell'API, v1 e v3, con scelta
  della fonte esposta nelle impostazioni.
- La v3 è la fonte principale dei metadati; la v1 completa i campi che la
  v3 non espone e fornisce l'elenco dei file.
- **Provenienza dichiarata** in ogni risultato: si sa sempre quale versione
  ha risposto.
- **Installazione di una mod da Nexus** senza passare dal browser: si incolla
  l'indirizzo della mod, il programma mostra i file pubblicati e li scarica.
  - Con un account **Premium** il download parte direttamente.
  - Con un account **gratuito** Nexus richiede di passare dal sito: si incolla
    il collegamento `.nxm` che il sito genera premendo "Mod Manager Download",
    e il download funziona lo stesso. **Il tipo di account è dichiarato nella
    schermata prima che si provi**, con la spiegazione di cosa comporta.
  - Il file scaricato **non viene installato automaticamente**: passa dalla
    stessa analisi preventiva e dalla stessa transazione di un archivio scelto
    a mano, e un download interrotto non lascia nulla dentro il gioco.
  - Il contenuto viene controllato mentre arriva: un collegamento scaduto
    risponde con una pagina web, che viene riconosciuta e rifiutata invece di
    essere salvata come se fosse un archivio.
  - Il nome del file viene ripulito: un nome costruito ad arte non può
    scrivere fuori dalla cartella temporanea.
- La chiave API sta nel **Credential Manager di Windows** e non viene mai
  mostrata né registrata nei log.

### Interfaccia

- Design system completo con **tema scuro**, set di icone vettoriali
  coerente e asset grafici.
- **Interfaccia interamente in italiano.**
- Icona dell'applicazione con il **logo della community** a tutte le
  risoluzioni, dal file `.ico` a 7 dimensioni (16 → 256) fino ai PNG fino a
  1024 pixel.
- Le sezioni non ancora realizzate sono dichiarate con la fase in cui
  arriveranno. Nessun pulsante che finge di funzionare.

### Analisi dei file di gioco

- **Analisi dei file `.MBIN`** tramite MBINCompiler, per vedere cosa una mod
  cambia dentro un file di dati binari invece di sapere solo che l'ha
  sostituito.
  - Lo strumento **non viene distribuito e non viene scaricato** dal
    programma: si usa se c'è, e quando non c'è lo si dichiara.
  - **La versione viene verificata prima di ogni uso.** MBINCompiler è legato
    alla versione del gioco: usarne uno di un'altra generazione non produce un
    errore ma un risultato *sbagliato* senza segnalarlo. Se le versioni non
    corrispondono, la lettura resta disattivata e il motivo viene detto.
  - Il confronto è su maggiore e minore: le ultime cifre sono correzioni dello
    strumento, non cambi di formato.
  - La conversione avviene su una **copia temporanea**: i file dell'utente non
    vengono mai toccati, e nessuna conversione parte da sola.
  - Lo stato dello strumento è visibile in **Impostazioni** e nella
    **Diagnostica**, che lo verifica a ogni esecuzione.

### Distribuzione

- Pacchetto **autosufficiente** per Windows a 64 bit: non richiede
  l'installazione di .NET.
- **Un solo eseguibile** nella cartella: gli strumenti diagnostici del runtime
  e la documentazione XML delle librerie vengono rimossi dalla pubblicazione,
  perché chi apre la cartella deve poter capire quale file avviare senza
  chiederlo.
- Circa 51 MB compressi, la maggior parte dei quali è il runtime incluso.
- **Licenza proprietaria**: uso gratuito, ma vietata la ridistribuzione, la
  modifica e la vendita. Il codice sorgente non viene distribuito.

### Community

- **Collegamento al Discord di NMS Italia** dalla schermata Impostazioni: un
  pulsante apre l'invito al server nel browser.
- Il collegamento è presente anche nel README, in `PRIVACY.md` e in
  `installer/LEGGIMI.txt`, così è raggiungibile anche da chi ha solo il
  pacchetto scaricato e non ha ancora avviato il programma.

### Limiti dichiarati

- **L'avvio del gioco è verificato solo su Xbox PC / Game Pass**, che è la
  piattaforma su cui è stato provato. I percorsi Steam e GOG sono coperti da
  test automatici sul comando prodotto, ma non sono stati provati su
  un'installazione reale di quelle piattaforme.
- **L'analisi dei `.MBIN` richiede MBINCompiler della stessa versione del
  gioco**, che va procurato a parte. Non è un difetto: il formato binario viene
  ricostruito dalla comunità a ogni aggiornamento di No Man's Sky, e
  riscriverlo qui significherebbe duplicare un lavoro che esiste già ed è
  mantenuto.
- **Gli archivi 7Z e RAR verificati solo sulla firma**, non ancora provati su
  archivi reali di ciascun formato.
- **Nessun aggiornatore automatico** del programma.
- **Nessuna localizzazione** oltre all'italiano.

---

## Come leggere questa cronologia

Le voci sono raggruppate per area, non per data di lavorazione: chi legge
sta cercando *cosa fa il programma*, non *quando è stato scritto*.

I limiti sono elencati nella stessa sezione delle funzioni, e non in fondo
o altrove. Un elenco di funzioni senza i propri limiti è pubblicità, non
documentazione.

---

## Community

Dubbi, problemi, segnalazioni e aggiornamenti: **Discord di NMS Italia**.

<https://discord.gg/KB2NbjCW6h>
