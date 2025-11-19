## Frame Ethernet

### Formato del pacchetto

I frame Ethernet sono utilizzati nel _livello data-link_[^1] ed hanno il seguente formato:

![[Pacchetto ethernet.excalidraw|1000]]

Il preambolo è una serie di 7 byte che non porta dati ma serve unicamente ad inizializzare la connessione, ogni byte ha un valore fisso di $55_{\textnormal{base}\ 16} = 01010101_{\textnormal{base}\ 2}$ permette di formare un'onda quadra di sincronizzazione per quando il segnale viene codificato tramite _Manchester_[^2].

Al termine del preambolo c'è un byte, $D5_{\textnormal{base}\ 16} = 11010101_{\textnormal{base}\ 2}$ (_Start of Frame Delimiter_, _SFD_), che serve ad identificare l'inizio del frame.

Questi primi byte si possono considerare un'intestazione del pacchetto livello data-link.

Il frame Ethernet ha una seconda intestazione che viene seguita da payload e CRC, essi hanno dimensione fissa per cui l'algoritmo che li legge sa come riconoscerli.

L'header è composto da 14 byte (indirizzo di destinazione, indirizzo sorgente e tipo), può però presentare un ulteriore tag per la VLAN.

Il payload ha lunghezza minima di 46 byte e massima di 1500 byte, se non vengono raggiunti si aggiungono dei byte nel campo PAD (_padding field_).

Il checksum (_CRC-32_[^3]) è formato da 4 byte.

Il campo _type_ identifica il tipo di payload in base al protocollo situato all'interno (IP, ARP) e specifica a quale livello dello stack inviare il pacchetto.

La fine trasmissione si riconosce quando il segnale torna a 0, il conteggio del CRC può quindi terminare.

### Indirizzi Ethernet

Ogni dispositivo ha un numero univoco a livello globale che viene fissato dal produttore.

Con 6 byte sono possibili $2^{48} = 2,8 \times 10^{14}$ schede di rete in cui i primi 3 byte identificano il produttore e gli ultimi 3 il dispositivo.

> [!info]
> Quando un produttore termina i byte per i prodotti utilizza un nuovo identificativo, si evita così il _clash_, ovvero l'utilizzo da parte di più dispositivi dello stesso indirizzo.

Gli indirizzi vengono usati per riconoscere mittente e destinatario, quando l'adattatore legge nel frame il suo indirizzo controlla il CRC e passa il frame all'host, se vede che il messaggio non è indirizzato a lui lo scarta.

Un indirizzo speciale è il _FF:FF:FF:FF:FF:FF_ (cioè tutti 1) ed è l'indirizzo di _broadcast_ che viene sempre accettato da tutte le stazione connesse alla rete ed in ascolto.

### Algoritmo di ricezione Ethernet

Se vengono utilizzati dei mezzi condivisi allora realizzare un sistema broadcast risulta molto facile, questo perché tutti i pacchetti inviati sul bus vengono visti da tutti.

Il sistema _multicast_ risulta simile, sfrutta la comunicazione condivisa con l'aggiunta di essere identificato unicamente da un insieme di indirizzi specifico.

### Algoritmo di trasmissione Ethernet

L'algoritmo prende il nome di _Media Access Control_ (_MAC_) e viene eseguito dalla parte hardware.

Una volta che l'intestazione del frame da trasmettere è realizzata l'algoritmo mette l'adattatore in ascolto sulla linea, in caso di linea libera (0V) trasmette il pacchetto, in caso di linea occupata si sa che il limite superiore del messaggio è di 1500 byte, l'adattatore aspetta quindi un intervallo di tempo (_inter packet gap_, _IPG_) e riprova ad inviare. 

L'algoritmo viene chiamata _1-persistent CSMA/CD_ ed ha certezza di trasmissione dei dati dato che continuerà a tentare l'invio finché non ci riesce.

Il CSMA/CD ha tre stati:
- **Idle**: Nessuna stazione ha dei frame da trasmettere;
- **Contention**: Le stazioni aspettano che la linea sia libera, ovvero che non ci sia nessuna trasmissione per un tempo IPG (12 byte);
- **Transmission**: Le stazioni trasmettono.

La trasmissione in contemporanea può causare collisioni, per questo motivo il protocollo prevede un meccanismo di _collision detection_ (_CD_).

Quando viene rilevata una collisione si abbandona la trasmissione e si comunica ciò con l'invio di una sequenza casuale di bit che completino il pacchetto (_jamming sequence_), i bit restanti vengono chiamati _runt frame_.

> [!info]
> Lo standard di lunghezza dei cavi deriva dalla rilevazione degli errori: se il cavo fosse troppo lungo si rischia che un terminale rilevi che la rete è inattiva ed inizi a trasmettere anche se in verità un altro terminale ha già inviato un pacchetto causando così una collisione.
> Inoltre un terminale controlla la presenza di collisioni soltanto quando sta trasmettendo per cui se avesse già trasmesso il suo pacchetto non può sapere se ci sono state delle collisioni.

Per poter rilevare tutti gli errori di collisione il tempo minimo di trasmissione del frame deve essere maggiore del doppio del tempo di propagazione ($t_{\textnormal{trasmissione}} > 2 \cdot t_{\textnormal{propagazione}}$), questo è il motivo per cui si necessita di introdurre un eventuale padding nel payload.

### Post collisione - backoff esponenziale

Quando avviene una collisione, gli endpoint producono un breve segnale di jamming ed aspettano un tempo casuale prima di tentare nuovamente a trasmettere, questo perché se si riprovasse immediatamente si rincorrerebbe nello stesso problema.

Il tempo casuale è in realtà quello dell'IPG più una quantità casuale scelta da un range di possibili ritardi. Il range raddoppia ad ogni tentativo, da cui il nome _backoff esponenziale_.

ll tempo di trasmissione dipende quindi dal carico complessivo della rete.

**Variabili**:
- `k`: numero di collisioni subite per il frame corrente (inizialmente 0).
- `Kmax`: limite massimo di tentativi (tipicamente 10 per il backoff, 16 per l’abbandono).
- `Tslot`: durata dello slot time.
- `r`: numero casuale scelto nel range appropriato.

**Algoritmo**:
1. Trasmetti il frame.
2. Se non c’è collisione:  
    terminare.
3. Se c’è collisione:
    - interrompi la trasmissione;
    - invia la _jamming sequence_;        
    - incrementa `k = k + 1`.
4. Se `k > 16`:  
    abbandona definitivamente la trasmissione del frame.
5. Calcolo del ritardo di backoff:
    - determina l’intervallo casuale:  
        $r \in \{0, 1, ..., 2^{\textnormal{min}}(\textnormal{k}, \textnormal{Kmax}) − 1\}$  
        (con `Kmax = 10` per limitare la crescita).
    - calcola il tempo di attesa:  
        $\textnormal{Backoff} = r \times \textnormal{Tslot}$.
6. Attendi `Backoff` sul canale.
7. Riprova la trasmissione del frame.
8. Torna al punto 2.

### Efficienza Ethernet

L'efficienza è data dal rapporto tra il tempo di trasmissione ed il tempo effettivo necessario per trasmettere il frame:
$$
\textnormal{Efficienza} = \frac{\textnormal{Tempo di trasmissione}}{\textnormal{Tempo effettivo di trasmissione}}
$$

> [!example]
> IPG = 9.6 $\mu s$ = 96 bit = 12 byte
> Preambolo + SFD = 8 byte
> P = lunghezza del payload (fino a 1500 byte)
> Intestazione + CRC = 18 byte
> Efficienza teorica: 
> $$
> \frac{\textnormal{P}}{12 + 8 + \textnormal{P} + 18} = \frac{\textnormal{P}}{\textnormal{P} + 38} = \frac{1}{1 + \frac{38}{\textnormal{P}}}
> $$
> Più grande è P più è efficiente il protocollo.
> 
> Caso peggiore con padding:
> P = 1
> P effettivo = 46 (a causa del padding)
> Efficienza = $\frac{1}{46 + 38} = \frac{1}{84} = 1,2\%$
> 
> Caso peggiore senza padding:
> P = 46
> Efficienza = $\frac{46}{46 + 38} = 54\%$
> 
> Caso migliore:
> P = 1500
> Efficienza = $\frac{1500}{1500 + 38} = 97\%$

## Collegamenti Wireless

### Spread Spectrum

La tecnica spread Spectrum è utilizzata per il Bluetooth e serve a diminuire le interferenze dei segnali ma non garantisce la segretezza dei dati.

**Spread Spectrum con salto di frequenza**:

Il segnale viene trasmesso su una sequenza casuale di frequenze. Il ricevitore utilizza lo stesso algoritmo del mittente che viene inizializzato con lo stesso _seed_, questo permette ad entrambi di rimanere sincronizzati sulle stesse frequenze ad ogni salto.

La trasmissione viene quindi sparpagliata su più canali di frequenze e i salti tra di essi vengono regolati da: una sequenza di _hopping_ ed intervalli di tempo.

I numeri di sequenza dei canali e gli intervalli di tempo vengono decisi all'inizio della comunicazione.

La probabilità di trovarsi nella stessa frequenza ed ampiezza di un altra coppia di dispositivi è molto bassa.

Il _dwell time_ è l'intervallo di tempo di utilizzo di una certa frequenza e l'_hop time_ è il tempo di sintonizzazione da un segnale all'altro, questo crea anche un calo di efficienza in quanto la trasmissione si deve interrompere momentaneamente per cambiare frequenza e risintonizzarsi.

**Spread Spectrum a sequenza diretta**:

Per ogni bit che il mittente vuole trasmettere si invia lo `XOR` di quel bit e di $n$ bit casuali chiamati _sequenza di chipping_, questi sono generati da un generatore noto sia a mittente che destinatario e pertanto basta rieseguire lo `XOR` tra la sequenza ricevuta e quella di chipping per riottenere i valori iniziali.

Dato che la sequenza casuale è più lunga dei bit che si vogliono trasmettere si deve introdurre un meccanismo che espanda i bit e uno che li comprima.

I valori trasmessi sono noti come _codice di chipping a n bit_ e diffondono il segnale su una banda di frequenze $n$ volte più ampia.

### Diverse tecnologie wireless

|                             |        Bluetooth (802.15.1)        |           Wi-Fi (802.11)           |                Cellular                 |
| :-------------------------: | :--------------------------------: | :--------------------------------: | :-------------------------------------: |
|     Typical link length     |                10 m                |               100 m                |           Tens of kilometers            |
|      Typical data rate      |          2 Mbps (shared)           |          54 Mbps (shared)          |  Hundreds of kbps<br>(per connection)   |
|         Typical use         | Link a peripheral<br>to a computer | Link a computer<br>to a wired base | Link a mobile phone<br>to a wired tower |
| Wired technology<br>analogy |                USB                 |              Ethernet              |                   DSL                   |

Solitamente le comunicazioni sono di tipo punto-punto o punto-multipunto e la comunicazione tra nodi _client_ può essere diretta o instradata attraverso la stazione base (telefoni e Bluetooth)[^4].

Le connessioni wireless supportano la mobilità dei nodi e vengono forniti tre livelli di mobilità per i client:
- Nessuna mobilità, il ricevitore deve trovarsi in una posizione fissa per ricevere una trasmissione direzionale dalla stazione base;
- Mobilità all'interno del raggio d'azione di una base (Bluetooth);
- Mobilità tra basi (Telefoni e Wi-Fi).

Un'alternativa alle reti asimmetriche sono le _mesh network_ o _ad-hoc network_ in cui i nodi sono _peer_ e i messaggi possono essere inoltrati attraverso una catena di essi.

## WIFI - IEEE 802.11

Comunemente chiamato WIFI lo standard fa parte della famiglia di reti locali (standard 802), ovvero aree geografiche limitate, e viene principalmente usato per le reti all'interno di edifici.

È importante sottolineare come lo standard non è pensato per fornire servizi di connessione come 3G, 4G o 5G, offre invece servizi come la gestione dell'alimentazione, dell'accesso al mezzo di comunicazione condiviso e meccanismi di sicurezza.

La velocità di trasmissione può variare molto in quanto dipende dalla qualità del segnale (SNR), dal tasso di codifica, dalla larghezza del canale e dal fatto che va divisa per ogni stazione connessa.

![[Standard WIFI.excalidraw|1000]]

- 802.11 a salto di frequenze: 79 bande di frequenza a 1MHz, banda da 2,4 GHz e fino a 2 Mbps;
- 802.11 a sequenza diretta: sequenza di chipping a 11 bit (1 bit -> 11 bit), banda da 2,4 GHz e fino a 2 Mbps;
- 802.11b: variante della sequenza diretta e fino a 11 Mbps;
- 802.11a: fino a 54 Mbps tramite OFDM e opera su una banda di 5 GHz;
- 802.11g: retrocompatibile con 802.11b, utilizza la banda a 2,4 GHz con OFDM e fornisce fino a 54 Mbps;
- 802.11n: utilizza le bande da 2.4 e 5.4 GHz con OFDM, fornisce fino a 300 Mbps e permette MIMO;
- 802.11ac: utilizza la banda da 5 GHz, fornisce fino a 1300 Mbps e permette MIMO.

**OFDM**:

L'_Orthogonal Frequency-Division Multiplexing_ è uno schema di multiplazione a divisione di frequenza (FDM) in cui un gran numero di segnali a sottoportanti (_sub-carriers_) ortogonali strettamente distanziate viene utilizzato per trasportare i dati su canali paralleli.

![[OFDM.excalidraw|500]]

Questa è una tecnica originata dalla DSL che consente di codificare segnali digitali suddividendoli in sottoportanti indipendenti, così da sfruttare pienamente la banda disponibile.

Gli intervalli di frequenze sono suddivisi in canali (sottoportanti), $f_C$ indica la frequenza centrale di riferimento e ogni canale trasporta una parte dei bit in parallelo, utilizzando l’intero spettro.

### IEEE 802.11 – Collision avoidance

![[Collision 1.excalidraw|400]]

In questo caso ci sono quattro stazioni, ognuna in grado di inviare e ricevere dei segnali che raggiungono solo i nodi contenuti nella loro area.

Si supponga che A e C vogliano comunicare con B, non sapendo l'uno della presenza dell'altro ciascuno invia un frame, questi due poi si scontrano e B non riesce a codificarli.

A differenza di Ethernet né A né C sono consapevoli della collisione e sono quindi detti _hidden nodes_ l'uno rispetto l'altro.

Il protocollo CSMA/CD non è quindi adatto, inoltre, le stazioni possono o ascoltare o trasmettere, non possono quindi rilevare la presenza di collisioni durante l'invio.

![[Collision 2.excalidraw|400]]

C'è anche il problema dell'_exposed node_, si supponga che B stia inviando ad A, C è a conoscenza di questa trasmissione perché riceve la trasmissione di B ma sarebbe sbagliato concludere che non può trasmettere, C può quindi trasmettere con D in quanto non interferisce con la capacità di A di ricevere da B.

Per risolvere questi problemi si introduce quindi la gestione delle comunicazioni tramite turni.

**CSMA/CD**:

Il _Carrier Sense Multiple Access with Collision Avoidance_ è un algoritmo simile a quello Ethernet ma adattato ad una visione incompleta della rete in cui la differenza principale è la sostituzione della Collision Detection con la _Collision Avoidance_.

L'algoritmo prevede che prima di inviare i dati effettivi, il mittente e ricevitore si scambiano dei frame di controllo che servono a verificare se sono in una posizione da poter comunicare e anche ad avvisare i nodi vicini che sta per iniziare una trasmissione in modo che rimangano in silenzio.

Il mittente invia un _Request to Send_ (_RTS_), questo include un campo _DURATION_ che specifica per quanto tempo il mittente intende riservare il mezzo, includendo l’intera trasmissione fino alla ricezione dell'ACK.

Se il ricevitore riceve correttamente l'RTS allora risponde con un _Clear to Send_ (_CTS_) che contiene lo stesso valore DURATION in modo da confermare ed autorizzare la connessione.

A questo punto avviene l'invio effettivo dei dati in cui il ricevitore invia un ACK al mittente quando termina la ricezione.

**SIFS**:

Lo _Short Inter Frame Space_ è un intervallo di tempo di pausa che intercorre tra la fine e l'inizio di un frame, questo serve a fornire il tempo alle componenti elettroniche per cambiare modalità (ricezione / trasmissione) e al ricevitore per elaborare il messaggio.

**DIFS**:

È il tempo che intercorre tra la fine e l'inizio di una nuova comunicazione, corrisponde a un $\textnormal{SIFS} + 2\ \textnormal{slot time}$.

**NAV**:

Il _Network Allocation Vector_ è il tempo in cui gli altri nodi non possono trasmettere in quanto hanno ricevuto un RTS con una durata di comunicazione che indica il loro periodo di attesa, si risolve così il problema dei nodi nascosti.

> [!info]
> Qualsiasi nodo che vede l'RTS ma non il CTS non è abbastanza vicino al ricevitore per interferire con esso ed è quindi libero di trasmettere, si risolve così il problema dei nodi esposti.

**Problema**:

Se due o più nodi rilevano un collegamento inattivo e cercano di trasmettere un RTS allo stesso momento i due frame si scontreranno.

L'802.11 non supporta il rilevamento delle collisioni e quindi i mittenti si accorgono della collisione solo quando non ricevono il CTS, si utilizza allora lo stesso algoritmo di backoff esponenziale utilizzato su Ethernet.

Si aggiunge poi l'uso degli ACK in cui il ricevitore invia un ACK dopo ogni frame ricevuto con successo, i nodi devono quindi attendere la fine dell'ACK prima di provare a trasmettere.

### IEEE 802.11 – Formato dei frame

![[Frame.excalidraw|1000]]

Nello standard 802.11 sono presenti diversi tipi di frame, come DATA, RTS, CTS, ACK e BEACON[^5], identificabili dai bit Type e Subtype del campo Frame Control.

La struttura generale del frame è simile a quella Ethernet, ma differisce per un’intestazione più articolata: 
- I campi _To DS_ e _From DS_ per il sistema di distribuzione;
- Il campo _Power Management_ per la gestione energetica;
- Il bit _WEP_ per un'eventuale cifratura dei dati.

Il campo _Frame Control_ (16 bit) è seguito dal campo _Durata/ID_, utilizzato dal meccanismo CSMA/CA per impostare il NAV o, nei Beacon, per trasportare l’identificatore della rete. Seguono fino a quattro indirizzi MAC e il campo _Sequence Control_, utile per la frammentazione.

Le dimensioni complessive dipendono dal tipo di frame: i frame di controllo sono generalmente più compatti, mentre i frame DATA includono 30 byte di intestazione, fino a 2312 byte di payload ed un FCS di 4 byte finale.

![[Pacchetti di controllo.excalidraw|500]]

L’uso di RTS, CTS e ACK introduce un notevole overhead: a ogni trasmissione di un frame DATA si sommano le dimensioni dei frame di controllo e degli intervalli temporali richiesti dal protocollo (SIFS e DIFS), aumentando il costo complessivo della comunicazione.

Quando si instaura una connessione si ha quindi un alto overhead poiché ogni frame DATA necessita di un intestazione di 82 byte a cui vanno sommati gli intervalli SIFS e DIFS.

> [!example]
> Si supponga di voler trasmettere un messaggio in 802.11g a 54 Mbps
> Il CSMA/CA aggiunge: $1\ \textnormal{DIFS} + 3\ \textnormal{SIFS} = 4\ \textnormal{SIFS} + 2\ \textnormal{Slot time} = 4 \cdot 10 + 2 \cdot 9 = 54 \mu s$ che equivalgono a $58 \mu s \times 54 Mbps = 391,5\ \textnormal{byte}$
> L'intestazione del fra,e il CRC e altri frame inviati hanno lunghezza totale di 82 byte
> La trasmissione completa introduce un overhead di $473,5$ byte per transazione
> Se il carico utile fosse di 1500 byte l'efficienza non supererebbe il $\frac{1500}{1500+473,5}=76\%$
> Si ottiene che la larghezza di banda netta è $\leq$ 41 Mbps e non 54 Mbps
> L'Ethernet raggiungeva un efficienza del $97\%$
> 
> Si nota quindi che i router inviano i pacchetti alla velocità dichiarata ma quelli realmente utili sono di meno, ottenendo un efficienza minore.

I dati trasmessi quando maggiori di 2312 byte sono divisi in più frame chiamati _frammenti_, essi hanno tutti il bit _MoreFrag_ impostato a 1 tranne l'ultimo e causano un ulteriore overhead dato che viene inviato un ACK per ogni pacchetto.

Questo metodo è poco usato dato che se si perdesse un solo pacchetto si perderebbe l'intera comunicazione.

### IEEE 802.11 – Distribuzione

Gli access point sono collegati tramite un  _Distribution System_ (DS). In questo contesto il _roaming_ consente di far apparire tutti gli access point collegati al DS come un’unica rete, permettendo quindi al dispositivo di passare da uno ad un altro mantenendo la continuità del servizio.

In questo processo assumono un ruolo centrale i quattro campi di indirizzo del frame 802.11: mittente, destinatario, access point di origine e access point di destinazione, la loro interpretazione varia in base ai bit To DS e From DS presenti nel campo Frame Control:
- Mittente e destinatario collegati allo stesso access point: entrambi i bit DS a 0; Addr1 è il destinatario, Addr2 è il mittente.
- Quando è necessario attraversare un access point, uno dei campi di indirizzo aggiuntivi viene utilizzato per indicarlo.
- Con entrambi i bit DS impostati a 1, il frame passa da un nodo wireless al sistema di distribuzione e poi a un altro nodo wireless, qui Addr1 indica la destinazione finale, Addr2 il mittente immediato, Addr3 la destinazione intermedia e Addr4 la sorgente originale.

## Bluetooth – 802.15

Questo protocollo viene spesso utilizzati per comunicazioni a brevissimo raggio.

## Switching e Forwarding

### Switch

**Topologia a stella**:

### Approccio switching and forwarding

**Approccio senza connessione**:

**Approccio con connessione**:

**Approccio source routing**:

## Bridge e Switch LAN

### Distributed Spanning Tree

**Funzionamento pratico dell’algoritmo**:

**Limitazioni di questo algoritmo**:

**Limitazioni dei bridge**:

[^1]: ![[Architettura_ISO_OSI.excalidraw]]

[^2]: ![[Lezione 1 - 14 Novembre 2025#Codifica Manchester]]

[^3]: *CRC-32*: $C(x) = x^{32} + x^{26} + x^{23} + x^{22} + x^{16} + x^{12} + x^{11} + x^{10} + x^{8} + x^{7} + x^{5} + x^{4} + x^{2} + x + 1$
![[Lezione 2 - gg m yy#Cyclic Redundancy Check]]]

[^4]: Comunicazione di tipo asimmetrica.

[^5]: Usato dall'algoritmo di scansione per trovare l'SSD degli access point.
