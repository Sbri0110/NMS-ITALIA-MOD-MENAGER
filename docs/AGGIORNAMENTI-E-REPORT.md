# Aggiornamenti mod e report di assistenza — 1.3.0

## Aggiornare una mod

1. Apri **Le mie mod** e premi **Controlla aggiornamenti mod**. Il controllo parte soltanto su richiesta e richiede una chiave API Nexus nelle Impostazioni.
2. Espandi **Aggiornamenti e versioni delle mod**. Ogni voce indica la versione registrata e i file pubblicati. Il confronto numerico distingue versioni superiori, precedenti e uguali; etichette non numeriche restano non confrontabili. Quando l'autore ha dichiarato una catena di aggiornamento su Nexus, il successore del file installato viene indicato.
3. **Scegli il file e la variante**: non viene selezionato o installato automaticamente un file opzionale. Una versione superiore non certifica la compatibilita' con la tua versione del gioco.
4. Con account Premium, premi **Aggiorna con il file selezionato**. Con account gratuito, apri i file su Nexus, usa **Mod Manager Download** e incolla il collegamento `.nxm` completo nel campo della stessa mod, quindi premi il pulsante. Il collegamento deve corrispondere esattamente a mod e file scelti. La chiave temporanea non viene salvata.
5. Chiudi No Man's Sky prima di aggiornare. Il programma ricontrolla il file disponibile e il contenuto locale, crea una copia completa nella schermata Backup e solo dopo sostituisce la mod con una transazione. Se il backup fallisce, l'aggiornamento non parte.

La cartella della mod conserva il nome originale, anche se l'archivio nuovo ha un nome diverso. I file della versione vecchia non presenti nella nuova vengono rimossi. Il file di stato del gioco non viene riscritto: attivazione e Safe Mode restano quelle correnti.

Gli archivi di aggiornamento contenenti eseguibili, anche rinominati con estensioni innocue, o privi di contenuti applicabili vengono rifiutati. Un errore durante la sostituzione usa il recupero transazionale; un recupero incompleto viene dichiarato e resta la copia completa in Backup.

**Annulla controllo** e **Annulla aggiornamento** interrompono le fasi annullabili. Dopo il completamento della transazione, la registrazione della provenienza deve finire: interromperla lascerebbe la versione su disco diversa da quella dichiarata.

## Mod installate prima della versione 1.3.0

La versione precedente non registrava il file Nexus installato. Il programma non indovina questi dati dal nome della cartella.

Seleziona la mod nell'elenco, incolla l'indirizzo HTTPS della sua pagina nel pannello **Esplora e installa da Nexus Mods**, poi premi **Associa la mod selezionata al link Nexus**. L'associazione registra la pagina e l'impronta del contenuto presente; la versione resta **non identificata**. Il prossimo aggiornamento scelto esplicitamente registra anche file e versione.

Se hai modificato a mano i file della mod, l'aggiornamento viene bloccato finche' non la associ nuovamente. L'associazione significa che scegli di usare il contenuto attuale come punto di partenza: eventuali personalizzazioni saranno sostituite dall'archivio scelto e conservate nelle copie precedenti.

## Tornare alla versione precedente

Premi **Ripristina la versione precedente** nella voce della mod. Il comando resta disponibile dopo aver riaperto il programma e premuto **Rileggi dal disco**, anche senza connessione. Ripristina solo quella mod; le altre mod e il file di attivazione corrente restano invariati. Prima del ripristino viene creata una nuova copia completa della configurazione corrente.

Il pulsante conserva **un livello di ritorno**. Le copie complete precedenti restano nella schermata Backup. Ripristinare una copia completa da quella schermata riguarda l'intera configurazione e anche il suo stato, come gia' indicato dalla funzione Backup.

Se i file della mod sono cambiati dopo l'aggiornamento, il ripristino della singola mod viene bloccato per non cancellare le modifiche. Se la cartella corrisponde esattamente alla versione precedente dopo un recupero o un ripristino completo, la provenienza viene riallineata al successivo controllo.

## Preparare un report di assistenza

1. Apri **Diagnostica** ed espandi **Report per l'assistenza**. La preparazione funziona anche quando il gioco non viene rilevato.
2. Scegli se includere i log recenti. Sono letti soltanto gli ultimi tre file `.log`, fino agli ultimi 64 KiB e 100 righe per file. Non vengono allegati database, impostazioni o salvataggi.
3. Premi **Prepara report**: una nuova raccolta in sola lettura mostra versione dell'app e del sistema, piattaforma e versione del gioco, mod e versioni registrate, controlli e confronti fra mod. Errori e verifiche incomplete sono dichiarati.
4. Controlla l'anteprima e premi **Salva report...** per scegliere un file `.txt`. Il salvataggio usa un file temporaneo e sostituzione atomica; un annullamento non cancella un report gia' esistente.

Percorsi assoluti Windows e dei profili Unix riconosciuti, nome dell'utente corrente, email, parametri dei collegamenti e credenziali riconoscibili vengono oscurati nell'intero testo, compresi i log. Nomi delle mod, descrizioni e altri testi liberi restano utili alla diagnosi: rileggi l'anteprima prima di condividerla se contengono informazioni personali. Un filtro non puo' identificare qualunque dato privato scritto liberamente in un messaggio.

Il report viene soltanto preparato e salvato localmente. Lo condividi tu, per esempio allegandolo alla richiesta di assistenza su Discord. Non viene inviato dal programma.

## Riferimento tecnico Nexus

Gli ID di file usati per i download e i collegamenti nxm sono quelli dell'API v1. La preferenza v3 resta disponibile per gli altri usi del client; gli ID delle versioni di file v3 non vengono confusi con gli ID v1.

Le forme dell'elenco e della catena `file_updates`, e le categorie `PATCH` e `OPTION`, sono state confrontate con i tipi del [client ufficiale Nexus Mods](https://github.com/Nexus-Mods/node-nexus-api/blob/master/src/types.ts).
