# Componenti di terze parti — NMS ITALIA MOD MENAGER

> Verificato il **25 settembre 2026** leggendo i file `.nuspec` dei pacchetti
> effettivamente ripristinati, non da fonti esterne o dalla memoria.## Compatibilità delle licenze

Il progetto e' distribuito con licenza **proprietaria** (tutti i diritti
riservati — vedi `LICENSE`).

Tutte le dipendenze sono sotto licenza **MIT** o **Apache License 2.0**. Entrambe
consentono la ridistribuzione all'interno di un'opera proprietaria: nessuna
impone di rendere pubblico il codice del progetto che le utilizza. Gli obblighi
che impongono — conservare l'avviso di copyright e il testo della licenza — sono
soddisfatti da questo documento e dai file di licenza inclusi nei pacchetti.

Non e' presente alcuna dipendenza sotto licenza copyleft (GPL, LGPL, AGPL) o
con clausole non commerciali. Una dipendenza copyleft avrebbe imposto di
rilasciare il codice del progetto, ed e' la ragione per cui viene verificata a
ogni aggiornamento.

---

## Dipendenze di esecuzione

Queste librerie fanno parte del software distribuito all'utente.

| Pacchetto | Versione | Licenza | Autore / progetto |
|---|---|---|---|
| Avalonia | 12.1.2 | MIT | AvaloniaUI OÜ — <https://avaloniaui.net> |
| Avalonia.Desktop | 12.1.2 | MIT | AvaloniaUI OÜ |
| Avalonia.Themes.Fluent | 12.1.2 | MIT | AvaloniaUI OÜ |
| Avalonia.Fonts.Inter | 12.1.2 | MIT | AvaloniaUI OÜ |
| CommunityToolkit.Mvvm | 8.4.2 | MIT | .NET Foundation |
| Microsoft.Extensions.DependencyInjection | 10.0.11 | MIT | Microsoft |
| Microsoft.Extensions.Logging | 10.0.11 | MIT | Microsoft |
| Microsoft.Extensions.Logging.Abstractions | 10.0.11 | MIT | Microsoft |
| Microsoft.Extensions.Options | 10.0.11 | MIT | Microsoft |
| Microsoft.Data.Sqlite | 10.0.12 | MIT | Microsoft |
| SharpCompress | 0.50.4 | MIT | Adam Hathcock — <https://github.com/adamhathcock/sharpcompress> |

### Dipendenze native di SQLite

`Microsoft.Data.Sqlite` usa SQLitePCLRaw per accedere alla libreria SQLite nativa.

| Pacchetto | Versione | Licenza |
|---|---|---|
| SQLitePCLRaw.bundle_e_sqlite3 | 2.1.12 | Apache-2.0 |
| SQLitePCLRaw.core | 2.1.12 | Apache-2.0 |
| SQLitePCLRaw.provider.e_sqlite3 | 2.1.12 | Apache-2.0 |

La libreria **SQLite** stessa e' di **pubblico dominio**. Non impone condizioni
sulla ridistribuzione.

---

## Dipendenze di sviluppo

Queste librerie servono solo a compilare ed eseguire i test. **Non** fanno parte
del software distribuito all'utente.

| Pacchetto | Versione | Licenza | Uso |
|---|---|---|---|
| Microsoft.NET.Test.Sdk | 17.14.1 | MIT | esecuzione dei test |
| xunit | 2.9.3 | Apache-2.0 | framework di test |
| xunit.runner.visualstudio | 3.1.4 | Apache-2.0 | integrazione dei test |
| coverlet.collector | 6.0.4 | MIT | copertura del codice |

---

## Font

| Risorsa | Licenza |
|---|---|
| **Inter** (via `Avalonia.Fonts.Inter`) | SIL Open Font License 1.1 |

La SIL Open Font License consente l'incorporamento e la ridistribuzione del
carattere all'interno di un'applicazione, incluse le applicazioni commerciali.

---

## Asset grafici

Gli asset grafici del progetto (logo, icone, illustrazioni) sono **originali**.

Non e' incluso, in nessuna forma:

- artwork ufficiale di No Man's Sky;
- loghi o marchi di Hello Games;
- colonne sonore o effetti sonori del gioco;
- asset estratti dai file di gioco.

L'identita' visiva del progetto e' ispirata a un tema fantascientifico ma non
riproduce elementi dell'interfaccia ufficiale del gioco.

---

## Marchi

**No Man's Sky** e **Hello Games** sono marchi dei rispettivi titolari.

NMS ITALIA MOD MENAGER e' un progetto community **non ufficiale**, non affiliato
ne' approvato da Hello Games. I marchi sono citati unicamente in forma
descrittiva, per indicare il gioco a cui il software si riferisce.

---

## Componenti esterni non inclusi

Il software **non incorpora** MBINCompiler, libMBIN o AMUMSS. Se in futuro
verranno integrati, saranno aggiunti a questo documento con la relativa licenza
dopo verifica.

---

## Come e' stato verificato questo elenco

> Verificato il **25 settembre 2026** leggendo i file `.nuspec` dei pacchetti
> effettivamente ripristinati, non da fonti esterne o dalla memoria.

L'elenco viene ricontrollato a ogni aggiornamento delle dipendenze. Una
dipendenza nuova entra nel progetto solo dopo la verifica della sua licenza: una
licenza copyleft o non commerciale renderebbe il progetto non distribuibile alle
condizioni attuali, e scoprirlo dopo la pubblicazione significherebbe ritirare
una versione.
