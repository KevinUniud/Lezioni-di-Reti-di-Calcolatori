## Frame

I frame sono unità di dati che vengono scambiate tra due interfacce collegate allo stesso mezzo tramite link.

Quando si è in un canale condiviso da più host è necessario introdurre delle informazioni aggiuntive su come trovare un ricevente specifico, questo viene fatto nell'header.

### Protocollo orientato ai byte

Il protocollo è basato su dei byte "sentinella", ovvero caratteri speciali che indicano l'inizio e fine dei frame. Questi caratteri sono i primi dell'alfabeto ASCII e vengono utilizzati principalmente quando i contenuti dei pacchetti sono di lunghezza variabile o contenenti caratteri opzionali.

**BISYNC (Binary Synchronous Communication)**:

Protocollo basato su sentinelle (Sentinel-based)

![[BISYNC.excalidraw|1000]]

- SYB (Synchronize, ASCII 22): Inizio dei frame e viene spedito 2 volte;
- SOH (Start of header, AScii 1): Inizio dell'intestazione;
- STX (Start of text, ASCII 2): Inizio del testo;
- ETX (End of text, ASCII 3): Fine del testo;
- DLE (Data Link Escape, ASCII 16): Inibisce ETX nel caso compaia nel body del text;
- CRC (Cyclic Redundant Check): Codici di controllo dei dati (trailer).

Quando i bit vengono convertiti in byte si inizia l'interpretazione cercando i due SYN (da cui il nome del protocollo), si controlla poi la presenza del SOH e si leggono i byte successivi fino all'STX, si estrae il body situato tra STX e ETX, infine si termina la lettura una volta letto il CRC.

Questa soluzione introduce un po' di overhaed, per ovviare a ciò si cerca di inviare frame di grandi dimensioni.

**DDCMP (Digital Data Communication Protocol)**:

Protocollo basato sul conteggio dei byte (Byte-count based)

![[DDCMP.excalidraw|1000]]

Si usano campi a lunghezza fissa, non servono quindi sentinelle.

Anche qui il preambolo inizia con due SYN seguiti da un campo classe ed il count dei byte contenuti nel corpo del frame,  l'header ed il CRC. Se count risulta corrotto si ottiene un errore.

Per identificare l'inizio e fine di intestazione e body si hanno dei count interni ai campi che servono ad identificare la loro lunghezza totale, si evita così l'uso delle sentinelle che creavano un overhead.

Dato che i frame sono di lunghezza fissa si può evitare di aggiungere i count in quanto il protocollo sa già la lunghezza che ogni campo deve avere.

Questa soluzione, inoltre, non risente del problema dei caratteri speciali preceduti da un DLE in quanto non vengono usate le sentinelle come terminatori dei campi.

### Protocollo orientato ai bit

**HDLC (High Level Data Link Control)**:

![[HDLC.excalidraw|1000]]

In questo caso ci sono delle sequenze di bit speciali che identificano l'inizio e la fine dei frame, ovvero "011111110". La parte centrale è composta unicamente da header, body e CRC.

Il protocollo prevede una tecnica chiama _bit stuffing_, consiste nel rompere le sequenze con più di cinque bit a 1 con degli 0. Il ricevitore sa che se dopo cinque bit a 1 trova uno 0 allora il ricevitore deve scartarlo a prescindere, nel caso ne trovasse un sesto se venisse seguito da uno 0 allora sa che è il terminatore, se fosse un 1 allora viene interpretato come errore e viene scartato tutto il frame.

### Protocollo PPP

**PPP (point-to-point protocol)**:

![[PPP.excalidraw|1000]]

Il PPP è un protocollo utilizzato per fare il framing di dati su linee seriali sulle connessioni Internet e utilizza il HDLC e l'approccio a sentinelle per la trasmissione dei dati.

Utilizza dei caratteri _flag_ per delimitare la lunghezza del frame ed in mezzo si trovano i campi indirizzo e controllo che hanno una lunghezza standard, il protocollo, il payload che normalmente ha 1500 byte e alla fine, il checksum.

Il payload è un campo negoziabile, ovvero gli host si mettono d'accordo sulla lunghezza nella trasmissione e ricezione.

## Errori

Al livello datalink, una volta che si sono riconosciuti i frame, si deve controllare che non ci siano degli errori.

Il compito di questo livello è quello di filtrare i dati in modo che ai livelli superiori arrivino solo messaggi corretti ed utilizzabili.

Può scegliere di:
- Scartare il messaggio senza avvisare i livelli superiori, sceglieranno loro poi cosa fare;
- Notifica il mittente di rimandare il pacchetto;
- Cerca di correggere il problema, _forward error correction_.

### Calcolo delle probabilità

Per conoscere quanti bit errati si hanno su una connessione si usano tecniche di tipo probabilistico.

Dati $A$ e $B$, la probabilità che avvenga uno dei due è $P[A \cup B]$, ottenibile anche togliendo dalla probabilità certa $1$ la probabilità che non avvenga nessuno dei due.

> [!example]
> Pacchetto = 10 kb
> $P_{\textnormal{bit errato}} = 10^{-7}$
> La probabilità che l'intero pacchetto arrivi corretto è: $1-P_{\textnormal{almeno un errore in 10kb}}$
> La probabilità di avere almeno un bit errato è: $1-P[\textnormal{no errori}] = 1 - (1-P_{\textnormal{bit errato}})^n = 1 - (1-10^{-7})^{10000} = 10^4 \times P_{\textnormal{bit errato}}$

> [!example]
> Con due errori si ha:
> $$P[\textnormal{due errori in 10kb}] = (1-P_{\textnormal{bit errato}})^{n-k} \times (P_{\textnormal{bit errato}})^k \times C(n,k)$$
> Dove $C$ è il binomiale.

### Rilevamento degli errori

Il trailing è il codice di controllo ridondante che non aggiunge alcuna informazione al testo ma fornisce un algoritmo derivato da esso.

**Parità uni-dimensionale**:

Il _one_dimensional parity_ controlla un bit extra di parità che viene aggiunto ad un codice di $n$ bit in modo da rendere il totale pari.

È da notare che se avvengono un numero pari di errori allora non verranno rilevati.

> [!info]
> L'algoritmo nacque per il codice ASCII in quanto all'inizio questo utilizzava solamente 7 bit con il settimo riservato per il parity.

**Parità bi-dimensionale**:

Il _two-dimensional parity_ prende l'algoritmo precedente (parità orizzontale) e ci aggiunge un altro bit di controllo per il frame (parità verticale).

L'algoritmo permette di rilevare la maggior parte degli errori in quanto se la singola riga non rileva l'errore allora lo rileverà la colonna. Rimane comunque il problema di errori allineati nella stessa riga e colonna.

Si può inoltre usare l'algoritmo per correggere errori su singoli bit.

**Internet Checksum Algorithm**:

Il _Internet Checksum Algorithm_ viene usato a livello 3/4, infatti è usato per i protocolli IP, UDP, TCP.

I dati vengono visti come sequenze di coppie di byte e vengono sommati tra loro, una volta arrivati in overflow di tronca la somma e si ricomincia, il risultato finale, complementato a uno, è il checksum.

### Cyclic Redundancy Check

Il CRC rappresenta il modo più compatto per rappresentare tanti controlli diversi, in quanto si possono combinare più livelli di parità e di vari generi all'interno dello stesso checksum o sequenza di bit in maniera modulare.

In questo algoritmo si rappresentano i bit con dei coefficienti numerici dei polinomi in cui una struttura di $k$ bit ha un grado massimo di $k-1$.

> [!example]
> Data una stringa 110001, essa viene rappresentata come: $1 \cdot x^5 + 1 \cdot x^4 + 0 \cdot x^3 + 0  \cdot x^2 +  0 \cdot x^1 + 1 =  x^5 + x^4 + 1$.

**Trasmissione dei messaggi tramite polinomi**:

L'algoritmo aggiunge $r$ zeri all'estremità di ordine inferiore del frame in modo che contenga $m + r$ bit e qua dà il polinomio $x^r M(x)$, ovvero $M(x)$ shiftato di $r$ bit; si divide $x^r M(x)$ per $C(x)$ utilizzando la divisione modulo 2; infine si sottrae il resto $R(x)$ dalla stringa corrispondente a $x^r M(x)$ usando la sottrazione modulo 2. Il risultato $P(x) = x^r M(x) - R(x) = x^r M(x) +R(x)$ è il frame con checksum da trasmettere.

Il receiver riceve $P'(x) = P(x) + E(x)$ e calcola $R'(x) = \textnormal{resto}\left[\frac{P'(x)}{C(x)}\right]$. Se $R'(x) = 0$ allora scarta $r$ LSB (_least significant bit_), cioè prende $M(x) = x - r P'(x)$; altrimenti si verifica un errore.

**Alcune proprietà del CRC**:

Se $\frac{E(x)}{C(x)}=0$ allora o $E(x) = 0$ oppure $C(x)$ è fattore di $E(x)$, questo è l'unico caso in cui non ci si accorge degli errori.

Se è presente un solo errore allora $E(x) = x^i$ determina il bit $i$ errato.

Se $C(x)$ contiene più di un termine non si riuscirà mai a dividere $E(x)$, ne segue che tutti gli errori a singolo bit verranno rilevati.

Supponendo di avere due polinomi per gli errori, $C_1 (x) \not = C_2(x)$ che rilevano errori diversi, e si volesse costruire un polinomio che consenta di rilevare entrambi gli errori basta moltiplicare i due polinomi, ovvero $C(x) = C_1(x) \times C_2(x)$. Questo funziona perché il polinomio $P(x)$ risulterebbe divisibile sia per $C_1(x)$ che per $C_2(x)$ dato che entrambi compongono $C(x)$.

> [!warning]
> È importante notare che il CRC non da informazioni su quali bit sono sbagliati, ne può correggerli, serve solo a rilevarne la presenza.

> [!example]
> Polinomio generatore = 10111
> Sequenza di bit = 110101101010
> Si toglie dalla sequenza di bit 1010 in quanto solo i primi bit sono per i dati.
> Si divide 11010110 per 10111 e si nota che il codice è sbagliato dato che il resto non è 0.

## Trasmissione affidabile:

I codici di correzione (_forward error correction_) sono complessi e pesanti per cui vengono utilizzati solo nei casi in cui il pacchetto da ricevere non può essere ritrasmesso, oppure, in presenza di connessioni molto rumorose. 

Le connessioni non affidabili (_unreliable_) lasciano ai livelli superiori il compito di decidere se chiedere un ulteriore invio di un pacchetto o meno (Ethernet, wifi). Al contrario le affidabili (_reliable_) devono occuparsi dei frame scartati (PPP).

Le connessioni affidabili sfruttano due meccanismi: _Acknowledgement_ e _Timeout_.
- Acknowledgement: Questo è un frame (o messaggio per un livello superiore) di controllo che serve ad informare il mittente dell'avvenuta ricezione o della richieste di re-invio del pacchetto.
- Timeout: Nel caso in cui il mittente sia in attesa di una risposta dal destinatario e intercorre un certo periodo di tempo senza ricezione di risposta esso procede con il re-invio dello stesso pacchetto.

Il meccanismo di acknowledgement e timeout è chiamato _Automatic Repeat reQuest_ (ARQ).

### Protocollo stop-and-wait

In questo protocollo ci si pone in ascolto di una risposta dopo l'invio di un messaggio.

Esistono quattro diversi scenari:
- *a*) Il messaggio ACK viene ricevuto entro il timeout;
- *b*) Il messaggio originale viene perso, scade il timeout e si re-invia il pacchetto;
- *c*) Il messaggio originale viene ricevuto ma l'ACK di perde, si necessita quindi di un re-invio del pacchetto iniziale, questo perché il mittente non può essere notificato dell'avvenuta ricezione;
- *d*) Il timeout scade sempre prima della ricezione dell'ACK.

I casi *c* e *d* sono i più problematici, nel primo il destinatario riceve due volte lo stesso pacchetto e deve capire se l'invio doppio è un fattore voluto dal mittente oppure è perché non è stato ricevuto l'ACK, nel secondo si ha lo stesso problema solo che si aggiunge il dilemma lato mittente sul capire se l'ACK arrivato è del primo o secondo pacchetto inviato.

Per risolvere il problema si aggiunge un'informazione extra nell'header (_metadata_) in modo da numerare il frame e distinguerlo, ad esempio con un flag di un bit. L'ACK di risposta dovrà contenere a sua volta il valore del flag del pacchetto ricevuto a cui fa riferimento.

In caso di timeout il frame viene ritrasmesso con lo stesso numero, il ricevente può quindi determinare che sta vedendo una seconda copia del frame rispetto che la prima di uno nuovo, può quindi ignorarla rispondendo comunque con un ACK.

> [!example]
> Throughtput = 1.5 Mbps
> Delay RTT = 45 ms
> $C \approx 8$ KB
> Il mittente può inviare solo frame da 1 KB per ogni RTT, esso sfrutta funque un ottavo del volume del canale
> Velocità di invio: $\frac{\textnormal{bit per frame}}{\textnormal{tempo per frame}} = \frac{1024 \times 8}{0.045} = 182$ Kpbs
> Più è lungo l'RTT meno si usa il canale.

### Protocollo sliding windows

Un protocollo che sfrutta meglio la capacità del canale è lo _sliding window_, è da notare che è implementato al livello 4 dello stack.

Questo protocollo è una generalizzazione dello stop-and-wait che consente di mandare più messaggi durante lo stesso RTT in modo da riempire il canale. Dato che si hanno più messaggi non basterà più solo un bit per riconoscere i frame e si dovrà aggiungere un numero di frequenza.




**Problemi con questo protocollo**:

## Ethernet - IEEE 802.3

### 10 base 5