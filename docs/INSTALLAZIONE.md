# Installazione e avvio

NMS ITALIA MOD MENAGER non si installa nel senso tradizionale: non c'è un
programma di installazione, non scrive nel registro di sistema e non tocca
le cartelle di Windows. Si estrae e si avvia.

---

## Requisiti

| | |
|---|---|
| **Sistema** | Windows 10 o successivo, 64 bit |
| **Gioco** | No Man's Sky su Steam, Xbox PC / Game Pass o GOG |
| **Runtime .NET** | **Non serve** — è incluso nel pacchetto |
| **Connessione** | **Non serve** — il programma funziona per intero offline |

---

## 1. Ottenere il pacchetto

Scarica `NMSItalia.ModMenager.zip` dalla pagina delle release e scompattalo
dove preferisci. **Non scompattarlo dentro la cartella di No Man's Sky.**

Va bene ovunque: sul Desktop, in `C:\Programmi`, su un'altra unità. Il
programma non ha bisogno di stare in un posto preciso.

Il pacchetto è **autosufficiente**: contiene il runtime .NET, quindi non serve
installare nulla. Il programma è disponibile **solo in questa forma** — non
esiste un programma di installazione, e il codice sorgente non viene
distribuito né è pubblico.

Scarica il pacchetto **solo** dai canali ufficiali: la pagina delle release e il
Discord di NMS Italia. Una copia presa altrove non è verificabile.

---

## 2. Avviare

Doppio clic su **`NMSItalia.ModMenager.App.exe`**.

**È l'unico file `.exe` nella cartella.** Gli altri file — qualche centinaio di
`.dll` — sono il motore del programma: non vanno aperti, spostati né toccati.

Non spostare l'eseguibile fuori dalla sua cartella: ha bisogno dei file che gli
stanno accanto. Per averlo sul Desktop, crea un **collegamento**: tasto destro
sul file → *Invia a* → *Desktop*.

### L'avviso di Windows

Al primo avvio è probabile che compaia **"Windows ha protetto il PC"**
(SmartScreen).

Succede per qualsiasi programma non firmato digitalmente. Una firma
digitale costa alcune centinaia di euro l'anno e questo è un progetto
amatoriale gratuito, quindi non c'è.

Per procedere:

1. clicca **"Ulteriori informazioni"**;
2. clicca **"Esegui comunque"**.

Non è un errore e non significa che il file sia danneggiato. Il programma non
è firmato digitalmente: è una scelta dichiarata, non un difetto nascosto.

Se vuoi essere sicuro che il file sia arrivato integro, confronta il suo hash
SHA-256 con quello pubblicato nella pagina della release (la procedura è in
fondo a questo documento).

---

## 3. Primo avvio

Il programma cerca No Man's Sky da solo e ne valida l'installazione. Se lo
trova, lo mostra nella schermata Home con la versione rilevata.

Se non lo trova:

1. apri **Impostazioni**;
2. indica la cartella di installazione a mano.

La cartella da indicare è quella che contiene `Binaries` e `GAMEDATA`, per
esempio `C:\XboxGames\No Man's Sky\Content`.

**Non viene chiesto alcun account.** Nessuna registrazione, nessuna
attivazione, nessuna email.

---

## 4. Chiave API di Nexus Mods (opzionale)

Serve **solo** per le funzioni online: controllo degli aggiornamenti delle
mod a partire dalla loro pagina su Nexus.

Tutto il resto — installazione, profili, backup, conflitti, diagnostica —
funziona senza. Se non la inserisci, il programma dichiara le funzioni
online come non disponibili invece di farle fallire.

Per inserirla:

1. apri la pagina `https://www.nexusmods.com/settings/api-keys` nel
   browser e genera una chiave personale;
2. copiala;
3. in **Impostazioni**, incollala nel campo e premi **"Salva e verifica"**.

La chiave viene conservata nel **Credential Manager di Windows**, legata al
tuo account. Non viene scritta in nessun file di configurazione, non viene
mai mostrata dopo il salvataggio e non compare nei registri.

Vedi `PRIVACY.md` per il dettaglio completo.

---

## 5. Installare la prima mod

1. **Le mie mod** → **Installa da archivio**
2. scegli il file `.zip`, `.7z` o `.rar` scaricato da Nexus;
3. il programma analizza l'archivio **prima** di toccare qualsiasi cosa e
   mostra cosa contiene;
4. conferma.

Il programma colloca la mod in `GAMEDATA\MODS`, che è il percorso corretto
dal sistema introdotto con Worlds Part II. Non devi sapere dove va.

### Dopo l'installazione

Alcune operazioni richiedono che il gioco abbia **registrato** la mod
almeno una volta. La registrazione avviene al primo avvio del gioco
successivo all'installazione.

Se provi ad attivare o disattivare una mod appena installata e il programma
dice che non è ancora registrata, avvia No Man's Sky una volta e riprova.
Non è un difetto: il programma non può attivare una mod che il gioco non
conosce ancora.

---

## 6. Dove finiscono i dati

Tutto in una cartella sola:

```
%LOCALAPPDATA%\NMS ITALIA MOD MENAGER\
```

Per aprirla: premi `Win+R`, incolla il percorso, invio.

---

## 7. Disinstallazione

1. cancella la cartella del programma (quella dove hai scompattato lo zip);
2. cancella `%LOCALAPPDATA%\NMS ITALIA MOD MENAGER\`;
3. se avevi inserito la chiave API: in **Impostazioni**, premi
   **"Rimuovi"** *prima* di cancellare la cartella, così la chiave viene
   tolta dal Credential Manager.

**Attenzione al punto 2.** Cancellando quella cartella perdi i profili, le
copie di sicurezza e il catalogo delle mod. Le mod installate nella
cartella del gioco **non** vengono toccate: se vuoi rimuoverle, fallo dal
programma prima di disinstallarlo.

---

## Problemi comuni

### Il programma si apre e non trova il gioco

La cartella indicata a mano deve contenere `Binaries` e `GAMEDATA`. Se hai
indicato la cartella sbagliata, il programma lo dice: la validazione
strutturale non si limita a controllare che la cartella esista.

### Una mod è installata ma nel gioco non cambia nulla

Apri **Diagnostica** → **Ripeti la verifica**. Il controllo "Mod che non
produrranno alcun effetto" spiega se la mod è confezionata per il vecchio
sistema a `.pak` o con estensioni che il gioco non legge più.

È la causa più frequente, e non dipende da te.

### Il gioco si chiude all'avvio e non so quale mod sia

**Diagnostica** → **Inizia la ricerca**.

Disattiva metà delle mod e ti chiede di avviare il gioco. A seconda di cosa
riferisci, restringe il campo. Con cinquanta mod servono sei avvii, non
cinquanta.

Le mod tornano allo stato di partenza appena finisce, e anche se
interrompi.

### Nessuna mod funziona, tutte insieme

**Diagnostica** → cerca il controllo "Safe Mode attivo".

Se il gioco è impostato per ignorare tutte le mod, nessuna viene caricata
qualunque sia il loro stato individuale.

### Il controllo degli aggiornamenti non funziona

Serve la chiave API di Nexus. Senza, il programma lo dichiara invece di
fallire in silenzio.

### Il programma non si avvia

Controlla che Windows sia a 64 bit. Il pacchetto contiene solo
l'esecuzione a 64 bit.

Se il pacchetto è stato estratto con un programma che ha alterato i file —
per esempio scompattando solo l'eseguibile e non il resto — riestrai
l'archivio per intero in una cartella vuota. L'eseguibile ha bisogno delle
`.dll` che gli stanno accanto.

---

## Serve una mano?

Entra nel **Discord di NMS Italia**: è il posto dove si trovano supporto,
segnalazioni e aggiornamenti.

<https://discord.gg/KB2NbjCW6h>

Quando chiedi aiuto per un problema, allega il **report diagnostico** che
il programma esporta dalla sezione Diagnostica: contiene cosa ha verificato
e cosa ha trovato, e fa risparmiare parecchi passaggi.

---

## Verifica dell'integrità

Ogni release pubblica gli hash SHA-256 degli archivi. Per verificarli:

```powershell
Get-FileHash .\NMSItalia.ModMenager.zip -Algorithm SHA256
```

Confronta il valore con quello pubblicato nella pagina della release. Se
non corrisponde, **non usare il file**.
