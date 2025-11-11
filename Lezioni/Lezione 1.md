## Interfacce

Un'interfaccia è l'insieme di funzionalità che viene offerto da ogni livello di una architettura.

Le interfaccie sono divise in due tipi:
- Interfaccie di servizio: API che vengono offerte ai livelli superiori
- Interfaccie peer: che servono a comunicare tra i servizzi allo stesso livello


> [!info]
> È importante sottolineare come in ogni architettura le interfaccie sono uguali, questo permette a tutti gli host di poter comunicare liberamente

## Protocolli

Un protocollo è un insieme di regole che servono a definire come devono essere le interfaccie, in particolare come i servizzi vengono offerti e utilizzati.

## Architettura ISO/OSI

![[Architettura_ISO_OSI.excalidraw|1000]]

Il trasport service è implementato dai sistemi operativi mentre l'upper layer gestito dalle applicazioni (_user space_)

Dei tre livelli:
- la parte in verde è implementata su tutti i dispositivi
- la parte in giallo ed in rosso sono presenti sugli host finali

### Repeater

![[Repeater.excalidraw]]

Ricevono un segnale codificato, lo decodificano per ricostruire lo stream di bit, lo interpretano e lo ricodificano per reinviarlo.

>[!attention]
> Non sono da confondere con gli amplificatori di segnale.

### Bridge

![[Bridge.excalidraw]]

Ricevono dei frame che anche qui vengono decoficati e interpretati, gli si aggiunge il controllo di errori e la correzione di essi prima del reinvio.

Può essere presente un controllo di flusso che notifica il mittente dell'invio di troppi messaggi.

Un tipo di bridge è lo _switch_, questo è un bridge che effettua dei controlli su dove inviare i frame.

### Router

![[Router.excalidraw]]

Uniscono più reti tra loro e implementano l'instradamento per l'invio dei pacchetti.

Per eseguire l'instradamento viene preso il frame, si toglie l'header, si osserva il contenuto per conoscere l'indirizzo e si ricostruisce un nuovo frame.

### Proxy

![[Proxy.excalidraw]]

Lavorano con datagrammi (comunicazione senza connessioni) e segmenti (comunicazioni con connessioni).

Essi ricevono richieste di connessione e le reindirizzano ad altri, i quali forniranno dei servizi.

Permettono di passare i firewall, controllare il contenuto del traffico ed altro.

Nella manipolazione dei messaggi vengono sostituiti i frame e le intestazioni del Network layer, vengono invece mantenuti i protocolli applicativi e l'intestazione del livello di trasporto.

## Architettura TCP/IP

Lorem Ipsum