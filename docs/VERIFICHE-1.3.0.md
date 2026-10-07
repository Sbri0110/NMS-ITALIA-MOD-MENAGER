# Verifiche della versione 1.3.0

La versione 1.3.0 del 7 ottobre 2026 aggiunge aggiornamenti delle mod Nexus
con backup e ripristino della singola mod, e report di assistenza esportabili.

## Verifiche completate

- **812 test automatici superati**, nessun fallimento o test ignorato.
- Build Release e pacchetto Windows x64 con runtime .NET incluso.
- Test con archivi, file, database, backup e transazioni reali; servizio Nexus
  e stato del processo del gioco simulati.
- Coperti errori di copia e backup, annullamento durante la sostituzione,
  ripristino dopo riavvio, copie mancanti, errori di salvataggio dei metadati,
  file ritirati, collegamenti nxm non corrispondenti e modifiche esterne.
- Verificati filtro del report, log limitati o esclusi e conservazione del
  file precedente in caso di annullamento del salvataggio.
- Interfaccia renderizzata e controllata con dati dimostrativi a 1280x840,
  1024x840 e alla dimensione minima di 920x620, anche dopo scorrimento.
- Contenuto dell'archivio controllato: eseguibile, runtime, licenze e istruzioni;
  nessun sorgente, simbolo PDB, database o dato locale dell'utente.

## Limiti dichiarati

Non e' stato effettuato un collaudo completo con account Nexus personale e
installazione reale di No Man's Sky. I test automatici e le immagini
dimostrative non sostituiscono questa prova. La presenza di un aggiornamento
non garantisce compatibilita' con la versione del gioco o con altre mod.

Il ripristino rapido conserva un livello precedente per mod; le copie complete
si gestiscono nella schermata Backup. Il report oscura dati sensibili
riconoscibili, ma il testo libero va controllato prima della condivisione.
Il programma non e' firmato digitalmente.

Per installazione e utilizzo: [guida](AGGIORNAMENTI-E-REPORT.md).
