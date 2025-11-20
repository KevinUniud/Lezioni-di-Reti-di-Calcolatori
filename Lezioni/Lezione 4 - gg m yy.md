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

TODO: Trunking

### Approccio switching and forwarding

**Approccio senza connessione**:

**Approccio con connessione**:

**Approccio source routing**:

## Bridge e Switch LAN

### Distributed Spanning Tree

**Funzionamento pratico dell’algoritmo**:

**Limitazioni di questo algoritmo**:

**Limitazioni dei bridge**:
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
