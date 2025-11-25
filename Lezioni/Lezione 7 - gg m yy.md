## Obiettivi e terminologie

La sicurezza informatica si basa sulla triade CIA:

* **Riservatezza**: protezione delle informazioni da accessi non autorizzati. Il traffico può essere intercettato da strumenti come Wireshark o da dispositivi intermedi compromessi.
* **Integrità**: solo soggetti autorizzati possono modificare i dati. Anche dispositivi di rete potrebbero modificare volutamente i pacchetti.
* **Disponibilità**: i dati devono essere accessibili alle entità autorizzate quando necessario.

I tre obiettivi sono indipendenti e combinabili.

### Terminologie sulla sicurezza

**Terminologie principali**

* **Minaccia**: possibilità teorica che si verifichi una violazione degli obiettivi di sicurezza.
* **Attacco**: azione concreta che compromette la sicurezza.
* **Rilevazione**: individuazione di accessi o attività anomale, ad esempio login da dispositivo sconosciuto.
* **Servizio di sicurezza**: funzione che migliora la sicurezza contrastando attacchi tramite meccanismi dedicati.
* **Meccanismo di sicurezza**: componente elementare che previene, rileva o recupera da un attacco.

**Concetti collegati**

* Un *asset* può essere minacciato e una *vulnerabilità* può permettere un attacco.
* Le contromisure includono autenticazione (password, fingerprint, smartcard, controlli biometrici, verifiche via mail).
* La *recovery* consiste nel ripristino dopo un attacco, spesso tramite rollback basato su backup.

## Tipi di attacchi

**Attacchi alla riservatezza**

* **Snooping**: accesso non autorizzato a dati o metadati; include sniffing del traffico e consultazione di tabulati. La cifratura riduce il rischio.
* **Analisi del traffico**: ricavo di informazioni indirette osservando volumi, tempi e destinatari, anche se i dati sono cifrati.

**Attacchi all’integrità**

* **Modifica**: intercettazione e alterazione dei messaggi.
* **Mascheramento/Spoofing**: l’attaccante si finge un altro soggetto, compreso il man‑in‑the‑middle.
* **Replaying**: riutilizzo di un messaggio autentico catturato in precedenza.
* **Ripudio**: il mittente o il destinatario nega in seguito di aver inviato o ricevuto un messaggio.

Questi attacchi sono particolarmente critici, soprattutto in contesti finanziari. Sono necessari autenticazione e scambio sicuro delle chiavi. Il non‑ripudio richiede strumenti come firme digitali, notarizzazione o PEC.

**Attacchi alla disponibilità**

* **DoS/DDoS**: sovraccaricano un servizio con richieste false o fanno perdere richieste/risposte, causando rallentamenti o blocchi.
  Un esempio tipico è l’attacco SYN, in cui il server alloca risorse per connessioni che non verranno completate.
  Le contromisure includono meccanismi che ritardano l’allocazione delle risorse (es. SCTP Cookie) e filtri contro richieste sospette.

**Attacchi passivi e attivi**

* **Passivi**: raccolgono informazioni senza modificare il sistema; difficili da rilevare, prevenzione prioritaria.
* **Attivi**: alterano dati o operazioni; più visibili ma non completamente prevenibili.
  Le contromisure includono verifiche di integrità e richieste di reinvio quando i dati non corrispondono a quanto atteso.

## Servizi di sicurezza

I servizi di sicurezza informatica principali sono:

* **Riservatezza dei dati:** protezione dei dati e metadati da accessi non autorizzati.
* **Integrità dei dati:** assicurare che i dati ricevuti siano esattamente quelli inviati da un’entità autorizzata.
* **Autenticazione:** verifica dell’identità dell’entità comunicante, distinguendo tra autenticazione dei dati (origine dei dati) e autenticazione del peer (identità dell’entità durante la connessione).
* **Non-repudiazione:** impedire che mittente o destinatario possano negare l’invio o la ricezione di un messaggio, con prove specifiche per origine e destinazione.
* **Controllo degli accessi:** prevenzione dell’uso non autorizzato di risorse (lettura, scrittura, modifica, esecuzione).

La **disponibilità** è considerata una proprietà che garantisce che le risorse siano accessibili per gli usi previsti, non un servizio a sé stante.

## Meccanismi di sicurezza

I meccanismi di sicurezza sono funzioni di base progettate per rilevare, prevenire o recuperare un sistema da attacchi informatici. Non esiste un meccanismo unico per tutti i servizi; spesso replicano funzioni associate ai documenti fisici, come firme, protezione dalla divulgazione o manomissione, autenticazione notarile, ecc.

Principali meccanismi:

* **Encipherment:** nasconde i dati, garantendo riservatezza; include crittografia e steganografia.
* **Integrità dei dati:** rileva modifiche tramite checksum, hash, ecc.
* **Firma digitale:** garantisce l’origine del messaggio.
* **Scambio di autenticazione:** le parti confermano reciprocamente la propria identità.
* **Padding del traffico:** inserisce dati fasulli per evitare l’analisi del traffico.
* **Controllo dell’instradamento:** seleziona percorsi sicuri per evitare intercettazioni o zone problematiche.
* **Notarizzazione:** terza parte fidata verifica le comunicazioni per evitare ripudio.
* **Controllo degli accessi:** verifica i diritti di accesso alle risorse.

La combinazione di più meccanismi permette di implementare i servizi di sicurezza, ma non tutti i meccanismi sono adatti a ogni servizio.


## Modelli per la sicurezza di rete

Un modello è un’astrazione che permette di concentrarsi sugli aspetti rilevanti, ignorando dettagli non essenziali. I due modelli principali sono: il modello del canale insicuro, che riguarda la protezione dei dati trasmessi su una rete non fidata, e il modello dell’accesso alla rete, relativo alle minacce al sistema informativo.

### Modello del canale insicuro (Dolev-Yao)

Il modello del canale insicuro, noto come modello di Dolev‑Yao, considera un mittente e un destinatario che comunicano attraverso un canale completamente controllabile da un avversario. L’opponent può leggere, modificare, duplicare e bloccare i messaggi. Per questo motivo il messaggio deve essere trasformato prima della trasmissione tramite meccanismi di sicurezza (cifratura, firme digitali), basati su informazioni segrete condivise in precedenza. Il modello non specifica come avvenga lo scambio iniziale delle chiavi.

Una terza parte onesta può intervenire per facilitare la comunicazione o gestire le chiavi. L’uso del modello richiede la progettazione degli algoritmi di protezione, la generazione e distribuzione delle chiavi e la definizione di un protocollo che consenta ai partecipanti di applicare correttamente le trasformazioni di sicurezza.

Il modello è molto diffuso nello studio e nella progettazione della sicurezza su canali insicuri, ma non è l’unico esistente. Nella pratica occorre scegliere come implementare concretamente il canale e a quale livello della pila OSI o TCP/IP applicare i servizi di sicurezza.

## Posizionamento della sicurezza

* **Sicurezza del collegamento (link security):** la cifratura avviene tra singoli collegamenti; richiede molti dispositivi con chiavi accoppiate; protegge dati “in transito” tra router ma non end-to-end.
* **Sicurezza end-to-end:** la cifratura avviene tra sorgente e destinazione finale; richiede dispositivi alle estremità con chiavi condivise; protegge i contenuti ma lascia intestazioni in chiaro.
* Entrambe possono essere combinate: end-to-end per proteggere contenuti e autenticazione, collegamento per proteggere flussi di traffico.

### Sicurezza nei vari livelli

* **Fase generale:** più alto è il livello, meno informazioni protette ma maggiore sicurezza e complessità; più chiavi necessarie $O(n^2)$.
* **Fisico:** sensori per intrusioni fisiche; costoso, difficile da bypassare fisicamente.
* **Collegamento dati:** protezione tramite schede di rete (es. WEP, WPA); vulnerabile ai router e agli sniffing di MAC address.
* **Rete:** sicurezza implementata nel kernel (es. IPsec, VPN); trasparente agli sviluppatori, copre dati e intestazioni di trasporto.
* **Presentazione/Trasporto:** sicurezza end-to-end tramite librerie/API (es. TLS/SSL); protegge dati applicativi e intestazioni, ma non indirizzi/porte.
* **Applicazione:** cifratura specifica delle applicazioni (es. Telegram, SSH, PGP, Signal); protegge solo il payload, header in chiaro.

Le cifrature si applicano a strati (“stile cipolla”), con ogni livello che cifra i dati del livello superiore, generando una protezione multilivello dei dati.

## Crittografia

### Crittografia simmetrica

### Principio di Kerckhoff

### Crittoanalisi

## Cifrari di flusso

### RC4

## Cifrari a blocchi

### AES

### Modalità di funzionamento dei cifrari a blocchi

## Integrità e autenticazione dei messaggi

### Autenticazione dei messaggi tramite crittografia simmetrica

### Codice di autenticazione del messaggio (MAC)

### Funzioni di hash

### Algoritmo di Sicure Hash (SHA)

### Uso degli Hash Digest nell’autenticazione dei messaggi

### Wired Equivalent Privacy (WEP)