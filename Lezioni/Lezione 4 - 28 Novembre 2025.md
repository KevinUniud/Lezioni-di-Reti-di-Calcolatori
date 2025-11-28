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

L'internetworking è un insieme arbitrario di reti interconnesse per fornire un servizio di consegna dei pacchetti da host a host.

Per far si che i pacchetti riescano a viaggiare in una rete eterogenea di usano degli instradatori chiamati _router_.

Quando un router riceve un pacchetto incapsulato in un certo protocollo di livello 2 e deve inoltrarlo verso un dispositivo che utilizza un diverso protocollo di livello 2, esegue la decapsulazione del frame ricevuto ed effettua una nuova incapsulazione nel formato richiesto dall’interfaccia di uscita.

L’inoltro avviene sulla base del protocollo IP: il router utilizza l’intestazione IP del pacchetto per determinarne la destinazione e garantire un formato uniforme indipendentemente dalla tecnologia di livello 2.

Gli indirizzi fisici degli host, specifici della tecnologia utilizzata, vengono quindi gestiti localmente sulle singole interfacce, mentre l’indirizzo IP (IPv4 o IPv6) rimane l’identificatore usato per l’instradamento end-to-end.

Il livello 3 fornisce anche API ai livelli superiori dello stack, tra cui il servizio di inoltro dei pacchetti, questo indipendentemente dalla posizione dell’host o dalla tecnologia di rete utilizzata, si occupa di instradare il messaggio verso la destinazione, la consegna tuttavia non è garantita in modo assoluto.

Un pacchetto (o “datagramma”) può raggiungere il livello IP in due modi:

**Da un livello superiore** (ad esempio TCP o UDP) che richiede l’invio di dati a un altro host:
1. I dati vengono incapsulati in un datagramma IP;
2. Il datagramma viene trasmesso al nodo successivo tramite un protocollo di livello collegamento adatto all'interfaccia utilizzata.

In questo modo un’applicazione può inviare dati attraverso qualunque interfaccia disponibile senza conoscerne le caratteristiche. La scelta dell’interfaccia di uscita è compito del livello IP.

**Da un livello inferiore** quando un pacchetto arriva tramite un’interfaccia di rete:  
- Se il pacchetto è destinato all’host locale, l’intestazione IP viene rimossa e il payload viene consegnato al corretto protocollo di trasporto;  
- Se è destinato a un altro host, e il dispositivo è configurato per l’inoltro, il pacchetto viene instradato verso la prossima interfaccia utilizzando il relativo protocollo di datalink.

Anche in questo caso si adotta una strategia di store-and-forward ma a differenza degli switch di livello 2, il funzionamento è indipendente dalla tecnologia di collegamento sottostante.

Il modello di consegna dei pacchetti è senza connessione, quindi di tipo _best-effort_ e non affidabile, di conseguenza i pacchetti possono venire persi, duplicati, ritardati o arrivare fuori ordine.

L’indirizzamento è globale e consente di identificare qualsiasi host della rete Internet, astraendo dagli indirizzi di livello 2 (MAC) utilizzati sui collegamenti locali.

### Formato dei pacchetti

Ogni _word_ è formata da 4 bit mentre l'intestazione è sempre di almeno 1 byte.

I parametri dei pacchetti IPv4 sono:
- Versione (4 bit): vale 4 per IPv4 e 6 per IPv6
- Header Length (4 bit): numero di parole a 32 bit che formano l’intestazione (la lunghezza effettiva è 4 × H.len)
- TOS (8 bit): Type of Service
- Lunghezza totale (16 bit): dimensione complessiva del pacchetto, inclusa l’intestazione, espressa in byte
- Identificazione (16 bit): utilizzata per la frammentazione
- Flag (3 bit) e Offset (13 bit): impiegati per la frammentazione; flag 0 indica assenza di frammentazione, 1 indica “don’t fragment” (DF), 2 indica “more fragments” (MF)
- TTL (8 bit): numero massimo di hop consentiti prima che il datagramma venga scartato
- Protocollo (8 bit): valore che identifica il protocollo di livello superiore (TCP=6, UDP=17, altri specificati nelle tabelle standard)
- Checksum (16 bit): calcolato solo sull’intestazione
- Indirizzo sorgente e indirizzo destinazione (32 bit ciascuno)

Il TOS è un campo da 8 bit pensato per indicare al router come trattare un datagramma in base alle esigenze del servizio.

In origine serviva a gestire la priorità in caso di congestione e a scegliere il percorso più adatto in funzione delle caratteristiche delle interfacce di rete.

I codici principali erano:
- 0000: servizio normale
- 1000: minimizzare il ritardo
- 0100: massimizzare il throughput
- 0010: massimizzare l’affidabilità
- 0001: minimizzare il costo

Il TOS era spesso usato per dare priorità a pacchetti di controllo. Se non impostato, il router decide autonomamente e i router più avanzati possono dedurre dal payload quale servizio applicare. Alcuni protocolli avevano valori predefiniti (ad esempio TELNET e HTTP orientati al ritardo, FTP dati orientato al throughput, SNMP all’affidabilità).

Nelle versioni moderne di IPv4 il TOS è stato sostituito dal DSCP, che offre servizi differenziati. Se gli ultimi 3 bit sono 000, i primi 3 indicano la priorità come nel TOS originale. Se sono diversi da 000, i primi 6 bit definiscono uno dei 64 servizi disponibili: standard IETF, definiti da autorità locali o sperimentali.

### Frammentazione e ricomposizione

La frammentazione nasce dalla necessità di adattare i datagrammi alla MTU del livello 2.  
- Se DF=1, il datagramma viene scartato e il mittente notificato via ICMP.  
- Se DF=0, il router frammenta il datagramma; i frammenti viaggiano autonomamente e condividono lo stesso identificatore.  
- Ogni frammento deve rispettare: intestazione + payload ≤ MTU; il payload è scelto come massimo multiplo di 8 byte possibile.

### Indirizzi globali

Gli indirizzi IPv4 sono unici e gerarchici: una parte identifica la rete (assegnata a un Autonomous System), l’altra l’host. I router instradano sulla base della parte di rete. L’uso di gerarchie evita tabelle non scalabili: si memorizzano solo le reti, non i singoli host.

- Classe A: prefisso 0; 7 bit rete, 24 bit host.  
- Classe B: prefisso 10; 14 bit rete, 16 bit host.  
- Classe C: prefisso 110; 21 bit rete, 8 bit host.  
- Classe D: 1110 per multicast.  
- Classe E: 1111 riservata.  
In ogni rete gli host “tutti 0” e “tutti 1” non sono assegnabili (rete e broadcast).

### Subnetting

Permette di suddividere ulteriormente la parte host della rete per ottenere sottoreti interne, invisibili all’esterno. Le subnet mask determinano quali bit appartengono alla rete. La maschera 0.0.0.0 ha sempre match e punta al router predefinito.

### Indirizzamento senza classi

Il _CIDR (Classless Inter-Domain Routing)_ sostituisce le classi. Consente di aggregare reti contigue per ridurre le tabelle di instradamento e migliorare l’efficienza dell’uso degli indirizzi. Esempio: due reti /24 contigue possono essere aggregate in una /23; quattro reti in una /22. Riduce memoria e numero di entry necessarie nei router.

### Indirizzi IP per usi speciali (RFC 5735)

- 0.0.0.0/8: non instradabile; indica “qualsiasi indirizzo”.  
- 10.0.0.0/8: rete privata.  
- 127.0.0.0/8: loopback.  
- 169.254.0.0/16: autoconfigurazione locale quando il DHCP non risponde.  
- 172.16.0.0/12, 192.168.0.0/16: reti private.  
- 224.0.0.0/4: indirizzi multicast.  
- 240.0.0.0/4: riservati per usi futuri.

## Protocolli IP ausiliari (ancillary)

I protocolli ausiliari di IP (ARP, ICMP, DHCP, ecc.) forniscono tutte le funzioni che IP non può svolgere da solo: risoluzione degli indirizzi, configurazione degli host, segnalazione degli errori, gestione delle tabelle di inoltro.

Alcuni operano a livello 3 (ARP, ICMP), altri a livello applicativo (DHCP).

### Protocollo di traduzione degli indirizzi (ARP)

_ARP_ collega indirizzi IP a indirizzi MAC. 

Ogni router mantiene una tabella ARP con le associazioni IP–MAC degli host o dei router next-hop.

Se un indirizzo non è presente, il router invia una richiesta ARP in broadcast; chi possiede quell’IP risponde in unicast.

Le entry scadono dopo un timeout e devono essere rinnovate.

I pacchetti ARP sono incapsulati direttamente nei frame di livello 2.

Un ARP proxy può rispondere al posto di altri host o reti.

### Configurazioni degli host e DHCP

_DHCP_ automatizza la configurazione degli indirizzi IP.

Un host appena collegato invia un messaggio DISCOVER in broadcast; il server DHCP offre un indirizzo e, dopo la conferma, il client imposta IP, gateway, DNS e altri parametri.

Gli indirizzi possono essere statici (basati su MAC) o dinamici (lease temporanei).

In assenza di server DHCP l’host si auto-configura con un indirizzo 169.254.0.0/16.

Agenti di relay permettono a un server DHCP di servire più reti.

Se il server perde lo stato, può generare conflitti di indirizzi.

### Protocollo di segnalazione e gestione della rete (ICMP)

_ICMP_ gestisce messaggi di errore e di controllo: host o rete irraggiungibili, TTL scaduto, checksum errato, redirect verso percorsi migliori.

È incapsulato in IP e non trasporta dati applicativi, solo informazioni diagnostiche contenenti parte del datagramma scartato.

Echo (ping) verifica la raggiungibilità e misura l’RTT.

Traceroute sfrutta gli errori ICMP generati da TTL crescenti per scoprire i router attraversati, pur con limiti dovuti ai percorsi variabili.

ICMP-Redirect informa un host che esiste un router migliore verso una destinazione, permettendo di aggiornare le tabelle di inoltro.

## Reti IP private e NAT

Le reti IP private usano indirizzi non instradabili (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) per creare LAN isolate o indipendenti da Internet.

Questa scelta semplifica la gestione interna, evita la richiesta di IP pubblici e permette la duplicazione degli stessi indirizzi in reti diverse.

Quando un host entra in una rete privata, riceve un IP tramite DHCP valido solo nella sottorete locale.

Poiché gli indirizzi privati non possono attraversare Internet, serve un meccanismo di traduzione: il _NAT (Network Address Translation)_.

Il NAT, situato al confine tra LAN privata e rete pubblica, sostituisce gli indirizzi IP privati e, se necessario, le porte TCP/UDP, con un indirizzo pubblico.

Mantiene una tabella dei flussi: ogni pacchetto in uscita crea una entry; le risposte vengono riconosciute e ritrasformate verso l’host privato corretto.

### Funzionamento NAT di base full cone e con PAT

Nel _NAT base (full cone)_, l’indirizzo sorgente privato viene rimpiazzato dal pubblico del router.

Con _PAT_, se più host usano la stessa porta sorgente, il router assegna porte pubbliche diverse per distinguerli.

Il NAT permette quindi di condividere un solo IP pubblico fra molti dispositivi, agevola la migrazione tra ISP e consente applicazioni come il load balancing.

### Applicazioni dei NAT

Il NAT agisce anche da filtro naturale: pacchetti in ingresso privi di una entry nella tabella vengono scartati.

Per consentire accessi dall'esterno verso host specifici si usa il _port forwarding_, che crea regole esplicite nella tabella del NAT, con conseguenti implicazioni di sicurezza.

### “Firewall” naturale e port forwarding

La presenza di NAT su entrambe le reti rende la comunicazione diretta difficile.

Per superare il blocco si usa il _hole punching_, basato su due protocolli:  
- _STUN_, che permette a un host di scoprire il proprio indirizzo IP pubblico e il comportamento del proprio NAT;  
- _TURN_, che funge da relay esterno e permette di far passare il traffico quando il NAT non consente alcuna connessione diretta. Il server TURN inoltra i pacchetti tra i due host, consentendo la successiva comunicazione diretta una volta popolata la tabella del NAT.

### Preoccupazioni relative al NAT

Il NAT introduce complessità: richiede riscrittura delle intestazioni, calcolo dei checksum, gestione dello stato, traffico extra verso server STUN/TURN e riduce il modello end-to-end di Internet.

Nonostante i limiti, è ampiamente utilizzato perché permette di usare poche risorse pubbliche e mantenere molte reti private, in attesa di un’adozione più completa di IPv6.

[^1]: Trasmissione bidirezionale simultanea.

[^2]: Dimensione massima del payload.
