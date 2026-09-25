# Informativa sulla privacy — NMS ITALIA MOD MENAGER

**Ultimo aggiornamento:** 25 settembre 2026
**Versione del programma:** 1.1.0

---

## In una riga

Il programma **non raccoglie nulla e non invia nulla che ti riguardi**. Non
esiste un server nostro, non esiste un account, non esiste telemetria. I tuoi
dati restano sul tuo computer.

---

## 1. Dati raccolti

**Nessuno.**

Il programma non raccoglie, non trasmette e non conserva informazioni
personali. In particolare non raccoglie:

- statistiche d'uso o di avvio;
- segnalazioni automatiche di errore;
- identificatori del dispositivo o dell'installazione;
- indirizzi IP, nomi utente di Windows, percorsi personali;
- l'elenco delle mod installate, delle partite o dei salvataggi;
- alcun dato sull'hardware.

Non esistono SDK di analisi, né pubblicitari, né di tracciamento. Il
programma non contiene codice di terze parti che telefoni a casa.

---

## 2. Connessioni di rete

Il programma effettua connessioni in uscita verso **tre destinazioni**, e solo
in queste circostanze:

| Destinazione | Quando | Cosa viene inviato |
|---|---|---|
| `api.nexusmods.com` | Solo se premi un pulsante nella schermata Impostazioni, e solo se hai inserito una chiave API | La tua chiave API (header `apikey`) e l'intestazione `User-Agent` |
| il servizio di distribuzione degli archivi di Nexus | Solo dopo che hai premuto "Scarica e installa" | Nessun dato: è una richiesta di scaricamento |
| `api.github.com` e il servizio di distribuzione degli archivi di GitHub | Solo se premi "Controlla" nelle Impostazioni, **oppure** all'avvio se hai attivato il controllo | Solo l'intestazione `User-Agent` |

**Nessuna connessione avviene senza il tuo consenso.** Non in background, non su
un timer.

L'unica eccezione è il controllo degli aggiornamenti all'avvio: è
**disattivato per impostazione predefinita** e si attiva con una casella nella
schermata Impostazioni. Se non la spunti, il programma non contatta nessuno
finché non premi un pulsante.

**Cosa contiene la richiesta di controllo degli aggiornamenti.** È la stessa che
farebbe un browser aprendo la pagina delle release: nessun identificativo,
nessuna versione installata, nessuna statistica. GitHub vede una richiesta
anonima, come ne vede molte.

Nessun'altra destinazione è raggiungibile dal programma. Gli indirizzi
`www.nexusmods.com` che compaiono nel codice sono **testo mostrato a schermo** o
aperti nel tuo browser su tua richiesta: non vengono mai contattati dal
programma.

Se non usi le funzioni online — e non sei obbligato — il programma funziona per
intero senza mai toccare la rete. Puoi verificarlo tu stesso: il programma
continua a funzionare con la scheda di rete disattivata.

### Cosa contiene l'header `User-Agent`

```
NMSItalia.ModMenager/1.1.0 (Windows; +https://discord.gg/KB2NbjCW6h)
```

Sono tre informazioni: il nome del programma, la sua versione e un indirizzo
web. La documentazione dell'API di Nexus richiede questa intestazione, e serve
al fornitore per identificare chi effettua le richieste in caso di problemi.
**Non contiene nulla che ti riguarda.**

È **una sola stringa per tutte le destinazioni**: averne due significherebbe
doverle tenere allineate, e la versione dentro è letta dal programma stesso, non
scritta a mano.

L'indirizzo è l'invito al Discord della community: è il recapito a cui un
fornitore può rivolgersi se le richieste del programma dovessero dare problemi.
Non è un identificativo personale e non permette di risalire a te.

---

## 3. La chiave API di Nexus Mods

Se scegli di usare le funzioni online, devi fornire una chiave API
personale di Nexus Mods. Come viene trattata:

- **Dove viene conservata:** nel **Credential Manager di Windows**, legata
  al tuo account utente. Non in un file di configurazione, non nel
  database del programma, non nel registro di sistema.
- **Chi può leggerla:** solo il tuo account Windows. Un altro utente della
  stessa macchina non vi accede.
- **Se viene mostrata:** **mai.** Una volta salvata, il programma dichiara
  solo che è configurata. Non esiste una funzione "mostra chiave".
- **Se finisce nei registri:** **no.** Esiste un filtro che intercetta i
  valori sensibili prima della scrittura, e la chiave non viene mai passata
  al sistema di registrazione.
- **Come si rimuove:** con il pulsante "Rimuovi" nella schermata
  Impostazioni. La chiave viene cancellata dal Credential Manager.

La chiave è tua e resta tua. Il programma non la trasmette a nessuno
tranne che a `api.nexusmods.com`, che è il legittimo proprietario.

---

## 4. Dati conservati sul tuo computer

Il programma scrive in una sola cartella, sotto il tuo profilo:

```
%LOCALAPPDATA%\NMS ITALIA MOD MENAGER\
├── logs\          registri di funzionamento (testo)
├── settings\      impostazioni
├── data\          catalogo delle mod, profili (database SQLite)
├── backups\       copie di sicurezza delle configurazioni di mod
└── transactions\  giornali delle operazioni, per il ripristino
```

Cosa contengono, in sintesi:

- **logs** — cosa ha fatto il programma: installazioni, errori, percorsi
  dei file di gioco. I percorsi del tuo computer compaiono qui. I valori
  sensibili sono rimossi prima della scrittura.
- **data** — l'elenco delle mod che hai installato, i profili che hai
  creato, l'ultima versione del gioco osservata, la preferenza sulla
  versione dell'API.
- **backups** — copie delle configurazioni di mod, create
  automaticamente prima di ogni modifica. Sono ciò che rende possibile il
  ripristino.
- **transactions** — il giornale delle operazioni, usato per riportare il
  gioco allo stato precedente se qualcosa si interrompe.

**Nessuno di questi dati lascia il tuo computer.**

Se usi la funzione "esporta report diagnostico", il programma **mostra
prima esattamente cosa verrà incluso** e il file resta locale. Sta a te
decidere se condividerlo.

---

## 5. Salvataggi e cartella di gioco

Il programma **non legge e non modifica i salvataggi** di No Man's Sky.
Non tocca la cartella `%APPDATA%\HelloGames\NMS`.

Modifica la cartella di installazione del gioco, e solo per installare,
rimuovere o attivare mod. Ogni modifica passa da una transazione con copia
di sicurezza.

---

## 6. Minori

Il programma non raccoglie dati di nessuno, quindi non ne raccoglie
nemmeno di minori. Non è richiesta alcuna registrazione.

---

## 7. Modifiche a questa informativa

La versione 1.0.0 di questo documento dichiarava che, se il programma avesse
introdotto una funzione che comunica con l'esterno, l'informativa sarebbe stata
aggiornata **prima** che la funzione venisse attivata.

**È successo con la versione 1.1.0**, che introduce il controllo degli
aggiornamenti del programma. Questa informativa è stata aggiornata con essa, e
la funzione è disattivabile: il controllo all'avvio è spento per impostazione
predefinita, e quello manuale parte solo se premi un pulsante.

Vale anche in futuro: se una nuova funzione dovesse comunicare con l'esterno,
questa informativa verrà aggiornata **prima** che la funzione venga attivata, e
la funzione sarà disattivabile.

---

## 8. Riferimenti

- **Licenza:** proprietaria — uso gratuito, ma vietata la ridistribuzione,
  la modifica e la vendita (vedi `LICENSE`).
- **Codice sorgente:** **non è distribuito e non è pubblico.** Il programma
  è disponibile solo in forma compilata.
- **Librerie di terze parti:** `THIRD_PARTY_NOTICES.md`.

### Come puoi verificare quanto è scritto qui

Le affermazioni di questo documento non si appoggiano alla lettura del
codice, che non è disponibile. Si appoggiano a due cose che puoi
controllare tu:

1. **Il comportamento osservabile.** Il programma funziona per intero con
   la scheda di rete disattivata — installazione delle mod, profili,
   diagnostica, avvio del gioco. Se qualcosa inviasse dati di nascosto, non
   funzionerebbe offline, e invece funziona.
2. **Il traffico di rete.** Puoi osservare le connessioni del programma
   con gli strumenti di Windows (Monitoraggio risorse, o il firewall con
   le regole di notifica attive). Vedrai al massimo `api.nexusmods.com`,
   `api.github.com` e i rispettivi servizi di scaricamento degli archivi —
   e solo dopo che hai premuto un pulsante, o all'avvio se hai attivato il
   controllo degli aggiornamenti.

Se trovi una connessione che questo documento non dichiara, è un difetto
grave: segnalala sul Discord della community (sezione 9) e verrà
corretta.

---

## 9. Contatti

Per domande su questa informativa, segnalazioni o chiarimenti, entra nel
**Discord di NMS Italia**:

<https://discord.gg/KB2NbjCW6h>

---

*Questo documento descrive il comportamento del programma così come è
progettato e scritto. Le due verifiche indicate nella sezione 8 sono alla
portata di chiunque usi il programma.*
