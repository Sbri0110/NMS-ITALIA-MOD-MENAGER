# Formato mod di No Man's Sky — stato verificato

> **Documento di riferimento tecnico.**
> Ogni affermazione qui riportata e' stata verificata su fonti ufficiali/primarie
> oppure direttamente sull'installazione presente sulla macchina di sviluppo.
> Data della verifica: **25 settembre 2026**.
> Quando una voce non e' verificabile, e' marcata esplicitamente come **NON VERIFICATO**.

Questo documento esiste per una ragione precisa: **il 90% delle guide in
circolazione su come si installano le mod di No Man's Sky e' obsoleto.** Se il
software si basasse su quelle guide, installerebbe le mod in una cartella che il
gioco non legge piu', e nessuna mod funzionerebbe.

---

## 1. La riscrittura di "Worlds Part II"

L'update **Worlds Part II** ha riscritto il sistema di modding del gioco. Questo
e' il cambiamento piu' importante degli ultimi anni e riguarda ogni singola riga
di codice di un mod manager.

### Prima (obsoleto)

```text
No Man's Sky/
  GAMEDATA/
    PCBANKS/
      MODS/               <- le mod andavano QUI
        MiaMod.pak        <- archivio compresso
      DISABLEMODS.TXT     <- la sua presenza disattivava tutte le mod
```

Caratteristiche del vecchio sistema:

- ogni mod era un **unico file `.pak`**;
- una mod **sostituiva interi file** di gioco;
- se due mod toccavano lo stesso file, **la seconda sovrascriveva la prima**;
- quindi il **load order era critico** e i conflitti erano la norma;
- per combinare piu' mod servivano tool esterni (AMUMSS e simili).

### Adesso (attuale)

```text
No Man's Sky/
  GAMEDATA/
    MODS/                       <- le mod vanno QUI (una cartella per mod)
      MiaMod/                   <- cartella dedicata, nome libero
        GCSPACESHIPGLOBALS.GLOBAL.EXML
        ...
    PCBANKS/                    <- solo dati di gioco, NON mod
      NMSARC.*.pak
  Binaries/
    SETTINGS/
      GCMODSETTINGS.MXML        <- stato mod: on/off globale e per mod
```

Caratteristiche del nuovo sistema:

| Aspetto | Comportamento attuale |
|---|---|
| Percorso di installazione | `<install>\GAMEDATA\MODS\<NomeMod>\` |
| Formato di pacchetto | **Nessun pacchetto**: cartella con i file dentro |
| `.MBIN` | **Sostituisce interamente** il file di gioco |
| `.EXML` | **Applica una patch parziale**: sostituisce solo le proprieta' dichiarate |
| `.MXML` | **Non funziona**, unica eccezione `LocTable.MXML` |
| `.PAK` | **Probabilmente non funziona** piu' |
| Conflitti | Molto ridotti: due `.EXML` che toccano proprieta' diverse **convivono** |
| Load order | "Non molto importante" — gli EXML si sovrappongono, gli MBIN sostituiscono |

**Conseguenza progettuale di enorme portata.** Il modello mentale "due mod
toccano lo stesso file = conflitto" e' **sbagliato** nel sistema attuale. Il
conflitto reale e' molto piu' fine: due mod sono in conflitto quando scrivono
**valori diversi sulla stessa proprieta'** dello stesso oggetto. Due mod che
modificano `ShipSpeed` e `ShipBoost` nello stesso file **non sono in conflitto**.

Questo e' esattamente cio' che il capitolato chiede al punto 11, ed e' anche
cio' che il motore di analisi deve implementare per non produrre falsi positivi
che renderebbero il software inutilizzabile.

### 1.1 La forma leggibile: da `.EXML` a `.MXML`

> **VERIFICATO il 26 settembre 2026** su fonti primarie: il README del
> repository `monkeyman192/MBINCompiler` e la sua documentazione utente.

Con Worlds Part II e' cambiata anche la forma leggibile che MBINCompiler
produce.

| | Prima di Worlds Part II | Adesso |
|---|---|---|
| MBINCompiler produce | `.EXML` | **`.MXML`** |
| Il gioco legge direttamente | `.EXML` | **`.EXML`** (invariato) |

Il README dello strumento lo dichiara esplicitamente:

> *"As of the Worlds part 2 update, MBINCompiler will no longer generate or
> handle EXML files, and will instead handle MXML files. This is to (finally)
> get MBINCompiler producing files in the same format as NMS expects. For
> modding purposes the MXML are not the actual files you need to place in a mod
> directory. To do this, you can rename the MXML file to EXML."*

**Cosa significa in pratica.** Chi crea una mod decompila un `.MBIN` e ottiene
un `.MXML`: e' la forma leggibile, ma **non e' il file che va messo nella
cartella della mod**. Va rinominato in `.EXML` per essere applicato come patch
parziale, oppure ricompilato in `.MBIN` per una sostituzione totale. Una mod
consegnata con i `.MXML` dentro non produce **alcun effetto**, e il gioco non
segnala nulla: e' indistinguibile da una mod che funziona male.

**`.MXML` e `.EXML` sono lo stesso formato.** Cambia l'estensione, non la
struttura: entrambi sono XML con radice `<Data template="...">` e proprieta'
`<Property name="..." value="..." />`. Un lettore scritto per gli `.EXML` legge
correttamente anche gli `.MXML`, ed e' esattamente quello che fa il programma.

**`.MBXML` non esiste.** Non compare in nessuna fonte del modding di No Man's
Sky, in nessuna versione di MBINCompiler e in nessuna guida. Le estensioni reali
sono `.MBIN`, `.MXML` e `.EXML`, con `LocTable.MXML` come unica eccezione per la
localizzazione. Un riferimento a `.MBXML` e' un refuso per `.MXML`.

---

## 2. Regole di installazione corrette

1. Scaricare la mod (tipicamente da Nexus Mods).
2. Estrarre l'archivio.
3. Andare in `<install>\GAMEDATA\`.
4. Creare `MODS\` se non esiste.
5. Mettere la **cartella della mod** dentro `GAMEDATA\MODS\`.
6. **Verificare che non ci sia una cartella di troppo.** Errore classico: se il
   livello piu' alto della cartella ha un nome fatto di soli numeri, e' quasi
   sempre un wrapper da rimuovere. Il gioco non trova i file e la mod non
   funziona, senza alcun messaggio d'errore.
7. Avviare il gioco e attendere la fine della splash screen.
8. Se e' andata a buon fine, il gioco mostra un avviso prima della selezione dei
   salvataggi.

### Errori di installazione piu' comuni (base del motore diagnostico)

| Sintomo | Causa | Verifica automatica possibile |
|---|---|---|
| La mod non cambia nulla | installata in `PCBANKS\MODS` | si: cartella sbagliata |
| La mod non cambia nulla | cartella annidata di troppo | si: nome del primo livello numerico |
| La mod non cambia nulla | file `.MXML` invece di `.EXML` | si: estensione |
| La mod non cambia nulla | `.MXML` prodotto da MBINCompiler e non rinominato in `.EXML` | si: estensione, piu' il numero di proprieta' dichiarate lette dal file |
| La mod non cambia nulla | file `.pak` in era 5.50+ | si: estensione |
| Nessuna mod funziona | `DisableAllMods = true` | si: lettura `GCMODSETTINGS.MXML` |
| Crash all'avvio | mod precedente a gen 2025 | si: confronto data/versione |
| Crash all'avvio | conflitto MBIN su stesso file | si: analisi proprieta' |
| La mod funziona a meta' | mod vecchia, struttura cambiata | parziale: confronto versione NMS |

---

## 3. `GCMODSETTINGS.MXML` — il file di stato delle mod

Percorso: `<install>\Binaries\SETTINGS\GCMODSETTINGS.MXML`

**Contenuto reale rilevato sull'installazione di sviluppo** (nessuna mod
installata, 161 byte):

```xml
<?xml version="1.0" encoding="utf-8"?>
<Data template="GcModSettings">
	<Property name="DisableAllMods" value="false" />
	<Property name="Data" />
</Data>
```

Fatti accertati:

- il template si chiama `GcModSettings`;
- esiste un flag globale **`DisableAllMods`** (booleano);
- esiste una proprieta' **`Data`**, che risulta **vuota** in assenza di mod;
- il file viene **rigenerato con i valori di default** se lo si cancella;
- `DisableAllMods` puo' **attivarsi da solo dopo un crash** con mod installate.
  Attenzione: questo **non** significa che il crash sia stato causato dalle mod.

> **VERIFICATO il 25 settembre 2026.** La struttura della proprieta' `Data` e'
> stata osservata su un'installazione reale, avviando il gioco con una mod
> presente. Il contenuto completo e' documentato al paragrafo 3.1.

### 3.1 La proprieta' `Data`: struttura verificata

Il file completo, con una mod installata e il gioco avviato:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Data template="GcModSettings">
	<Property name="DisableAllMods" value="false" />
	<Property name="Data">
		<Property name="Data" value="GcModSettingsInfo" _index="0">
			<Property name="Name" value="NMSI-OSSERVAZIONE" />
			<Property name="Author" value="" />
			<Property name="ID" value="0" />
			<Property name="AuthorID" value="0" />
			<Property name="LastUpdated" value="0" />
			<Property name="ModPriority" value="0" />
			<Property name="Enabled" value="true" />
			<Property name="EnabledVR" value="true" />
			<Property name="Dependencies" />
		</Property>
	</Property>
</Data>
```

**Come si legge.** `Data` contiene una lista di voci, una per mod. Ogni voce e'
un `<Property name="Data">` con `value="GcModSettingsInfo"` e un attributo
`_index` che e' la posizione nella lista (0-based).

| Proprieta' | Significato accertato | Note |
|---|---|---|
| `Name` | Nome della cartella della mod | **Corrisponde esattamente** al nome della cartella in `GAMEDATA\MODS`. E' la chiave di collegamento fra il file e il disco. |
| `Author` | Autore | Stringa vuota quando non noto |
| `ID` | Identificativo numerico della mod | `0` quando non noto — presumibilmente l'id di Nexus Mods |
| `AuthorID` | Identificativo numerico dell'autore | `0` quando non noto |
| `LastUpdated` | Data di ultimo aggiornamento | `0` quando non nota |
| **`ModPriority`** | **Ordine di caricamento** | `0` per la prima mod |
| **`Enabled`** | **Attivazione della singola mod** | `true` / `false` |
| `EnabledVR` | Attivazione specifica per VR | Separata da `Enabled` |
| `Dependencies` | Elenco di dipendenze | Elemento vuoto quando non ce ne sono |

**Tre conseguenze importanti.**

1. **L'attivazione per singola mod esiste**, ed e' la proprieta' `Enabled`. Il
   software non deve piu' aggirarla spostando cartelle: si modifica il file, che
   e' il meccanismo nativo del gioco.

2. **Esiste un ordine di caricamento**, ed e' `ModPriority`. E' il punto 80 del
   capitolato, e non va inventato: e' il gioco a leggerlo.

3. **Il gioco conserva metadati che provengono dalla fonte della mod** — `ID`,
   `AuthorID`, `LastUpdated`. Sono i campi che l'API di Nexus puo' popolare, e
   che permettono al software di collegare una mod installata alla sua pagina
   originale senza indovinare dal nome.

**Nota sulla scrittura.** L'attributo `_index` determina la posizione nella
lista: riscrivendo il file va mantenuto coerente con l'ordine desiderato.
L'attributo `value="GcModSettingsInfo"` identifica il tipo della voce e va
preservato su ogni elemento.

**Nota sul nome.** `Name` e' il nome della **cartella**. Rinominare la cartella
di una mod senza aggiornare questa proprieta' fa perdere il collegamento: il
gioco tratterebbe la mod come nuova e quella vecchia come rimossa. Il software
deve quindi aggiornare entrambi insieme.

### Safe Mode — come si implementa davvero

Il capitolato (punto 10) chiede "AVVIA SENZA MOD". Il sistema attuale offre un
meccanismo nativo e pulito:

- `DisableAllMods = true` disattiva **tutte** le mod senza cancellarne nessuna;
- ripristinare `false` riporta tutto esattamente come prima.

E' la soluzione corretta: **non** spostare cartelle, **non** rinominare file.
Si tocca un singolo booleano in un file di testo, con backup preventivo. Il
ripristino e' garantito e non c'e' rischio di lasciare il gioco in stato
incoerente.

---

## 4. Cronologia e compatibilita' delle mod

- **Soglia dura:** le mod **non aggiornate prima del 29 gennaio 2025** non
  funzionano con NMS 5.50+.
- Ogni mod `.MBIN` compilata con una versione precedente puo' **non essere piu'
  decompilabile** se il formato di quel file e' cambiato tra le versioni.
- Le mod `.EXML` sono intrinsecamente piu' resistenti agli aggiornamenti, perche'
  dichiarano solo le proprieta' che modificano.

Questa e' la base dell'**Obsolete Mod Detector** (punto 16) e del **Game Update
Guard** (punto 15): non si guarda la data in modo ingenuo, si guarda se le
strutture toccate dalla mod esistono ancora.

---

## 5. Piattaforme

| Piattaforma | Supporto mod | Note |
|---|---|---|
| Steam (Windows) | Si | percorso classico in `steamapps\common` |
| GOG | Si | percorso classico |
| **Xbox PC / Microsoft Store / Game Pass** | **Si** | supporto confermato; installazione sotto `C:\XboxGames\<Gioco>\Content` |
| Steam su macOS | **No** | la release Mac non supporta mod |

### Verifica diretta sulla macchina di sviluppo

```text
Piattaforma : Xbox PC (Game Pass)
Percorso    : C:\XboxGames\No Man's Sky\Content
Versione    : 7.3.0.0  (da appxmanifest.xml, Identity Name="HelloGames.NoMansSky")
Eseguibile  : Binaries\NMS.exe
```

Esiti dei test di accessibilita':

| Test | Esito |
|---|---|
| `GAMEDATA` scrivibile | **Si** |
| `GAMEDATA\MODS` creabile | **Si** |
| `Binaries\SETTINGS\GCMODSETTINGS.MXML` presente | **Si** (161 byte) |
| `GAMEDATA\PCBANKS` presente | Si (contiene solo `NMSARC.*.pak` di gioco) |
| `GAMEDATA\MODS` presente | **No** — da creare al primo utilizzo |

**Conclusione importante:** l'idea diffusa che la versione Game Pass non
permetta le mod **non e' piu' vera** e, soprattutto, **non e' vera su questa
macchina**: la cartella di gioco e' accessibile in scrittura. Il software deve
quindi trattare Xbox PC come piattaforma di prima classe, non come caso limite.
Il percorso `C:\XboxGames\...` e' comunque da **rilevare**, non da hardcodare:
su altre macchine la libreria Xbox puo' risiedere su un altro volume.

---

## 6. Nexus Mods — API ufficiale

> ### ✅ MIGRAZIONE COMPLETATA — 25/09/2026, API v3 implementata
>
> La v1 è **legacy**, la v3 è la generazione corrente. Il software ora supporta
> **entrambe**, e sceglie per ogni dato la fonte che lo fornisce davvero.
>
> **Riferimento:** `https://api.nexusmods.com/openapi.yaml` (OpenAPI 3.0.3,
> `info.version` = `3.0.0`). Il file è pubblicato da Nexus e non richiede
> autenticazione: chiunque può verificare quanto segue.
>
> | | v1 (legacy) | v3 (corrente) |
> |---|---|---|
> | Base URL | `https://api.nexusmods.com` | `https://api.nexusmods.com/v3` |
> | Autenticazione | header `apikey` | header `apikey` **oppure** Bearer JWT |
> | Identificativi | numeri (`mod_id`) | **stringhe** (`game_scoped_id`) |
> | Forma della risposta | radice = oggetto | **busta** `{ "data": ... }` |
> | Errori | corpo arbitrario | **ProblemDetails** (`type`/`title`/`status`/`detail`/`instance`) |
> | Chiave API | pagina del profilo | `https://www.nexusmods.com/settings/api-keys` |
> | Header di rate limit | documentati | **non documentati** nella specifica |
>
> #### Il dato che ha deciso l'architettura
>
> **Tutte le rotte della v3 che servono a un gestore di mod sono marcate
> `Experimental`.** Verificato leggendo il campo `x-badges` di ogni operazione
> nella specifica:
>
> | Rotte | Stabilita' dichiarata |
> |---|---|
> | `GET /games/{game_domain}/mods/{game_scoped_id}` | **Experimental** |
> | `GET /mods/{id}/files` | **Experimental** |
> | `GET /mod-files/{id}` | **Experimental** |
> | `GET /mod-files/{id}/versions` | **Experimental** |
> | `GET /mod-file-versions/{id}` | **Experimental** |
> | `POST /mod-file-versions/{id}/download-repacked` | **Experimental** |
> | `POST /mods/batch` | **Experimental** |
> | `POST /uploads`, `POST /collections` | stable |
>
> La specifica stessa definisce il badge: *«Experimental — May change
> significantly or be removed. **Not recommended for production**.»*
>
> Le uniche rotte **stable** della v3 riguardano il caricamento di mod e le
> collezioni — cioè la pubblicazione, non la lettura. Per un gestore di mod la
> v3 è, oggi, interamente sperimentale.
>
> #### Cosa la v3 non espone
>
> Lo schema `Mod` della rotta dei metadati dichiara **quattro** campi:
> `id`, `game_scoped_id`, `game_id`, `name`. **Non** dichiara autore, versione,
> riepilogo, numero di download, endorsement né data di aggiornamento. La v1 li
> fornisce tutti.
>
> Lo schema `ModFile` dichiara **due** campi: `id`, `name`. La versione e la
> categoria stanno nella *versione* del file, che richiede una seconda chiamata.
>
> **Conseguenza sui costi:** elencare i file di una mod con la v3 costa
> **1 + N** richieste (una per l'elenco, N per le versioni dei gruppi), mentre la
> v1 ne costa **1**. Su un limite di 2.500 richieste al giorno, la differenza non
> è teorica.
>
> #### Nessuna rotta di download
>
> La v1 ha `…/files/{id}/download_link.json`. La v3 **non ha nulla di
> equivalente**: l'unica rotta che restituisce un link è
> `POST /mod-file-versions/{id}/download-repacked`, marcata *Experimental* e
> appartenente al flusso delle collezioni, non a quello dei singoli file.
> **Il download tramite API resta quindi non disponibile sulla v3.** Il
> comportamento progettato non cambia: si mostra `[APRI SU NEXUS MODS]` e si
> apre il browser. Nessuno scraping, in nessun caso.
>
> #### Nessuna rotta utente
>
> Nell'elenco dei percorsi della v3 **non compare alcun endpoint utente**: non
> esiste un equivalente di `/v1/users/validate.json`. La validazione della
> chiave resta quindi sulla v1 **anche con la preferenza impostata su "solo
> v3"**: senza di essa non ci sarebbe alcun modo di dire all'utente se la
> propria chiave funziona.
>
> #### Identificativi: la trappola
>
> Nella v3 gli identificativi sono **stringhe**. L'identità della mod è un UID
> composito, e la specifica dichiara la scomposizione:
>
> ```
> modId  = id & 0xFFFFFFFF
> gameId = id >> 32
> ```
>
> La v3 espone comunque `game_scoped_id`, che è il numero che compare nel link
> della mod ed è quello che serve. Il software lo legge, e ricava l'altro
> dall'UID solo se il primo manca.
>
> **Attenzione:** l'identificativo scaricabile cambia significato fra le due
> versioni. Nella v1 è il **file**; nella v3 è la **versione** del file. I due
> spazi di identificativi non coincidono e non vanno confrontati.
>
> ### Architettura implementata
>
> ```
> NexusApiClient          (facciata: sceglie la fonte, gestisce la chiave)
>   ├── NexusApiTransport     (HTTP, auth, 429, timeout, offline — una volta sola)
>   ├── NexusApiV1Client      (adattatore v1, legacy ma stabile e completo)
>   └── NexusApiV3Client      (adattatore v3, corrente ma sperimentale e piu' povero)
> ```
>
> **Regola di scelta (preferenza `Automatico`, predefinita):** si usa la
> versione che fornisce il dato richiesto, preferendo la più recente a parità di
> completezza.
>
> | Dato | Fonte | Perché |
> |---|---|---|
> | Metadati della mod | **v3**, completati dalla **v1** | La v3 è la generazione corrente; la v1 riempie i campi che la v3 non espone |
> | Elenco dei file | **v1** | Fornisce in 1 richiesta ciò che alla v3 ne costa 1+N, e in più espone dimensione e nome del file scaricato |
> | Validazione chiave | **v1** | Unica rotta esistente |
>
> La scelta è esposta all'utente in **Impostazioni → Versione dell'API Nexus**,
> con tre valori: *Automatico*, *Solo v3*, *Solo v1*. La preferenza è
> conservata nella tabella `Settings` del database locale e applicata
> all'avvio.
>
> **Provenienza dichiarata.** Ogni risultato porta con sé
> `SourceVersion` (la fonte principale) e `CompletedBy` (la fonte che ha
> completato i campi mancanti, se ce n'è stata una). La parzialità dei dati non
> è un dettaglio interno: è un'informazione che la UI può mostrare.
>
> **Copertura dei test:** 49 test dedicati alla mappatura delle due versioni,
> compresi i casi che contano — la v3 che *non deve* inventare i metadati che
> non espone, il limite sul numero di letture di versioni, la busta `data`
> assente, gli identificativi non interpretabili, e il ripiego da v3 a v1.

### Riferimento storico — API v1 (legacy)

Le informazioni seguenti descrivono la v1, che il software usa attualmente. Sono
conservate perché restano corrette per quella versione.

Base URL: `https://api.nexusmods.com/` — versione **v1**, OAS 2.0.

**Autenticazione.** Due strade, entrambe ufficiali:

1. **API key personale** dell'utente, inviata nell'header `apikey`.
2. **SSO Nexus Mods**, per integrazioni in cui l'utente non deve gestire chiavi.
   Demo ufficiale: `github.com/Nexus-Mods/sso-integration-demo`.

> La documentazione ufficiale e' esplicita: **"NEVER share your personal API
> keys with any other user, or include it as part of your own software."**
> Quindi: **nessuna API key hardcodata**, mai. La chiave, se fornita
> dall'utente, va in Windows Credential Manager / DPAPI (punto 30).

**Rate limit** (per utente):

- 2.500 richieste in 24 ore;
- superata la soglia, 100 richieste/ora;
- header di risposta: `X-RL-Hourly-Limit`, `X-RL-Hourly-Remaining`,
  `X-RL-Hourly-Reset`, `X-RL-Daily-Limit`, `X-RL-Daily-Remaining`,
  `X-RL-Daily-Reset`;
- superamento → **HTTP 429**;
- nginx rifiuta oltre **30 richieste/secondo**;
- l'unica rotta che **non** consuma quota e' `/v1/users/validate.json`.

**User-Agent obbligatorio.** La documentazione chiede una stringa che identifichi
applicazione, libreria e sistema operativo. Esempio ufficiale (Vortex):
`NexusApiClient/0.7.3 (Windows_NT 10.0.17134; x64) Node/8.9.3`

### Endpoint verificati

| Metodo | Rotta | Uso nel progetto |
|---|---|---|
| GET | `/v1/games/nomanssky/mods/{id}.json` | metadata mod |
| GET | `/v1/games/nomanssky/mods/{id}/files.json` | elenco file |
| GET | `/v1/games/nomanssky/mods/{mod_id}/files/{file_id}.json` | dettaglio file |
| GET | `/v1/games/nomanssky/mods/{mod_id}/files/{id}/download_link.json` | link di download |
| GET | `/v1/games/nomanssky/mods/{id}/changelogs.json` | changelog |
| GET | `/v1/games/nomanssky/mods/updated.json` | mod aggiornate |
| GET | `/v1/games/nomanssky/mods/latest_added.json` | ultime aggiunte |
| GET | `/v1/games/nomanssky/mods/latest_updated.json` | ultime aggiornate |
| GET | `/v1/games/nomanssky/mods/trending.json` | tendenze |
| GET | `/v1/games/nomanssky/mods/md5_search/{md5}.json` | **identificare una mod da hash** |
| GET | `/v1/games/nomanssky.json` | info gioco |
| GET | `/v1/users/validate.json` | validare la chiave |
| GET | `/v1/user/tracked_mods.json` | mod seguite |

Il dominio gioco da usare e' **`nomanssky`**.

**Nota su `md5_search`.** E' una funzione molto utile per il **Duplicate
Detector** (punto 78): dato l'hash di un archivio, si risale alla mod su Nexus.
Attenzione: l'endpoint vuole un **MD5**, mentre internamente il progetto usa
**SHA-256** (punto 27). Servira' quindi calcolare **entrambi** gli hash per gli
archivi importati, e documentare il perche'.

**Download automatico.** Il capitolato impone di non aggirarlo. La rotta
`download_link.json` genera un link, ma l'uso effettivo dipende dal tipo di
account, dalle policy correnti e dai termini di servizio. Comportamento
progettato: si tenta la rotta ufficiale; se non e' consentita o fallisce, si
mostra `[APRI SU NEXUS MODS]` e si apre il browser. **Nessuno scraping.**

---

## 7. Strumenti di riferimento nel panorama modding

| Strumento | Ruolo | Nota per il progetto |
|---|---|---|
| **MBINCompiler / libMBIN** (monkeyman192) | MBIN ⇄ EXML | ogni release e' legata a una versione di NMS |
| **AMUMSS** | modding via script Lua | **non** e' un mod manager |
| **Vortex** | mod manager generico | supporta la nuova posizione delle mod |

**Vincolo critico su MBINCompiler.** La documentazione ufficiale e' chiara:
*"every update to the game breaks any number of MBIN formats"* e *"each version
of MBINCompiler is tied to a specific version of NMS"*. Quindi il software
**non** deve mai assumere che la versione di MBINCompiler presente funzioni con
la versione di NMS installata: deve **verificare la corrispondenza** e, se non
combacia, dichiarare la funzionalita' non disponibile invece di produrre analisi
errate (punto 12 e punto 72).

---

## 8. Riepilogo delle decisioni prese da questo documento

1. Il percorso di installazione e' **`GAMEDATA\MODS`**, non `PCBANKS\MODS`.
   Il vecchio percorso viene **rilevato e segnalato** come errore, non usato.
2. Nessun supporto ai `.pak` come formato di installazione primario. Un `.pak`
   trovato in un archivio viene segnalato come **probabilmente inefficace**.
3. Il **Safe Mode** agisce su `DisableAllMods`, non sul filesystem.
4. Il **Conflict Scanner** lavora a livello di **proprieta'**, non di file.
5. L'**Obsolete Mod Detector** usa la soglia **29/01/2025** e l'analisi delle
   strutture, non la sola data.
6. **Xbox PC e' una piattaforma supportata** e verificata.
7. L'integrazione Nexus usa **solo** API ufficiali — nessuno scraping, in nessun
   caso. La chiave dell'utente sta nel Credential Manager di Windows e non ha
   alcun valore predefinito.
8. L'integrazione Nexus supporta **entrambe le generazioni** dell'API. La v3 e'
   la fonte preferita per i metadati perche' e' quella corrente; la v1 completa
   i campi che la v3 non espone, fornisce l'elenco dei file e valida la chiave,
   perche' e' l'unica con rotte **stabili** e metadati completi. La scelta e'
   configurabile dall'utente e la provenienza dei dati e' dichiarata nel
   risultato (`SourceVersion`, `CompletedBy`).
9. Il **download tramite API non e' disponibile sulla v3**. Finche' resta cosi',
   il comportamento e' quello gia' previsto: si apre la pagina della mod nel
   browser. Non si aggira il limite.
10. La **forma leggibile** dei dati di gioco e' `.MXML` (prodotta da
    MBINCompiler da Worlds Part II), ma il gioco applica solo `.EXML`. Il
    programma legge entrambe — sono lo stesso formato XML — e **avvisa** quando
    un file va rinominato, perche' una mod consegnata con i `.MXML` non produce
    alcun effetto e non lo segnala. `.MBXML` **non esiste**: e' un refuso.
11. **Due mod in conflitto possono essere unite.** Poiche' un `.EXML` dichiara
    solo le proprieta' che modifica, unire due mod significa costruire l'unione
    delle proprieta' dichiarate e scegliere esplicitamente un valore dove le due
    si contraddicono. E' un'operazione sull'XML: **non richiede MBINCompiler**.
    Due `.MBIN` con lo stesso nome, invece, **non si possono unire**: sono
    sostituzioni totali dello stesso file, e la fusione viene rifiutata
    dichiarando il motivo.
