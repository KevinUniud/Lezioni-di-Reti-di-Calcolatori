## Routing

_Instradamento (routing)_ e _inoltro (forwarding)_ operano su due piani distinti.

**Forwarding (data plane)**

- Funzione: scegliere l’interfaccia di uscita per ogni pacchetto in base alla tabella di inoltro.
- Caratteristiche: semplice, molto veloce, spesso implementato in hardware.
- Input: tabella di forwarding già costruita.
- Output: next hop e uscita fisica.
- Attività: esamina l’indirizzo di destinazione, applica la ricerca della route più specifica, inoltra.

**Routing (control plane)**

- Funzione: costruire e mantenere la tabella di routing, da cui deriva la tabella di forwarding.
- Caratteristiche: complesso, eseguito in background, reagisce a eventi (link su/giù, nuovi router, variazioni di costo).
- Mantiene informazioni aggiuntive: costi, distanze, metriche, politiche.
- Risultato: una tabella di routing che viene tradotta in tabella di forwarding.

**Relazione tra i due piani**

Il control plane decide *dove* i pacchetti dovrebbero passare; il data plane *li invia*.

L’analogia con la guida: prima si decide il percorso (routing), poi si gestiscono sterzo e acceleratore (forwarding).

**Tabelle**

- Tabella di routing: usata dal control plane, contiene costi, distanze, percorsi.
- Tabella di forwarding: usata dal data plane, contiene next hop, interfacce, prefissi.

### Algoritmi di routing come problema sui grafi

La rete viene modellata come un grafo pesato: router = nodi, collegamenti = archi, costi = pesi.

Il problema è trovare il percorso di costo minimo.

Le politiche possono differire tra sistemi autonomi (AS).

- **Intra-domain (dentro un AS)**: reti più piccole, algoritmi più semplici e ottimizzabili.
- **Inter-domain (tra AS)**: maggiore attenzione alla raggiungibilità e alle politiche, non all’ottimo.

### Classi di protocolli

#### 1) Distance Vector

Esempio: RIP.
- Ogni router conosce i costi verso i vicini.
- Scambia periodicamente un vettore di distanze con i vicini.
- Usa l’algoritmo di Bellman-Ford per aggiornare le rotte.
- Vulnerabile al *count to infinity*: rotte che si aggiornano erroneamente e fanno crescere i costi senza fine.
- Soluzioni parziali:
	- infinito finito (es. 16 in RIP)
	- split horizon[^1]
	- split horizon with poison reverse[^2]
- Adatto solo per reti piccole.

#### 2) Link State

Esempi: OSPF, IS-IS.
- Ogni nodo crea una visione globale della rete.
- Invia LSP (Link State Packets) tramite flooding affidabile: SEQNO, TTL, informazioni sui vicini.
- Ogni router calcola autonomamente i percorsi minimi tramite Dijkstra.
- Richiede più memoria e CPU, ma converge molto più velocemente rispetto al distance vector.
- Permette metriche multiple, diverse tabelle per diversi servizi.

#### 3) Path Vector

Esempio: BGP (inter-domain).
- Simile al distance vector, ma il vettore contiene l’intero percorso (sequenza di AS).
- Serve a imporre politiche e prevenire loop.
- Usato esclusivamente nel routing tra AS.

### Distance Vector in dettaglio

Ogni nodo mantiene un vettore dei costi verso tutti gli altri nodi. Quando riceve un vettore da un vicino R, aggiorna:

```
c’ = V(D) + cost(R)

Se la rotta migliore nota passa da R → aggiorna costo
Altrimenti, se c’ è migliore del costo corrente → sostituisce la rotta
```

Il problema principale è il *count to infinity*: quando un link fallisce, i nodi possono auto-convincersi che esista un percorso alternativo, aumentando il costo all’infinito.

_RIP_ (_routing information protocol_) limita l’infinito a 16.

### Link State in dettaglio

Ogni router:
1. Misura i costi verso i vicini.
2. Crea un LSP[^3].
3. Inonda l’AS con il suo LSP tramite flooding affidabile.
4. Mantiene per ogni router solo l’LSP più recente.
5. Esegue Dijkstra per ricostruire la tabella di routing.

#### Algoritmo di Dijkstra

Costruisce iterativamente un insieme di nodi confermati, aggiornando i costi verso i rimanenti e scegliendo sempre il prossimo nodo col costo minimo.

### OSPF

- Protocollo intra-domain più usato.
- Implementa link state.
- Divide l’AS in aree e backbone; i border router le collegano.
- Supporta metriche multiple e diverse tabelle di routing per servizi diversi.
- Utilizza autenticazione per prevenire annunci falsi.
- Mantiene LSP e calcola rotte tramite Dijkstra.
- Possibile integrazione con tecniche di traffic shaping e flow detection.

OSPF preferisce l’affidabilità e il controllo rispetto alla semplicità di RIP, ed è quindi dominante nelle reti moderne.

**Sintesi finale**
- Forwarding: operativo, veloce, hardware, applica una tabella.
- Routing: decisionale, complesso, software, costruisce la tabella.
- Distance Vector: semplice ma lento nel convergere, vulnerabile ai loop.
- Link State: complesso ma rapido e affidabile.
- Path Vector: usato per politiche inter-domain.

## Instradamento inter-dominio

L’**instradamento inter-dominio** organizza Internet in **sistemi autonomi (AS)**, ciascuno controllato da un’unica entità amministrativa che impone regole interne coerenti (politiche, protocolli, metriche).

Un AS è una sottorete di Internet composta da router e reti locali.

Gli AS dei provider possono contenere a loro volta le reti dei clienti.

Il GARR è un esempio di AS backbone nazionale dedicato a ricerca e istruzione, con PoP[^4] distribuiti sul territorio e collegamenti verso reti internazionali (GÉANT, NaMeX, Cogent).

La topologia reale può contenere decine di router per singolo PoP e migliaia di host.

### Scalabilità e gerarchia dell’instradamento

Internet non può usare protocolli interni come RIP o OSPF per l’inter-dominio:
- non esiste una metrica comune accettabile da tutti gli AS;
- le decisioni dipendono da **politiche** (evitare certi provider, preferire link economici o affidabili);
- le dimensioni della rete richiedono un’aggregazione gerarchica delle informazioni.

Si separano quindi due livelli:

1. **Instradamento intra-dominio**: dentro un AS, i router sono peer e usano un IGP (RIP, OSPF, IS-IS).
2. **Instradamento inter-dominio**: tra AS, serve un protocollo standard (oggi BGP).

### Evoluzione: da EGP a BGP

Il vecchio Internet aveva una struttura ad albero, con una dorsale centrale e reti regionali subordinate.

L’EGP funzionava solo in infrastrutture gerarchiche.

Con la crescita di Internet si è passati a una topologia a grafo, con molti provider backbone connessi tra loro e provider minori collegati a uno o più backbone.

È nato quindi **BGP (Border Gateway Protocol)**, capace di gestire reti arbitrariamente interconnesse.

### Tipologie di AS

**AS stub**

- Connesso a _un solo AS esterno_.
- Usa quel provider per tutto il traffico in uscita e riceve traffico solo per le proprie reti.
- _Non_ fornisce transito.
- Esempio tipico: una piccola azienda che ha un unico ISP.

**AS multi-homed**

- Connesso a _due o più AS esterni_, ma con politiche BGP che _rifiutano di instradare traffico tra quei provider_.
- Usa più collegamenti esterni per ridondanza o bilanciamento.
- _Non_ diventa un AS di transito perché non permette a un provider di raggiungere l’altro attraverso di sé.
- Esempio: un’azienda di medie dimensioni con due ISP per affidabilità, ma che non vuole trasportare traffico tra loro.

**AS transit**

- Connesso a _molti AS esterni_ e _accetta di instradare traffico che non ha origine né destinazione interna_.
- Fornisce un servizio di _transito IP_, tipico dei carrier e backbone provider globali.
- È parte della “core” di Internet perché permette ad AS altrimenti separati di comunicare.    


> [!importart]
> Un AS diventa _transit_ solo quando le sue politiche BGP _permettono transito di terzi_; non basta avere molte connessioni.

> [!info]
> Non fornire transito significa che non instrada traffico tra due AS esterni diversi attraverso di sé.
> 
> Nel dettaglio:
> - Un AS stub o multi-homed instrada solo:
>	- traffico in entrata verso le proprie reti;
>	- traffico in uscita dalle proprie reti;
>	- traffico interno.
> - Non instrada invece traffico del tipo:
>	- un pacchetto che arriva da AS X e deve andare verso AS Y. 

### Ruolo di BGP

BGP _non ottimizza_ i percorsi: garantisce _raggiungibilità_ e _assenza di loop_. Gli obiettivi principali:
- **Scalabilità** delle tabelle di routing globali;
- **Autonomia** dei domini (ogni AS applica proprie politiche);
- **Gestione della fiducia**: un AS può decidere di ignorare annunci ritenuti indesiderabili.

#### Router speaker e gateway

Ogni AS ha:
- **Speaker BGP**: router che scambiano annunci BGP con i peer;
- **Gateway di frontiera**: router di ingresso/uscita del traffico (non necessariamente speaker).

#### Due versioni di BGP

- **eBGP**: tra AS diversi;
- **iBGP**: all’interno dello stesso AS per distribuire gli annunci ricevuti dagli speaker.

### Funzionamento generale

In un AS tutti i router eseguono:
- un IGP per il routing interno;
- **iBGP** per propagare i percorsi appresi tramite eBGP.

Gli annunci BGP trasportano _interi percorsi_ sotto forma di lista di AS. 

> [!example]
> AS 100 → AS 300 → AS 400 → rete 7.7.0.0/16

Questa rappresentazione:
- consente il controllo delle politiche (accettare o rifiutare un percorso);
- previene i loop: se un AS vede sé stesso nella lista, scarta l’annuncio.

Gli speaker BGP _non inoltrano direttamente il traffico_ a meno che siano anche gateway; forniscono le informazioni per costruire la tabella di forwarding del dominio.

### Policy routing e prevenzione dei loop

Un router BGP scarta un percorso se:
- il proprio AS compare nella lista (loop inter-AS);
- una policy locale lo proibisce (filtri per evitare certi provider o usare solo link preferiti).

BGP è un protocollo _a vettore di percorso_: non è distance vector e non è link state.

> [!example]
> Un cliente P annuncia al proprio provider (AS 2) le sue reti (es. 128.96/16 e 192.4.153/24).
Il provider le aggiunge alla sua tabella e le inoltra alla backbone, aggiungendo il proprio AS alla lista.
> 
> Gli altri provider decidono se accettare o filtrare i percorsi in base alle politiche interne.

### Problemi e requisiti di BGP

- Gli _ASN_ devono essere univoci; sono numeri a 32 bit assegnati da IANA e dai registri regionali.
- BGP deve gestire centinaia di migliaia di prefissi e migliaia di AS.
- La convergenza può essere lenta.
- Le politiche possono rendere il routing non ottimale ma coerente con le strategie economiche dei provider.

In sintesi, l’instradamento inter-dominio si basa su AS autonomi, collegati tramite BGP, che scambiano percorsi completi e applicano politiche locali per garantire raggiungibilità e assenza di cicli in un’infrastruttura globale non gerarchica.

## IP di nuova generazione (IPv6)

IPv6 è il successore di IPv4.

La differenza fondamentale è la dimensione dell’indirizzo: 128 bit invece di 32, per risolvere l’esaurimento degli indirizzi IPv4.

Lo spazio di indirizzamento è dell’ordine di 3×10³⁸ indirizzi.

Oltre all'ampliamento degli indirizzi, IPv6 introduce diverse funzionalità aggiuntive:

- sicurezza e autenticazione obbligatorie
- autoconfigurazione
- multicast migliorato
- frammentazione end-to-end
- supporto alla mobilità
- estendibilità mediante intestazioni aggiuntive

### Indirizzamento IPv6

- Notazione: x:x:x:x:x:x:x:x (otto blocchi esadecimali da 16 bit).
- Sequenze di zeri consecutive possono essere compresse con “::”.
- Compatibilità con IPv4 tramite notazione mista (::128.42.1.87).
- Indirizzamento simile a CIDR, senza classi.
- Assegnazione gerarchica basata su provider o regioni geografiche.

### Formato del pacchetto IPv6

Header base di 40 byte, più eventuali header di estensione (ordine fisso).
Campi principali:

- Version
- TrafficClass: priorità del datagramma
- FlowLabel: identificazione di un flusso
- PayloadLength: fino a 64 KB
- NextHeader: indica l’header successivo
- HopLimit: equivalente del TTL

Alcune informazioni presenti in IPv4 sono state spostate nelle intestazioni di estensione, come la frammentazione.

### Traffic Class (priorità)

Serve come suggerimento ai router in caso di congestione.
Valori significativi:

- 0: traffico generico
- 1: background
- 2: senza attesa (es. SMTP)
- 4: con attesa (es. HTTP, FTP)
- 6: interattivo (es. SSH)
- 7: controllo (OSPF, RIP, SNMP)
- 8–15: traffico realtime non interrompibile

### Flow Label
Valore a 20 bit assegnato dalla sorgente.

Identifica i datagrammi appartenenti allo stesso flusso per fornire un trattamento coerente (routing simile, risorse riservate, servizi real-time).

Usato soprattutto all’interno di un AS.

### Transizione da IPv4 a IPv6

IPv4 e IPv6 non sono compatibili. La migrazione richiede soluzioni intermedie:
1. **Dual Stack**
   Host e router implementano entrambi i protocolli. È la tecnica più comune. Le applicazioni devono gestire indirizzi IPv4 e IPv6.

2. **Tunneling**
   Pacchetti IPv6 incapsulati in pacchetti IPv4 per attraversare reti IPv4. Il router di uscita del tunnel estrae il pacchetto IPv6. Introduce overhead e perde alcune funzionalità IPv6 nella parte IPv4.

3. **Traduzione dell’intestazione**
   Conversione tra header IPv6 e IPv4 (simile al NAT, ma tra protocolli diversi). Richiede dispositivi di livello 3 dedicati.

## Internet Multicast

### Concetto di multicast

Multicast permette di inviare un messaggio a un gruppo di host senza conoscerli singolarmente.

Due modelli:
- **SSM (one-to-many)**: una sorgente specifica trasmette a un gruppo.
- **ASM (many-to-many)**: qualsiasi sorgente può trasmettere al gruppo.

Multicast evita duplicazioni inutili di traffico che si avrebbero usando solo unicast.

### Multicast IP

- Gli host inviano un solo pacchetto all’indirizzo multicast; i router duplicano i pacchetti quando necessario.
- Il range IPv4 multicast è la _classe D_: 224.0.0.0 – 239.255.255.255.
  - 224.0.0.0/24: uso locale
  - 224.0.1.0/24: gruppi globali IANA (es. NTP)
  - Altri gruppi allocati dinamicamente (SAP/SDP)

### Gestione dei gruppi

Gli host si uniscono o abbandonano gruppi usando:
- **IGMP** per IPv4
- **MLD** per IPv6

Il router locale coordina l’iscrizione e la consegna del traffico.

### Forwarding multicast

Un router mantiene:
- **Tabelle unicast**
- **Tabelle multicast**, che definiscono gli _alberi di distribuzione_ del gruppo.

L’insieme dei percorsi per un gruppo multicast forma un _multicast distribution tree_.

### Famiglie di alberi per il routing multicast

1. **Source-based trees**
	- Un albero per ogni sorgente.
	- Ogni router usa il proprio albero dei percorsi più brevi verso la sorgente.

2. **Group-shared trees**
	- Un solo albero condiviso per ogni gruppo.
	- Un router centrale (rendezvous/core) gestisce il traffico.

### DVMRP (Distance-Vector Multicast Routing Protocol)

Primo protocollo multicast (1988).
Basato su distance vector e sulla tecnica _flood and prune_.

#### Flooding

- Usa _Reverse Path Flooding_: un router inoltra un pacchetto solo se proviene dall’interfaccia corrispondente al suo percorso più breve verso la sorgente.
- Ottimizzazione: _Reverse Path Broadcast_, che assegna un router genitore per ogni LAN così da evitare duplicati.

#### Pruning

- Le LAN senza membri del gruppo inviano messaggi di prune.
- I rami inutili vengono tagliati.
- Se un host si iscrive successivamente, si usa il meccanismo di _grafting_ per reinserire il ramo.

Funziona bene su reti piccole (intra-AS).

È inefficiente su scala globale per l’eccessivo flooding.

### PIM (Protocol Independent Multicast)

Non dipende dal protocollo di routing unicast.

Due modalità:
1. **PIM-DM (dense mode)**:
	- Adatto quando il gruppo è denso.
	- Simile a DVMRP (flood+prune).
	- Usa alberi basati sulla sorgente.
2. **PIM-SM (sparse mode)**:
	- Adatto quando pochi host partecipano (tipico su Internet).
	- Costruisce un _albero condiviso_ con un _rendezvous point (RP)_.
	- I router inviano messaggi di join verso l’RP.
	- I pacchetti della sorgente vengono incapsulati e inviati al RP, che li distribuisce lungo l’albero.

Possibile ottimizzazione: costruire un albero specifico per la sorgente (source-specific tree).

### MBONE (Multicast Backbone)

Poiché pochi router supportano il multicast, storicamente si sono usati tunnel unicast tra router multicast-aware per attraversare zone non multicast.

Questa dorsale logica è chiamata _MBONE_.
- I pacchetti multicast vengono incapsulati in pacchetti unicast.
- Oggi MBONE è obsoleto e destinato a essere sostituito da IPv6 con PIMv6.
- Multicast rimane usato soprattutto in reti locali o domini gestiti (es. IPTV, reti aziendali, hotel, ospedali).

[^1]: Un router che impara una rotta da un’interfaccia **non la reinvia sulla stessa interfaccia**.

[^2]: Come split horizon con l'aggiunta che il router **annuncia la rotta sull’interfaccia da cui l’ha appresa**, però la dichiara con **costo infinito**.

[^3]: È il messaggio con cui ogni router **annuncia lo stato dei propri link** (vicini, metriche, costi).

[^4]: Point of Presence.
