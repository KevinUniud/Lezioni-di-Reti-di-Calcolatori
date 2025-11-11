TODO: Add links

## Introduzione  
### Scopo del corso  
### Cos’è una rete  
### Caratteristiche comuni di una rete 
### Caratteristiche specifiche delle reti di calcolatori  
Applicazioni  
Requisiti 
Connettività  
Affidabilità  
Amministratori di reti (management)  
### Architetture di rete e protocolli  
Interfacce 
Protocolli  
Incapsulamento 
Architettura ISO/OSI  
Architettura TCP/IP  
Architettura internet 
### Network API  
Modello Client-Server tramite UDP  
Modello Client-Server tramite TCP  
### Performance  
Definizione di bandwidth  
Definizione di throughput  
Latenza  
Delay vs throughput  
Jitter  
## Reti di computer  
### Link e frequenze 
### Codifica e simboli  
Teorema di Nyquist-Shannon 
Teorema di Shannon-Hartley  
### Tipi di codifiche 
Codifica NRZ
Codifica NRZI  
Codifica Manchester  
Codifica MLT-3 
Codifica 4B/5B 
### Frame 
Protocollo orientato ai byte 
Protocollo orientato ai bit  
Protocollo PPP 
### Errori 
Calcolo delle probabilità 
Detenzione degli errori  
Cyclic Redundancy Check  
### Trasmissione affidabile  
Protocollo stop-and-wait 
Protocollo sliding window  
### Ethernet – IEEE 802.3  
10base5
Fattori di velocità minima per i cavi di rete  
10baseT  
Reti miste (mixed) 
Altre varianti  
### Frame Ethernet  
Formato del pacchetto  
Indirizzi Ethernet  
Algoritmo di ricezione Ethernet  
Algoritmo di trasmissione Ethernet 
Post collisione – backoff esponenziale 
Efficienza della Ethernet 
### Collegamenti wireless  
Spread Spectrum  
Diverse tecnologie wireless  
### WIFI – IEEE 802.11
IEEE 802.11 – Collision avoidance  
IEEE 802.11 – Formato dei frame  
IEEE 802.11 – Distribuzione  
### Bluetooth – 802.15
BLE (Bluetooth Low Energy)  
## Internet  
### Switching e Forwarding  
Switch 
Approccio switching and forwarding  
### Bridge e Switch LAN  
Distributed Spanning Tree  
### Internetworking  
Formato dei pacchetti  
Frammentazione e ricomposizione  
Indirizzi globali  
IP Datagram Forwarding 
Subnetting  
Indirizzamento senza classi  
IP fowarding rivisitato  
Indirizzi IP per usi speciali (RFC 5735) 
### Protocolli IP ausiliari (ancillary)  
Protocollo di traduzione degli indirizzi (ARP)  
Formato del pacchetto ARP  
ARP proxy  
Configurazioni degli host e DHCP  
Protocollo di segnalazione e gestione della rete (ICMP) 
Tipi di messaggi di richiesta ICMP  
### Reti IP private e NAT  
Funzionamento NAT di base (“cono pieno” = “full cone”)  
Funzionamento NAT di base (“cono pieno” con PAT)  
Applicazioni dei NAT  
“Firewall” naturale e port forwarding 
Hole Punching (STUN)  
Hole Punching (TURN)  
Preoccupazioni relative al NAT 
### Routing 
Routing vs forwarding  
Algoritmi di routing 
Vettore di distanza (distance vector)  
Algoritmo di Bellman-Ford  Problema del conteggio all’infinito  
Protocollo di informazione di routing (RIP)  
Stato dei collegamenti (link state)  
Instradamento sul percorso più breve (Dijkstra) 
Protocollo Open Shortest Path First (OSPF)  
## Internetworking avanzato 
### Instradamento inter-dominio  
Propagazione del percorso (Route Propagation)  
EGP e BGP  
Integrazione del routing inter-dominio e intra-dominio  
Prevenzione dei loop BGP e policy routing  
Problemi di BGP  
### IP di nuova generazione (IPv6)  
Formato del pacchetto  
Priorità  
Flow label  
Transizione da IPv4 a IPv6
### Internet Multicast  
Multicast in IP  
Routing multicast basato sulla sorgente (source-based)  
Instradamento multicast ad albero condiviso per gruppi (group-shared trees) 
Protocollo di instradamento multicast a vettore di distanza (DVMRP)  
Multicast indipendente dal protocollo (PIM)  
Backbone multicast (MBONE) 
## Protocolli end-to-end  
### Livello di trasporto  
Indirizzamento end-to-end in TCP/IP  
Socket 
Modello client/server  
Tipi di comunicazione  
### UDP 
Calcolo del checksum 
API per i servizi UDP  
Demultiplexer semplice  
### TCP 
Controllo del flusso vs controllo della congestione  Problemi end-to-end  
Segmento TCP  
Diagramma di stato di TCP  
Rivisitazione della finestra scorrevole 
Protezione contro il wraparound  
Mantenere la pipe piena  
Attivazione della trasmissione  
Sindrome della finestra stupida (Silly Window Syndrome) 
Algoritmo di Nagle 
Ritrasmissione adattiva e calcolo del timeout  
Prestazioni del TCP  
## Controllo della congestione e allocazione delle risorse 
### Problemi di allocazione delle risorse  
Criterio di valutazione con allocazione efficace delle risorse  
Criteri di valutazione con allocazione equa delle risorse  
Disciplina di accodamento FIFO con caduta della coda (tail drop)  
Disciplina di accodamento con accodamento equo  
### Controllo della congestione di TCP  
Finestra di congestione (congestion window)  
### Meccanismi di evitamento della congestione  
Rilevamento precoce casuale (RED)  
Evitare le congestioni basate sulle sorgenti 
## Sicurezza della rete 
### Obiettivi e terminologie  
Terminologie sulla sicurezza  
### Tipi di attacchi 
### Servizi di sicurezza  
### Meccanismi di sicurezza 
### Modelli per la sicurezza di rete  
Modello del canale insicuro (Dolev-Yao)  
### Posizionamento della sicurezza  
Sicurezza nei vari livelli 
### Crittografia  
Crittografia simmetrica  
Principio di Kerckhoff  
Crittoanalisi
### Cifrari di flusso  
RC4 
One-Time Pad  
### Cifrari a blocchi  
AES  
Modalità di funzionamento dei cifrari a blocchi  
### Integrità e autenticazione dei messaggi  
Autenticazione dei messaggi tramite crittografia simmetrica  
Codice di autenticazione del messaggio (MAC)  
Funzioni di hash  
Algoritmo di Sicure Hash (SHA)  
Uso degli Hash Digest nell’autenticazione dei messaggi 
Wired Equivalent Privacy (WEP) 
### Crittografia a chiave pubblica  
RSA 
Altri crittosistemi a chiave pubblica 
Sicurezza degli schemi a chiave pubblica  
### Firme digitali 
Certificati a chiave pubblica  
### Sicurezza delle e-mail  
Modello PGP  
S/MIME (Secure/Multipurpose Internet Mail Extensions) 
Posta Elettronica Certificata (PEC)  
### Gestione delle chiavi e autenticazione delle entità  
Autenticazione del messaggio e dell’entità  
Procedure di autenticazione in X.509
### Diffie-Hellman Key Agreement  
Attacco Man-in-the-middle  
Accordo di chiave sicura da stazione a stazione  
### Sicurezza Web  
Sicurezza a livello di trasporto (protocollo SSL/TLS)  
Architettura TLS 
Protocollo Record  
### Protocollo Handshake 
Fase 1 
Fase 2 
Fase 3  
Fase 4 