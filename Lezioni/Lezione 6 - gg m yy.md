## Livello di trasporto

Il livello di trasporto fornisce ai processi applicativi un servizio affidabile e indirizzato, trasformando le funzionalità della rete sottostante in comunicazioni end-to-end. Gestisce pacchettizzazione, indirizzamento dei processi tramite porte, controllo della connessione, affidabilità e controllo di flusso. Alcune funzioni, come QoS e controllo della congestione, richiedono cooperazione tra più livelli.

### Indirizzamento end-to-end in TCP/IP

Ogni processo è identificato da una porta (0–65535) associata all’indirizzo IP, formando un socket <IP, porta>.

Una comunicazione è identificata dalla tupla <IPA, portA, IPB, portB, protocollo>. 

Server e client sono processi: il server ascolta su porte note, mentre il client usa porte effimere.

### Tipi di comunicazione

I tipi principali di comunicazione sono:  
- orientata alla connessione (TCP), che richiede una connessione e garantisce affidabilità;  
- senza connessione (UDP), più rapida ma inaffidabile;  
- varianti meno diffuse come connectionless affidabile;  
- modelli request/response implementati nei protocolli applicativi moderni.

## UDP

UDP estende il servizio di consegna di IP fornendo comunicazione processo-processo tramite porte, con un semplice demultiplexing e senza garanzie aggiuntive rispetto al livello di rete. Il datagramma ha un header minimale; il checksum, se usato, viene calcolato su header UDP, dati e un’intestazione pseudo-IP non trasmessa, che serve solo per l’identificazione della comunicazione.

### API per i servizi UDP

UDP utilizza buffer di ingresso e uscita legati alle socket. La recvmsg() è bloccante e preleva datagrammi dalla coda della porta; la sendmsg() è non bloccante. Le code sono FIFO e possono essere limitate: se piene o se la porta non è associata a un processo, i datagrammi vengono scartati.


### Demultiplexer semplice

Il protocollo non offre controllo di flusso, affidabilità, numerazione o notifica di errori. I datagrammi sono indipendenti e possono arrivare fuori ordine o essere persi. L’incapsulamento è diretto in IP.

UDP è orientato alle transazioni, semplice e stateless, adatto a protocolli di richiesta-risposta leggeri (DNS, NTP), al bootstrap di rete (DHCP, TFTP), a molte connessioni simultanee e a comunicazioni in tempo reale o multimediali (VoIP, giochi online, IPTV), nonché a broadcast, multicast e a protocolli implementati in spazio utente come QUIC.

## TCP

TCP fornisce un flusso di byte affidabile, ordinato e senza duplicati, a differenza di UDP. È orientato alla connessione: prima stabilisce un canale bidirezionale tra due processi, poi trasferisce i dati, infine chiude la connessione. Gestisce automaticamente perdita dei segmenti, ritrasmissioni, riordinamento, controllo di flusso e controllo della congestione.
### Controllo del flusso vs controllo della congestione

**Controllo di flusso:** impedisce al mittente di saturare il buffer del destinatario tramite la finestra scorrevole.  
**Controllo della congestione:** evita di sovraccaricare la rete, adattandosi alla capacità dei router intermedi.

### Problemi end-to-end

TCP usa buffer di invio e ricezione indicizzati da puntatori (LastByteWritten, LastByteSent, LastByteAcked, ecc.) e pubblicizza al mittente quanto spazio è disponibile tramite la _AdvertisedWindow_. Il mittente può inviare dati solo entro la _EffectiveWindow_. Se la finestra scende a zero, il mittente attende o invia “probe” da 1 byte dopo un timeout crescente.

### Segmento TCP

Ogni pacchetto TCP è un _segmento_: contiene header (con porte, numero di sequenza, acknowledgment, flag SYN/FIN/ACK/URG/PUSH/RESET, dimensione finestra, checksum) e payload. TCP è byte-oriented: non preserva i confini dei messaggi.

### Diagramma di stato di TCP

**Handshake a tre vie:**
1. client → server: SYN
2. server → client: SYN+ACK
3. client → server: ACK  
    Al termine entrambi sono in ESTABLISHED. Sono possibili aperture simultanee.

**Trasferimento dati:** i segmenti trasportano dati in una direzione e ACK nell’altra (piggyback). I numeri di sequenza identificano il flusso dei byte. I dati urgenti usano il flag URG e l’urgent pointer.

**Chiusura:** si usa FIN e ACK in una sequenza a quattro segmenti. L’initiator entra in TIME-WAIT per un periodo pari al Maximum Segment Lifetime (MSL), per evitare confusione con vecchi segmenti e problemi di “wraparound” dei numeri di sequenza.

### Mantenere la pipe piena

**Finestra scorrevole:** garantisce affidabilità, ordine e controllo di flusso. La finestra del destinatario determina quanto può essere inviato. TCP supporta scaling della finestra fino a 1 GB per linee ad alta latenza × banda.

### Attivazione della trasmissione

**Invio dei segmenti:** avviene quando  
– si raggiunge la MSS,  
– l’applicazione richiede un push,  
– scade un timer.

### Sindrome della finestra stupida (Silly Window Syndrome)

**Silly Window Syndrome:** inefficienza dovuta all’invio di segmenti troppo piccoli quando la finestra si apre di pochissimo. Richiede meccanismi addizionali per evitare l’invio di pacchetti minuscoli.

### Algoritmo di Nagle

Riduce l’invio di piccoli segmenti. Quando un’applicazione genera molti pacchetti piccoli, TCP invia il primo subito e poi trattiene i successivi finché non arriva un ACK o finché non può inviare un segmento pieno (MSS). Serve a evitare la SWS lato mittente.

### Ritrasmissione adattiva e calcolo del timeout

TCP stima dinamicamente il _Round-Trip Time_ (RTT). Mantiene una media pesata (SRTT) e una stima della variabilità (RTTVAR). Il timeout di ritrasmissione (RTO) viene calcolato come:  
RTO = SRTT + 4 × RTTVAR.  
Il timer cresce (backoff esponenziale) quando una ritrasmissione fallisce, adattandosi alla congestione e alle variazioni della rete.

### Prestazioni del TCP

Le prestazioni dipendono da finestra di congestione, finestra del ricevitore, MSS e RTT. La capacità massima è legata al prodotto banda×RTT: con latenze elevate servono finestre grandi per saturare la rete. Perdite frequenti o timeout riducono la finestra di congestione e quindi il throughput. L’uso di ACK cumulativi, segmentazione corretta e controllo della congestione permette a TCP di adattarsi alle condizioni di rete e mantenere una trasmissione efficiente.

## Problemi di allocazione delle risorse

L’allocazione delle risorse nelle reti a commutazione di pacchetto affronta problemi di congestione: una sorgente può avere banda sufficiente sul primo collegamento, ma incontrare colli di bottiglia più avanti.

Nelle reti senza connessione, i router trattano i pacchetti come datagrammi indipendenti, ma in pratica i pacchetti tra due host seguono spesso gli stessi percorsi e formano flussi. I router possono mantenere per ciascun flusso uno “stato soft”, dedotto automaticamente, utile per prendere decisioni migliori sull’allocazione delle risorse senza necessità di una vera segnalazione.

Due approcci di progettazione:  
- Router-centric: i router decidono inoltro, scarti e inviano feedback agli host.  
- Host-centric: gli host regolano il ritmo di invio osservando il comportamento della rete.

Due modelli di allocazione:  
- Basato su prenotazione: un flusso richiede capacità; i router la riservano se possibile.  
- Basato su feedback: gli host inviano subito e regolano la velocità in base al feedback esplicito o implicito.

### Criterio di valutazione con allocazione efficace delle risorse

L’efficacia della gestione delle risorse si valuta tramite throughput e ritardo, in equilibrio tra loro. Con un modello M/M/1 si ottengono espressioni per ritardo totale, utilizzo, lunghezza media della coda e attesa media. Un indicatore sintetico è la “potenza” (throughput/ritardo), che cresce quasi linearmente a basso carico, raggiunge un massimo e poi peggiora con l’aumento della congestione.

### Criteri di valutazione con allocazione equa delle risorse

Per la _fairness_, quando più flussi condividono collegamenti, una ripartizione uguale di banda non è necessariamente equa se i percorsi hanno lunghezze diverse. L’indice di equità di Jain misura quanto la ripartizione sia bilanciata (1 = perfetta equità).

### Disciplina di accodamento

Code e discipline di accodamento:  
- FIFO con tail drop: i pacchetti vengono serviti in ordine di arrivo; se il buffer è pieno, i nuovi pacchetti in arrivo vengono scartati.  
- Priority queuing: più code FIFO con priorità diverse; prima si servono i pacchetti di priorità più alta.  
- Fair Queuing (FQ): una coda per ogni flusso, servite in modo che la banda sia ripartita equamente. Poiché i pacchetti hanno lunghezze diverse, si usa un’approximation del round-robin bit-per-bit calcolando un timestamp di completamento 𝐹ᵢ = max(𝐹ᵢ₋₁, 𝐴ᵢ) + 𝑃ᵢ. Il pacchetto con il timestamp più basso viene trasmesso per primo, ottenendo così un’equa ripartizione della capacità del collegamento.

## Controllo della congestione di TCP

Il controllo della congestione TCP nasce alla fine degli anni ’80 per evitare il _congestion collapse_, causato dall’invio eccessivo di pacchetti rispetto alla capacità dei router. TCP introduce quindi meccanismi per adattare dinamicamente la quantità di dati in transito.

**Principi generali**

- Ogni sorgente deve stimare la capacità disponibile della rete.
- Viene introdotta la _congestion window_ (cwnd), variabile mantenuta dal mittente che limita il numero di byte non ancora confermati.
- La finestra effettiva di trasmissione è il minimo tra cwnd e la _Advertised Window_ del ricevente.
- TCP non riceve informazioni esplicite dai router, quindi deduce la congestione da segnali impliciti, soprattutto _perdita di pacchetti_ e _timeout_.

### Finestra di congestione (congestion window)

**AIMD (Additive Increase / Multiplicative Decrease)**

- In assenza di congestione: cwnd aumenta linearmente di 1 MSS per RTT.
- In caso di congestione (timeout): cwnd viene dimezzata (b = 0.5).
- Il valore non può scendere sotto 1 MSS.
- Il comportamento produce un tipico andamento “a dente di sega”.

**Slow Start**

- Usato quando la connessione parte da zero o dopo un timeout.
- cwnd inizia da 1 MSS e raddoppia ogni RTT grazie agli ACK.
- Crescita esponenziale fino a raggiungere una soglia, _ssthresh_, poi si torna ad AIMD lineare.
- ssthresh è pari a metà della cwnd nel momento in cui è stata rilevata la congestione.

**Fast Retransmit (Tahoe, 1988)**

- Ritrasmette un pacchetto senza attendere il timeout quando il mittente riceve 3 ACK duplicati.
- Imposta ssthresh = cwnd/2.
- Riporta cwnd a 1 MSS e riparte in slow start.

**Fast Recovery (Reno, 1990)**

- Dopo 3 ACK duplicati: ritrasmissione veloce, ssthresh = cwnd/2.
- cwnd non torna a 1 MSS ma diventa ssthresh + 3 MSS.
- Si evita la fase di slow start e si prosegue direttamente con l’aumento lineare.
- Se avviene un timeout, il comportamento torna identico a Tahoe: cwnd = 1 e slow start.

**Tahoe vs Reno**

- _Tahoe_: dopo la perdita riparte sempre da 1 MSS con slow start.
- _Reno_: dopo fast retransmit evita lo slow start e riparte da metà cwnd + 3 MSS; solo un timeout lo riporta a 1 MSS.
- Entrambi dimezzano cwnd quando rilevano congestione.

**Algoritmi moderni**

- CUBIC (default Linux): ottimizzato per reti ad alta capacità.
- Compound TCP (Windows).
- PRR: miglior recupero da perdita, utile in reti wireless.
- BBR (Google): non usa la perdita come segnale, ma modelli di banda e RTT.

In conclusione, TCP alterna crescita della finestra (esponenziale o lineare) e riduzione drastica in caso di congestione, usando perdita e timeout come indicatori, con varianti storiche (Tahoe e Reno) e moderne che migliorano efficienza ed equità.

## Meccanismi di evitamento della congestione

TCP non previene la congestione in anticipo, ma aumenta progressivamente il proprio invio finché non rileva perdite, poi riduce la finestra. L’evitamento della congestione cerca invece di anticipare il problema e rallentare prima che i pacchetti vengano scartati.

### Rilevamento precoce casuale (RED)

**RED (Random Early Detection)**

- Ogni router monitora la lunghezza media della propria coda tramite una media mobile.
- Due soglie:  
    - sotto **MinThreshold**: nessuno scarto  
    - tra **MinThreshold** e **MaxThreshold**: scarto probabilistico  
    - oltre **MaxThreshold**: scarto certo
- Il router scarta alcuni pacchetti prima che la coda sia piena, inducendo le sorgenti TCP a ridurre la loro finestra tramite timeout o ACK duplicati.
- La probabilità di scarto cresce linearmente con la lunghezza media della coda ed è regolata dal parametro **MaxP**.
- L’obiettivo è evitare che la coda si saturi completamente e ridurre gli scarti massivi.


### Evitare le congestioni basate sulle sorgenti

**Evitamento della congestione basato sulla sorgente**

- L’host osserva variazioni dell’RTT come segnale precoce di accumulo nelle code dei router.
- Se l’RTT cresce oltre un intervallo considerato normale, la sorgente riduce la propria finestra di congestione (ad esempio moltiplicandola per 0,875).
- Varianti confrontano cambiamenti di RTT e finestra per capire se l’aumento di carico sta degradando le prestazioni.
- La finestra oscilla attorno al punto ottimale, adattandosi continuamente.

In sintesi, RED agisce sui router scartando pacchetti in anticipo, mentre gli algoritmi basati sulla sorgente regolano la finestra osservando i tempi di rete. Entrambi mirano ad anticipare la congestione invece di reagire solo dopo la perdita.