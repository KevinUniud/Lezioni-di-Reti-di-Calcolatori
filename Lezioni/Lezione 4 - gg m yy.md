## Switching e Forwarding

### Switch

Il commutatore (_switch_) è un dispositivo che permette di interconnettere i collegamenti per formare un'unica rete.

Lo switch è multi-input / multi-output e permette di trasferire i pacchetti in modalità full-duplex[^1].

Il dispositivo può essere collegato ad un insieme di link che possono essere di più tipi e per ciascuno di essi esegue il protocollo di collegamento appropriato.

Il suo compito principale è la commutazione e l'inoltro (_switch and forward_): riceve i pacchetti in arrivo su uno dei collegamenti e lo ritrasmette su un altro.

La strategia più comune è lo _store-and-forward_, ovvero inoltrare i pacchetti mantenendo il formato dei frame sempre uguale:
1. Riceve un frame;
2. Controlla che il CRC sia valido, in caso viene scartato;
3. Utilizza una tabella interna per scegliere il link a cui inoltrarlo;
4. Invia il frame al link scelto

> [!info]
> Gli switch più moderni utilizzano una strategia più veloce chiamata _cut through_ che consiste nel fermarsi alla lettura dell'intestazione.

Lo switch accumula dinamicamente gli indirizzi MAC osservati e li associa alle porte, costruendo una mappatura dei dispositivi raggiungibili attraverso ciascuna interfaccia.

La lista dei dispositivi che costruisce è la tabella MAC (MAC address table o forwarding table). Il processo avviene così:
1. Quando un frame arriva su una porta, lo switch legge l’indirizzo MAC sorgente.
2. Registra nella sua tabella l’associazione “MAC sorgente → porta di ingresso”.
3. Se in seguito deve inoltrare un frame verso quel MAC, utilizza l’associazione per inviare il traffico solo sulla porta corretta.
4. Se l’indirizzo MAC di destinazione non è presente nella tabella, lo switch esegue flooding inviando il frame su tutte le porte tranne quella di ingresso.
5. Le voci della tabella scadono dopo un intervallo di tempo se non vengono più osservati frame da quei MAC.

**Topologia a stella**:

Il modello a stella è una topologia di rete in cui tutti i dispositivi sono collegati a un nodo centrale, di solito uno switch o un hub. 

Ogni nodo comunica con gli altri passando attraverso questo elemento centrale, che gestisce l’inoltro dei dati.

Caratteristiche principali:
- Ogni dispositivo ha un collegamento punto-punto dedicato verso il nodo centrale;
- Il nodo centrale controlla o smista il traffico, evitando collisioni (se è uno switch);
- Un guasto in un cavo o in un dispositivo periferico non compromette il funzionamento della rete;
- Un guasto del nodo centrale interrompe invece l’intera comunicazione;
- L’architettura è semplice da installare, estendere e diagnosticare;
- Può verificarsi un collo di bottiglia: lo switch, essendo il punto di passaggio obbligato per tutto il traffico e disponendo di risorse limitate, può non riuscire a gestire volumi eccessivi di dati.

**Trunking**:

Il trunking consente a uno switch di trasportare più VLAN su un unico collegamento utilizzando il tagging dei frame per indicare la VLAN di appartenenza. 

Le porte trunk accettano frame taggati e possono limitare le VLAN ammesse; la native VLAN è l’unica trasmessa senza tag.

### Approccio switching and forwarding

Ci sono tre metodi per capire dove inoltrare i frame:
- Datagramma o approccio senza connessione;
- Circuito virtuale o approccio orientato alla connessione; 
- Instradamento alla fonte (source routing);

È importante che tutti gli switch all'interno di una rete utilizzino lo stesso algoritmo in quanto sono metodi completamente diversi.

**Approccio senza connessione**:

Ogni pacchetto viene instradato in modo indipendente. Lo switch non mantiene informazioni sul flusso; ogni pacchetto contiene l’indirizzo completo di destinazione.

_Vantaggi_:
- Nessuna fase di setup;
- Maggiore robustezza: se un nodo cade, i pacchetti possono seguire percorsi alternativi;
- Buona scalabilità.

_Svantaggi_:
- Instradamento meno efficiente;
- Ordine dei pacchetti non garantito;
- Maggiore overhead perché ogni pacchetto porta tutte le informazioni di instradamento.

**Approccio con connessione**:

Prima della trasmissione si stabilisce un circuito virtuale. Gli switch memorizzano lo stato del flusso in una tabella ed inoltrano i pacchetti usando etichette o identificatori del circuito.

La tabella che viene formata all'interno degli switch contiene per ogni circuito virtuale attivo:
- Associazione tra porta di ingresso;
- Identificatore del circuito in ingresso;
- Porta di uscita;
- Identificatore del circuito in uscita.

_Vantaggi_:
- Minore overhead nei pacchetti;
- Instradamento più veloce dopo il setup;
- Consegna più ordinata e affidabile.    

_Svantaggi_:    
- Necessità di una fase di setup;
- Minore flessibilità: la caduta di un nodo interrompe il circuito;
- Stato mantenuto negli switch, quindi minor scalabilità.

> [!info]
> Si possono configurare anche delle connessioni permanenti.

**Approccio source routing**:

L’intero percorso è specificato dall'host sorgente e inserito nei pacchetti. Gli switch non calcolano il percorso: leggono e seguono le istruzioni incluse nei pacchetti.

_Vantaggi_:
- Nessun mantenimento di stato negli switch.
- Massimo controllo da parte del mittente sul percorso.
- Possibilità di percorsi personalizzati o ottimizzati.

_Svantaggi_:
- Overhead elevato: il percorso completo deve essere incluso nei pacchetti.    
- Complessità per il sorgente, che deve conoscere la topologia.
- Cambiamenti di rete richiedono aggiornamenti immediati delle informazioni di routing nel mittente.
## Bridge e Switch LAN

Un nodo che permette di inoltrare i frame da un rete Ethernet all'altra è detto _bridge_.

I primi bridge utilizzavano una strategia di store-and-forward con _flooding_: quando ricevono un frame su un’interfaccia viene copiato su tutte le altre, verificavano inoltre la correttezza del frame prima dell’inoltro.

Ora i bridge osservano il flusso di pacchetti che circolano sulla rete e internamente memorizzano dove sono le posizioni dei dispositivi.

Se vedono arrivare un frame che a l'indirizzo di destinazione sullo stesso link non fanno nulla dato che è già all'interno della sottorete corretta.

Se arriva un frame che ha l'indirizzo di un host che sta su un altra rete mandano il pacchetto riconosciuto sull'apposito link.

> [!info]
> Gli host non rilevano la presenza dei bridge.

Per sapere dove sono gli host ci sono due possibilità, programmare il bridge manualmente oppure utilizzare lo switch ad autoapprendimento.

Nella seconda opzione il bridge aggiorna automaticamente la propria tabella osservando il traffico sulle interfacce, registra gli indirizzi dei mittenti dei frame che riceve e, man mano che altri host rispondono, li aggiunge anch'essi come nuovi mittenti.

Ogni voce della tabella ha un timeout: se un indirizzo non viene più visto per qualche minuto, viene rimosso per gestire correttamente eventuali spostamenti degli host.

Se il bridge riceve un frame destinato a un indirizzo non presente in tabella, lo inoltra in flooding su tutte le altre porte.

Nel caso in cui ci siano dei loop nella rete un pacchetto potrebbe continuare a girarci, per evitare ciò si introduce il _Time To Live_ (_TTL_) che definisce il numero massimo di hop che un pacchetto può fare prima di essere cancellato.

Un altra soluzione al problema sarebbe conoscere la strada che il pacchetto deve effettuare, si cerca quindi di fare _network mapping_ in modo da avere una visione a grafo della rete, questo viene effettuato tramite un algoritmo chiamato _Distributed Spanning Tree_(_DST_).

### Distributed Spanning Tree

Si vuole creare un albero senza cicli che collega tutti i bridge, disattivando le porte che produrrebbero percorsi ridondanti.

**Funzionamento**:
- _Elezione del root bridge_: Ogni bridge ha un identificatore (bridge ID), quello con l’ID più basso diventa il root bridge;
- _Calcolo della distanza dal root_: Ogni bridge determina il costo minimo per raggiungere il root attraverso le proprie porte;
- _Selezione delle porte_:
	- _Root port_: per ogni bridge non root, è la porta con il costo minimo verso il root;
	- _Designated port_: per ogni segmento di rete, è la porta del bridge che offre il minor costo verso il root;
	- _Porte bloccate_: tutte le altre porte vengono messe in stato di blocking per impedire i cicli.
- _Costruzione dell’albero attivo_: Solo le root port e le designated port rimangono attive per l’inoltro dei frame. Le porte bloccate ascoltano il traffico ma non inoltrano.

Per determinare i costi i bridge scambiando dei _BPDU_, cioè messaggi di configurazione inviati sui segmenti condivisi.

Ogni BPDU contiene una tripla $(X, d, Y)$:
- $X$: L’identificatore del root bridge;
- $d$: La distanza da esso;
- $Y$: L’identificatore del bridge che invia il messaggio.

Lo standard 802.1D definisce questi messaggi che includono anche altre informazioni come priorità e costi, e sono trasportati in frame 802.3 con un indirizzo di destinazione speciale.

La rete rimane quindi completamente connessa ma senza percorsi ciclici e qualora un collegamento si guasti le porte bloccate possono essere riattivate per ristabilire la connettività seguendo lo spanning tree ricalcolato.

**Limitazioni**:

La disattivazione delle porte genera percorsi più lunghi, aumentando il rischio di congestione e di perdita di pacchetti quando i buffer si saturano, inoltre, l'algoritmo non può instradare il traffico su percorsi alternativi quando un bridge è saturo e deve ricalcolare l’intera configurazione ogni volta che se ne aggiunge o rimuove uno.

I bridge non sono scalabili: lo spanning tree cresce con complessità lineare e il traffico broadcast diventa eccessivo su reti ampie.

Non gestiscono bene l’eterogeneità, perché richiedono reti con caratteristiche simili. 

Con MTU[^2] diverse si devono frammentare e ricomporre i frame introducendo così ulteriore complessità.

## Internetworking



### Formato dei pacchetti

### Frammentazione e ricomposizione

### Indirizzi globali

### IP Datagram Forwarding

### Subnetting

### Indirizzamento senza classi

### IP fowarding rivisitato

### Indirizzi IP per usi speciali (RFC 5735)

## Protocolli IP ausiliari (ancillary)

### Protocollo di traduzione degli indirizzi (ARP)

### Formato del pacchetto ARP

### ARP proxy

### Configurazioni degli host e DHCP

### Protocollo di segnalazione e gestione della rete (ICMP)

### Tipi di messaggi di richiesta ICMP

## Reti IP private e NAT

### Funzionamento NAT di base full cone

### Funzionamento NAT di base full cone con PAT

### Applicazioni dei NAT

### “Firewall” naturale e port forwarding

### Hole Punching (STUN)

### Hole Punching (TURN)

### Preoccupazioni relative al NAT



[^1]: Trasmissione bidirezionale simultanea.

[^2]: Dimensione massima del payload.
