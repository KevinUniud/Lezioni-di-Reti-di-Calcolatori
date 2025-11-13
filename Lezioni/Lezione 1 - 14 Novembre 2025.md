## Introduzione

**Reti a commutazione di pacchetto**:

I dati vengono suddivisi in piccoli pacchetti, ciascuno dei quali contiene informazioni sull’origine, la destinazione e l’ordine. I pacchetti viaggiano indipendentemente attraverso la rete e possono seguire percorsi diversi; vengono poi ricomposti all'arrivo. È il metodo usato da Internet.

Vantaggi: uso efficiente della banda, flessibilità, tolleranza ai guasti. 
Svantaggi: possibili ritardi e variazione nel tempo di consegna (jitter).

**Reti a commutazione di circuito**:

Prima della comunicazione viene stabilito un collegamento fisso (un “circuito”) tra mittente e destinatario, che rimane riservato per tutta la durata della trasmissione. È il metodo usato nelle reti telefoniche tradizionali. 

Vantaggi: comunicazione continua e costante. 
Svantaggi: inefficienza (la linea resta occupata anche durante le pause).

**TDM (Time Division Multiplexing)**:

Tecnica di multiplexing che suddivide il tempo in intervalli (slot), assegnandone uno a ciascun segnale. Ogni utente trasmette in modo sequenziale nel proprio slot temporale. È tipica delle reti digitali e delle comunicazioni sincrone.

**FDM (Frequency Division Multiplexing)**:

Tecnica che divide la banda di frequenze disponibile in più canali distinti, ciascuno assegnato a un segnale. Tutti i segnali viaggiano simultaneamente ma su frequenze diverse. È usata, ad esempio, nelle trasmissioni radio e TV analogiche.

**STDM (Statistical Time Division Multiplexing)**:

Variante del TDM in cui gli slot temporali non sono fissi ma assegnati dinamicamente agli utenti che hanno dati da trasmettere. Migliora l’efficienza rispetto al TDM classico, riducendo i tempi morti.

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
> Più dati vengono mandati più aumenta il ritardo, specialmente in reti a commutazione di pacchetto.

### Definizione di bandwidth

Le onde sinusoidali sono composte da:
- Ampiezza ($A$), viene solitamente espressa in Volt e influenza quando l'onda è alta;
- Frequenza ($f$), espressa in Hertz (1/s), rappresenta quanto velocemente l'onda oscilla / si ripete;
- Fase ($\varphi$), quanto l'onda è spostata verso destra/sinistra.

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

## Link e frequenze

Un _link_ è un qualsiasi mezzo che permette di far comunicare due dispositivi. 

Ogni mezzo di comunicazione ha una banda in cui può trasmettere il segnale.

| Mezzo trasmissivo          | Bandwidth         |
| -------------------------- | ----------------- |
| Doppino intrecciato (rame) | 0 - 600 MHz       |
| Cavo coassiale             | 0 - 500 MHz       |
| Fibra ottica               | 186 - 375 THz     |
| Onde radio                 | 3 KHz - 300 GHz   |
| Infrarosso                 | 300 GHz - 400 THz |

La distanza tra cui si ripete l'onda del segnale è chiamata _wavelenght_, espressa in metri e calcolata come:
$$
\frac{\textnormal{velocità della luce sul mezzo trasmissivo}}{\textnormal{frequenza}}
$$

> [!example]
> Un cavo di rame ha frequenze tra i 300Hz e 3300Hz
> La wavelenght per 300Hz su un cavo di rame è:
> $$\frac{\textnormal{velocità della luce sul rame}}{\textnormal{frequenza}} = \frac{2 \times 10^8}{300}=667\times10^3 \textnormal{m}$$

## Codifica e simboli

L'inserimento di dati in un segnale si chiama _encoding_.

Un simbolo è l'unità elementare di informazione trasmessa in un canale, rappresentata da un determinato stato del segnale (ampiezza, fase o frequenza), inoltre, può essere composto da più bit.

### Teorema di Nyquist-Shannon

Il massimo numero di simboli al secondo è definito come _Baud rate_ e viene espresso come simboli al secondo ($R$).

> [!important] Teorema del campionamento di **Nyquist-Shannon**
> Un canale con una certa larghezza di banda ($B$), indifferentemente dalle frequenze a cui opera, può trasmettere al massimo il doppio di simboli rispetto alla larghezza di banda, $R = 2B$.

Dato un segnale con una certa frequenza massima, per campionarlo, trasmetterlo e ricostruirlo correttamente, è necessario prelevare campioni a intervalli regolari con una frequenza almeno doppia rispetto alla massima frequenza del segnale. In questo modo si ottiene una serie di campioni sufficienti a ricostruire completamente l’onda originale.

La presenza di almeno due punti per periodo permette di interpolare matematicamente l'onda originale.

In un segnale ogni simbolo può portare $\lceil\log_2 (\Sigma)\rceil$ bit.

Per codificare un segnale si prende il range di tensione tra $-V$ e $+V$ e lo si divide nel numero di livelli desiderato, successivamente si assegna ad ogni livello una tupla di bit, il primo corrisponderà a tutti i bit a 0, il successivo a tutti i bit a 0 tranne l'ultimo che sarà ad 1 e così via fino ad ottenere tutte le possibili combinazioni.

Per trasmettere una tupla di bit al codificatore bastarà impostare la tensione al livello stabilito.

Il _noise_ ($N$) è un segnale casuale che si aggiunge al segnale ($S$), se il rumore è troppo forte un simbolo può essere modificato e quindi interpretato in modo errato dal decodificatore.

Il _signal-to-noise_ è il rapporto tra la potenza media del segnale $S$ e la potenza media del rumore $N$, spesso viene espresso in decible ($dB$), qui prende il nome di SNR:
$$
SNR = 10 \times \log_{10} \left(\frac{S}
{N}\right)
$$

L'SNR esprime quindi il "livello di rumore" e determina la "qualità" del canale.

### Teorema di Shannon-Hartley

Il teorema di Shannon-Hartley fornisce il limite superiore della capacità di un collegamento in termini di _bps_ in funzione dell'SNR del collegamento e della banda disponibile:
$$
C = B \times \log_2 \left(1 + \frac{S}{N}\right)
$$

Se si volesse avere una capacità maggiore è necessario migliorare la larghezza di banda oppure la qualità della line.

> [!example]
> Una linea telefonica con B = 3000 Hz
> $30$ dB di rumore $\rightarrow \frac{S}{N} = 10^{\frac{30}{10}} = 1000$
> Segue $C = 3000 \times \log_2(1001) = 30\ \textnormal{kbps}$

## Tipi di codifiche

### Codifica NRZ

La codifica NRZ (_Non Return to Zero_) passa da voltaggi negativi a positivi senza fermarsi sullo 0.

**Problema Baseline Wander**:

È un fenomeno in cui il livello medio del segnale tende a spostarsi nel tempo, rendendo difficile distinguere correttamente i livelli dello 0 e 1. Questo accade perché il segnale non torna mai a zero tra un bit e l’altro.

Se si trasmette una lunga sequenza di bit uguali il livello del segnale resta costante per un periodo prolungato, questo provoca una perdita del riferimento e crea difficoltà al ricevitore nel riconoscere correttamente le transizioni dato che il livello medio del segnale “deriva” lentamente nel tempo.

**Problema Clock Recovery**:

Qui il ricevitore ha difficoltà a ricostruire il segnale di temporizzazione (clock) del trasmettitore, necessario per campionare correttamente i bit ricevuti. Questo avviene poiché il segnale trasmesso non contiene sufficienti transizioni di livello da cui il ricevitore possa ricavare il ritmo dei bit.

Se si trasmette una lunga sequenza di bit uguali, il segnale rimane stabile per un periodo prolungato, senza variazioni che indichino l’inizio o la fine di un bit, ne consegue una perdita di sincronizzazione che crea difficoltà nel determinare il momento esatto in cui leggere ciascun bit, aumentando così la probabilità di errore nella decodifica dei dati.

### Codifica NRZI

la NRZI (_Non Return to Zero Inverted_) è una variante della codifica precedente: se il bit da trasmettere è 0 il valore di tensione rimane uguale, se è 1 si inverte la tensione.

Questa codifica permette, in presenza di 1 consecutivi, di ottenere una buona sincronizzazione del ricevitore, rimane comunque un problema se si presentano tanti 0 consecutivi.

### Codifica Manchester

Si ottiene sovrapponendo (tramite `EX-OR`) una frequenza di clock alla codifica NRZ, in particolare il clock è una sequenza doppia di bit regolari e si prende la sua onda quadra quando si ha 0 e l'inversa quando si ha 1.

![[Manchester.excalidraw|1000]]

### Codifica MLT-3

La sigla significa _Multi-Level Transmit_ ed è simile alla NRZI, la differenza principale è che utilizza una terna di bit: -1, 0, 1, in cui il terzo livello introdotto serve a distinguere i voltaggi.

Se si presenta uno 0 si resta al livello attuale altrimenti si aumenta il livello del segnale, ogni volta che si arriva ad un estremo si inverte la crescita.

Anche in questo caso in presenza di molti 0 consecutivi si hanno problemi di sincronizzazione.

![[MLT-3excalidraw|500]]

### Codifica 4B/5B

Finora il problema sono state le lunghe sequenze di 0, si è quindi ideata una pre-codifica che permette di modificare le informazioni in modo da rompere queste sequenze e poter quindi essere codificate senza problemi.

Si suddividono le sequenze di bit in gruppi da 4, queste vengono poi trasformate in sequenze da 5 bit tramite una tabella di conversione, si ottiene così un efficienza dell'80%. Ne segue che ogni codice da 5 bit non presenterà più di uno 0 iniziale, né più di due 0 finali, questo permette di non avere mai più di tre 0 consecutivi.

![[4B5B.excalidraw]]

Le possibili combinazioni da 5 bit sono $2^5 = 32$, 16 di esse sono utilizzati per la sequenza di dati e delle restanti combinazioni 8 sono usate come codici di controllo.

| Control Sequence    | Encoded Sequence |
| ------------------- | :--------------: |
| Q (Quiet)           |      00000       |
| I (Idle)            |      11111       |
| H (Halt)            |      00100       |
| J (Start delimeter) |      11000       |
| K (Start delimeter) |      10001       |
| T (End delimeter)   |      01101       |
| S (Set)             |      11001       |
| R (Reset)           |      00111       |

> [!info]
> Questa codifica risulta molto utile per NRZ, NRZI e MLT-3, le quali hanno ottime performance ma necessitano dell'assenza di molti 0 consecutivi poiché non cambiano livello di tensione.
