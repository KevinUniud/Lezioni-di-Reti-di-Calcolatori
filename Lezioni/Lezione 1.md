## Architetture di rete e protocolli
### Interfacce

Un'interfaccia è l'insieme di funzionalità che viene offerto da ogni livello di una architettura.

Le interfaccie sono divise in due tipi:
- Interfaccie di servizio: API che vengono offerte ai livelli superiori
- Interfaccie peer: che servono a comunicare tra i servizzi allo stesso livello


> [!info]
> È importante sottolineare come in ogni architettura le interfaccie sono uguali, questo permette a tutti gli host di poter comunicare liberamente

### Protocolli

Un protocollo è un insieme di regole che servono a definire come devono essere le interfaccie, in particolare come i servizzi vengono offerti e utilizzati.

### Architettura ISO/OSI

![[Architettura_ISO_OSI.excalidraw|1000]]

Il trasport service è implementato dai sistemi operativi mentre l'upper layer gestito dalle applicazioni (_user space_)

Dei tre livelli:
- la parte in verde è implementata su tutti i dispositivi
- la parte in giallo ed in rosso sono presenti sugli host finali

#### Repeater

![[Repeater.excalidraw]]

Ricevono un segnale codificato, lo decodificano per ricostruire lo stream di bit, lo interpretano e lo ricodificano per reinviarlo.

>[!attention]
> Non sono da confondere con gli amplificatori di segnale.

#### Bridge

![[Bridge.excalidraw]]

Ricevono dei frame che anche qui vengono decoficati e interpretati, gli si aggiunge il controllo di errori e la correzione di essi prima del reinvio.

Può essere presente un controllo di flusso che notifica il mittente dell'invio di troppi messaggi.

Un tipo di bridge è lo _switch_, questo è un bridge che effettua dei controlli su dove inviare i frame.

#### Router

![[Router.excalidraw]]

Uniscono più reti tra loro e implementano l'instradamento per l'invio dei pacchetti.

Per eseguire l'instradamento viene preso il frame, si toglie l'header, si osserva il contenuto per conoscere l'indirizzo e si ricostruisce un nuovo frame.

#### Proxy

![[Proxy.excalidraw]]

Lavorano con datagrammi (comunicazione senza connessioni) e segmenti (comunicazioni con connessioni).

Essi ricevono richieste di connessione e le reindirizzano ad altri, i quali forniranno dei servizi.

Permettono di passare i firewall, controllare il contenuto del traffico ed altro.

Nella manipolazione dei messaggi vengono sostituiti i frame e le intestazioni del Network layer, vengono invece mantenuti i protocolli applicativi e l'intestazione del livello di trasporto.

### Architettura TCP/IP

![[TCP-IP.excalidraw|1000]]

#### Primo/secondo livello

Il Network layer rappresenta i primi due strati del modello ISO/OSI e si occupa infatti delle stesse cose, gestisce quindi gli aspetti di collegamento, cioè come accedere al mezzo, gestire gli errori, i frame, la codifica.

>[!info]
> Non si ha un singolo standard come riferimento, ma molteplici e ognuno dipendente dalla tecnologia implementata (bluetooth, wi-fi).


#### Terzo livello

Internet layer si occupa di gestire l'instradamento dei pacchetti, è inoltre da notare che non si assume che gli strati siano affidabili.
Fare assunzioni più deboli permette di utilizzare molte più tecnologie.

I protocolli utilizzati in questo livello sono ARP, ICMP, IGMP, essi non trasportano dati ma servono a gestire la rete.

>[!info]
> È importante notare che se si volesse implementare un nuovo protocollo di rete basta che si interfacci con l'Internet protocol

#### Quarto livello

Nel TCP/IP si ha l'application layer che implementa TCP e UDP.

TCP effettua dei controlli sul flusso e va a rimediare ai problemi di affidabilità e comunicazione, questo dato che i pacchetti possono non arrivare, arrivare in disordine, errati.

UDP non effettua nessun controllo, è molto più veloce, semplice ed efficiente.

Principalmente TCP serve per il trasporto affidabile dei dati, come per i download, mentre UDP per la velocità, cioè streaming audio/video in cui se si perdono dei pacchetti si riesci comunque a ottenere un risultato accettabile.

#### Quinto livello

Questo livello non è gestito dal sistema operativo, usa invece dei socket per comunicare tra le applicazioni.

Qui vengono gestiti i flussi, l'autenticazione utenti, la cifratura, la conversione dati, la compressione, le applicazioni. Tutto ciò viene dato in incarico al programmatore.
