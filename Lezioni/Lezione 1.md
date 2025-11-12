## Architetture di rete e protocolli
### Interfacce

Un'interfaccia è l'insieme di funzionalità che viene offerto da ogni livello di una architettura.

Le interfaccie sono divise in due tipi:
- Interfaccie di servizio: API che vengono offerte ai livelli superiori
- Interfaccie peer: che servono a comunicare tra i servizzi allo stesso livello


> [!info]
> È importante sottolineare come in ogni architettura le interfaccie sono uguali, questo permette a tutti gli host di poter comunicare liberamente

### Protocolli

Un protocollo è un insieme di regole che servono a definire come devono essere le interfacce, in particolare come i servizi vengono offerti e utilizzati.

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

#### Quinto/sesto/settimo livello

Questo livello non è gestito dal sistema operativo, usa invece dei socket per comunicare tra le applicazioni.

Qui vengono gestiti i flussi, l'autenticazione utenti, la cifratura, la conversione dati, la compressione, le applicazioni. Tutto ciò viene dato in incarico al programmatore.

## Performance

La _performance_ di una rete viene misurata attraverso:
- Il _throughput_: il numero di bit che vengono inviati ogni secondo.
- La _latency_: il tempo che passa dall'invio dei dati al loro effettivo arrivo.

> [!warning]
> Più dati vengono mandati più aumenta il ritardo, specialmente in reti a commutazione di pacchetto[^1].

### Definizione di bandwidth

Le onde sinusoidali sono composte da:
- Ampiezza ($A$), viene solitamente espressa in Volt e influenza quando l'onda è alta;
- Frequenza ($f$), espressa in Hertz (1/s) rappresenta quanto velocemente l'onda oscilla / si ripete;
- Fase ($\varphi$) quanto l'onda è spostata verso destra/sinistra.

La frequenza può essere scritta anche come l'inversa del tempo, $T = \frac1f$.

Una sinusoide è quindi definita come:
$$
x(t) = A \sin (2 \pi\ f\ t + \phi)
$$
![[Sinusoide.excalidraw|1000]]

Secondo la definizione di Fourier ogni segnale $x(t)$ periodico è definito dalla sommatorie di infinite sinusoidi:
$$
x(t) = \sum_{i=1}^{\infty} x_i(t) = \sum_{i=1}^{\infty} A_i \sin (2 \pi\ f\ t + \phi_i)
$$

Inoltre, studiare il segnale secondo le sue componenti ci porta a rappresentare il segnale come una sommatoria di tante sinusoidi, ognuna delle quali delinea un valore sull'ampiezza.

![[Bandwidth.excalidraw|1000]]

La bandwidth è quindi la parte occupata dal segnale nello spazio delle frequenze e viene calcolata tramite la differenza tra le frequenze massime e quelle minime (con ampiezza non nulla).

> [!info]
> L'insieme delle frequenze che compongono un segnale periodico è detto spettro delle frequenze.

### Latenza

La latenza è il ritardo complessivo dei tempi di propagazione, trasmissione ed accomodamento.

In particolare:
- propagazione: velocità del segnale, dipende dal mezzo fisico;
- trasmissione: tempo di trasmissione del messaggio (grandezza messaggio/throughput);
- accodamento: tempo trascorso all'interno di pc, switch e router.

> [!info]
> Si ricorda che se i messaggi contengono poche informazioni allora è importante la propagazione, nel caso opposto, con tante informazioni è importante il throughput.

| VF (%) | Cable               |
| ------ | ------------------- |
| 73-79  | Cat-7 twisted pair  |
| 77     | RG-8/U              |
| 67     | Optical fiber       |
| 65     | RG-58A/u            |
| 65     | Cat-6A twisted pair |
| 64     | Cat-5e twisted pair |
| 58.5   | Cat-3 twisted pair  |

Come si vede i cavi possono essere più "veloci" della fibra, è da notare però che la maggior latenza è aggiunta dal passaggio tra un dispositivo ed il successivo, il cavo richiedendo più amplificatori risulta aggiungere più latenza rispetto la fibra.

Il fattore di velocità è espresso come:
$$
\frac{\textnormal{velocità della luce nel mezzo}}{\textnormal{velocità della luce nel vuoto}}
$$

### Delay vs throughput

È molto difficile ottimizzare sia il throughput che il delay dato che per un throughput elevato si devono mantenere pieni i canali, questo però causa l'allungamento delle code e di conseguenza aumenta il ritardo, invece per abbassare i ritardi le code devono essere vuote/molto corte, implicando così un basso throughput.

Se il mittente vuole mantenere piena una connessione allora si devono inviare $Delay \times Throughput$ bit/s, questo viene chiamato _Bandwidth Delay Product_ (BDP).

Se il ricevitore deve rispondere allora si dovrà aggiungere un altro ritardo per il tempo di trasmissione del messaggio.

Il tempo di andata e ritorno è chiamato _Round Trip Time_ (RTT) ed equivale a $2 \times Delay$.

> [!example]
> Ritardo = 50ms
Throughput = 45 Mbps
$BDP = 10 \times 10^{-3}\ \textnormal{secondi} \times 45 \times 10^6\ \textnormal{bit/secondo} = 2.25 \times 10^6\ \textnormal{bit} = 280\ \textnormal{KB}$

### Jitter

Un problema nelle connessioni è anche la variazione stessa del ritardo, si immagini lo streaming audio, questo problema viene chiamato _Jitter_.

Se il ritardo non fosse costante i dati inizierebbero a rallentare per poi arrivare tutti insieme rischiando di incorrere in un _unbuffering_.

Per immagazzinare i dati si utilizzano i buffer, però, se viene esaurito lo spazio a disposizione la comunicazione si deve fermare per aspettare che si liberi, altrimenti verrebbero persi dei dati.

In una connessione si preferisce quindi che la varianza sia poca in modo che non si debbano avere buffer troppo grandi.

---
[^1]: La commutazione di pacchetto è una tecnica di accesso multiplo a divisione di tempo utilizzata per condividere un canale di comunicazione tra più nodi in modo non deterministico, suddividendo l'informazione da trasferire in pacchetti trasmessi individualmente e in sequenza.
