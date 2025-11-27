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

**Attacchi all'integrità**

* **Modifica**: intercettazione e alterazione dei messaggi.
* **Mascheramento/Spoofing**: l'attaccante si finge un altro soggetto, compreso il man‑in‑the‑middle.
* **Replaying**: riutilizzo di un messaggio autentico catturato in precedenza.
* **Ripudio**: il mittente o il destinatario nega in seguito di aver inviato o ricevuto un messaggio.

Questi attacchi sono particolarmente critici, soprattutto in contesti finanziari. Sono necessari autenticazione e scambio sicuro delle chiavi.

Il non‑ripudio richiede strumenti come firme digitali, notarizzazione o PEC.

**Attacchi alla disponibilità**

* **DoS/DDoS**: sovraccaricano un servizio con richieste false o fanno perdere richieste/risposte, causando rallentamenti o blocchi.
  Un esempio tipico è l'attacco SYN, in cui il server alloca risorse per connessioni che non verranno completate.
  Le contromisure includono meccanismi che ritardano l'allocazione delle risorse (es. SCTP Cookie) e filtri contro richieste sospette.

**Attacchi passivi e attivi**

* **Passivi**: raccolgono informazioni senza modificare il sistema; difficili da rilevare, prevenzione prioritaria.
* **Attivi**: alterano dati o operazioni; più visibili ma non completamente prevenibili.
  Le contromisure includono verifiche di integrità e richieste di reinvio quando i dati non corrispondono a quanto atteso.

## Servizi di sicurezza

I servizi di sicurezza informatica principali sono:

* **Riservatezza dei dati:** protezione dei dati e metadati da accessi non autorizzati.
* **Integrità dei dati:** assicurare che i dati ricevuti siano esattamente quelli inviati da un'entità autorizzata.
* **Autenticazione:** verifica dell'identità dell'entità comunicante, distinguendo tra autenticazione dei dati (origine dei dati) e autenticazione del peer (identità dell'entità durante la connessione).
* **Non-repudiazione:** impedire che mittente o destinatario possano negare l'invio o la ricezione di un messaggio, con prove specifiche per origine e destinazione.
* **Controllo degli accessi:** prevenzione dell'uso non autorizzato di risorse (lettura, scrittura, modifica, esecuzione).

La **disponibilità** è considerata una proprietà che garantisce che le risorse siano accessibili per gli usi previsti, non un servizio a sé stante.

## Meccanismi di sicurezza

I meccanismi di sicurezza sono funzioni di base progettate per rilevare, prevenire o recuperare un sistema da attacchi informatici.

Non esiste un meccanismo unico per tutti i servizi; spesso replicano funzioni associate ai documenti fisici, come firme, protezione dalla divulgazione o manomissione, autenticazione notarile, ecc.

Principali meccanismi:

* **Encipherment:** nasconde i dati, garantendo riservatezza; include crittografia e steganografia.
* **Integrità dei dati:** rileva modifiche tramite checksum, hash, ecc.
* **Firma digitale:** garantisce l'origine del messaggio.
* **Scambio di autenticazione:** le parti confermano reciprocamente la propria identità.
* **Padding del traffico:** inserisce dati fasulli per evitare l'analisi del traffico.
* **Controllo dell'instradamento:** seleziona percorsi sicuri per evitare intercettazioni o zone problematiche.
* **Notarizzazione:** terza parte fidata verifica le comunicazioni per evitare ripudio.
* **Controllo degli accessi:** verifica i diritti di accesso alle risorse.

La combinazione di più meccanismi permette di implementare i servizi di sicurezza, ma non tutti i meccanismi sono adatti a ogni servizio.

## Modelli per la sicurezza di rete

Un modello è un'astrazione che permette di concentrarsi sugli aspetti rilevanti, ignorando dettagli non essenziali.

I due modelli principali sono: il modello del canale insicuro, che riguarda la protezione dei dati trasmessi su una rete non fidata, e il modello dell'accesso alla rete, relativo alle minacce al sistema informativo.

### Modello del canale insicuro (Dolev-Yao)

Il modello del canale insicuro, noto come modello di Dolev‑Yao, considera un mittente e un destinatario che comunicano attraverso un canale completamente controllabile da un avversario.

L'opponent può leggere, modificare, duplicare e bloccare i messaggi.

Per questo motivo il messaggio deve essere trasformato prima della trasmissione tramite meccanismi di sicurezza (cifratura, firme digitali), basati su informazioni segrete condivise in precedenza. Il modello non specifica come avvenga lo scambio iniziale delle chiavi.

Una terza parte onesta può intervenire per facilitare la comunicazione o gestire le chiavi. 

L'uso del modello richiede la progettazione degli algoritmi di protezione, la generazione e distribuzione delle chiavi e la definizione di un protocollo che consenta ai partecipanti di applicare correttamente le trasformazioni di sicurezza.

Il modello è molto diffuso nello studio e nella progettazione della sicurezza su canali insicuri, ma non è l'unico esistente.

Nella pratica occorre scegliere come implementare concretamente il canale e a quale livello della pila OSI o TCP/IP applicare i servizi di sicurezza.

## Posizionamento della sicurezza

Esistono due modi principali di posizionare la sicurezza nelle comunicazioni:

**1. Sicurezza del collegamento (link security)**

Ogni collegamento della rete applica autonomamente la trasformazione di sicurezza.

I dati vengono cifrati e decifrati a ogni hop. 

Richiede molte chiavi e dispositivi ed è limitata ai livelli più bassi dello stack (fisico e datalink).

Protegge i flussi di traffico, ma tra un router e l'altro i dati transitano in chiaro.

**2. Sicurezza end-to-end**

La cifratura avviene direttamente tra sorgente e destinazione.

Protegge i contenuti lungo tutto il percorso, ma lascia in chiaro le intestazioni necessarie all'instradamento.

Si applica dai livelli superiori (rete, trasporto, applicazione).

Le due soluzioni sono complementari: la prima protegge i flussi, la seconda i contenuti.

### Sicurezza nei vari livelli

Spostando la sicurezza verso livelli più alti:
* diminuiscono le informazioni protette,
* aumenta la qualità della protezione,
* cresce il numero di chiavi necessarie,
* aumenta l'onere per programmatori e sistemi.

La quantità di chiavi cresce in $O(n^2)$.

**Livello fisico**

Protezione del mezzo tramite sensori e dispositivi anti-manomissione.

Costosa, valida solo su collegamenti controllati.

**Livello collegamento dati**

Protezione implementata nelle schede di rete (WEP, WPA).

Copre dati dell'applicazione, trasporto e intestazione di rete, ma non l'intestazione del frame.

Vulnerabile quando il traffico passa dai router.

**Livello rete**

Protezione a livello di sistema, trasparente per gli sviluppatori (IPsec, VPN).

Copre dati applicativi e intestazione del trasporto, non l'intestazione IP.

**Livello trasporto/presentazione**

Sicurezza end-to-end tramite librerie/API (TLS/SSL).

Copre i dati applicativi e parte dell'intestazione, non porte e indirizzi.

**Livello applicazione**

Crittografia integrata nelle applicazioni (PGP, S/MIME, SSH, Signal, MTProto, OAuth2). 

Protegge solo il payload.

Specifica per ogni servizio.

Le cifrature si applicano “a cipolla”: ogni livello aggiunge la propria protezione al pacchetto del livello superiore.

A valle, un messaggio può risultare cifrato più volte, una per ciascun livello che implementa sicurezza.

## Crittografia

La crittografia è un meccanismo di sicurezza fondamentale che consente di proteggere le comunicazioni trasformando il testo in chiaro in testo cifrato tramite un algoritmo e una chiave.

Esistono due tipi principali: simmetrica (a chiave privata) e asimmetrica (a chiave pubblica), utilizzati per scopi diversi.

Terminologia essenziale:
* _testo in chiaro_: messaggio originale;
* _testo cifrato_: messaggio codificato;
* _cifrario_: algoritmo di cifratura;
* _chiave_: informazione segreta usata per cifrare/decifrare;
* _cifrare/decifrare_: trasformazione da chiaro a cifrato e viceversa;
* _crittografia_: studio dei metodi di cifratura;
* _crittoanalisi_: studio dei metodi per rompere un cifrario senza conoscere la chiave;
* _crittologia_: insieme di crittografia e crittoanalisi.

### Crittografia simmetrica

Mittente e destinatario condividono la stessa chiave segreta.

È la forma storicamente più antica e ancora ampiamente utilizzata.

Per essere sicura richiede:
1. un algoritmo robusto, tale che senza la chiave non sia possibile risalire al testo in chiaro;
2. due funzioni matematiche di cifratura e decifratura coerenti, tali che decifrare ciò che è stato cifrato con la stessa chiave restituisca sempre il messaggio originale.

### Principio di Kerckhoff

La sicurezza deve dipendere solo dalla segretezza della chiave, non dall'algoritmo, che si assume essere pubblico.

Un sistema che si basa sulla segretezza del codice sorgente è considerato inaffidabile.

Per questo i cifrari moderni (AES, RC4, ecc.) sono pubblici e analizzabili da tutti.

### Crittoanalisi

Studia come recuperare il testo in chiaro o la chiave.

Gli attacchi si distinguono in base alle informazioni disponibili:
* *solo testo cifrato*: l'attaccante vede solo il testo cifrato;
* *known plaintext*: conosce coppie testo in chiaro–testo cifrato;
* *chosen plaintext*: può far cifrare testi a sua scelta;
* *chosen ciphertext*: può ottenere la decifratura di testi cifrati scelti;
* *chosen text*: combina le due precedenti.

I cifrari robusti devono resistere almeno agli attacchi known-plaintext.

**Brute force e sicurezza**

Un attacco di forza bruta prova tutte le chiavi possibili.

È sempre possibile in teoria, ma impraticabile in presenza di chiavi sufficientemente lunghe, perché il numero di combinazioni cresce esponenzialmente.

La sicurezza può essere:
* *incondizionata*: impossibile da violare indipendentemente dalla potenza computazionale;
* *computazionale*: violazione troppo costosa rispetto al valore dell'informazione.

La crittoanalisi può ridurre lo spazio delle chiavi, ma riduzioni minime (es. da 2¹²⁸ a 2¹²⁶) non rendono il cifrario realmente più debole.

## Cifrari di flusso

Un _cifrario di flusso_ elabora il messaggio bit o byte per volta combinandolo tramite XOR con un _keystream_ pseudo-casuale generato a partire da una chiave (seed).

Cifratura e decifratura coincidono, il che rende il metodo veloce ed efficiente. La sicurezza dipende da:
* keystream non riutilizzato, altrimenti da $(C \oplus C' = M \oplus M')$ si recuperano informazioni sui messaggi;
* periodo molto lungo senza ripetizioni;
* flusso statisticamente casuale;
* seed sufficientemente grande.

### RC4

Cifrario a flusso molto diffuso (SSL/TLS, WEP, WPA).

Caratteristiche:
* chiave variabile fino a 2048 bit;
* stato interno costituito da una permutazione di 256 byte;
* generazione veloce del keystream;
* periodo estremamente lungo.
  Funzionamento: inizializza un array S con i valori 0–255, lo rimescola usando la chiave e poi, a ogni passo, scambia elementi dell'array per produrre il byte del keystream da XORare con il messaggio.

### One-Time Pad

Cifrario a flusso ideale basato su un _keystream realmente casuale_, lungo quanto il messaggio e usato una sola volta.

Garantisce sicurezza incondizionata: ogni ciphertext può derivare da qualunque plaintext con una chiave opportuna, quindi non esistono informazioni utili per risalire al testo originale.

Limiti: la chiave deve essere lunga quanto il messaggio, deve essere distribuita in modo sicuro e non può essere riutilizzata.

## Cifrari a blocchi

Elaborano il messaggio a blocchi di dimensione fissa (oggi tipicamente 128 bit).

Un blocco grande riduce l'efficacia degli attacchi statistici.

Non si può usare una tabella di sostituzione completa (richiederebbe memoria enorme), quindi si costruiscono trasformazioni complesse tramite strutture iterative.  

I cifrari classici usano spesso la struttura di Feistel (DES), quelli moderni altre strutture (AES).

### AES

Standard USA dal 2001, scelto per sostituire DES.

Requisiti: cifrario simmetrico, blocchi da 128 bit, chiavi da 128/192/256 bit, più veloce e sicuro del 3DES, specifiche pubbliche.  

È ampiamente usato (Blu-ray, DVB, satellitare).

Implementabile in hardware molto piccolo.  

Cifrario iterativo: opera su una matrice 4×4 di byte con sostituzioni (S-box), permutazioni, mix delle colonne e XOR con la round key per 9/11/13 round.  

La sicurezza è elevata: gli unici attacchi pratici sono a canale laterale. È approvato per dati SECRET e TOP SECRET.

### Modalità di funzionamento dei cifrari a blocchi

Quando il messaggio è più lungo del blocco, si usano modalità operative.

**ECB (Electronic CodeBook)**  

Cripta ogni blocco in modo indipendente.

Blocchi uguali producono ciphertext uguali.

Non protegge la struttura del messaggio.

È usato solo per pochi blocchi isolati.

**CBC (Cipher Block Chaining)**  

Ogni blocco plaintext è XORato con il ciphertext precedente; serve un IV iniziale.

Blocchi dipendenti tra loro: una modifica si propaga nei blocchi successivi.

Usato per grandi quantità di dati.

L'IV deve essere gestito con cura.

**OFB (Output FeedBack)** 

Genera un flusso pseudo-casuale indipendente dal messaggio; ciphertext = plaintext XOR keystream.

Gli errori non si propagano.

Non deve mai riutilizzare la stessa coppia chiave+IV.

**CTR (Counter Mode)**  

Cripta valori di un contatore; ciphertext = plaintext XOR E_K(contatore).

Permette parallelizzazione, precomputazione ed è ideale per reti ad alta velocità e file system crittografati.

Mai riutilizzare la stessa combinazione chiave+contatore.

## Integrità e autenticazione dei messaggi

Proteggono i dati da modifiche e confermano l'identità del mittente. I meccanismi principali sono:
1. cifratura del messaggio
2. MAC (Message Authentication Code)
3. funzioni di hash

Gli attacchi alla sicurezza includono: divulgazione, analisi del traffico, mascheramento, modifiche a contenuto/sequenza/tempistica, ripudio della fonte o del destinatario.

**Modello generale di autenticazione**

Si usa un autenticatore (al mittente) e un verificatore (al destinatario).

Al messaggio vengono aggiunti dati che permettono di verificarne integrità e autenticità.

### Autenticazione tramite crittografia simmetrica

Con una chiave condivisa:
* il destinatario sa che il mittente è autentico;
* eventuali modifiche sono rilevabili tramite ridondanza;
* resta possibile il ripudio, perché entrambe le parti conoscono la stessa chiave.

### MAC (Message Authentication Code)

Il MAC è un piccolo blocco calcolato da:
$MAC = C_K(M)$
dipende dal messaggio e da una chiave segreta.

Proprietà:
* garantisce integrità e autenticità (chiave condivisa)
* non è una firma digitale
* non richiede cifratura del MAC
* funzione molti-a-uno, ma difficile trovare collisioni utili

### Funzioni di hash

Funzione pubblica che produce:
$h = H(M)$
con questi requisiti: output fisso, facile da calcolare, non invertibile, resistente a pre-immagine, seconda pre-immagine e collisione.

Usi: rilevamento modifiche, costruzione di MAC e firme digitali.

**SHA**

SHA-1 produce hash a 160 bit ma è indebolito.

SHA-256/384/512 (FIPS 180-2) offrono sicurezza più elevata.

### HMAC

MAC basato su funzione di hash:
$HMAC_K = Hash[(K+ ⊕ opad) || Hash[(K+ ⊕ ipad) || M]]$

Sicurezza collegata alla robustezza della funzione hash base.

Resistente ad attacchi di forza bruta e attacchi di compleanno.

### Esempio applicativo: WEP

WEP (1999) usa una chiave simmetrica condivisa tra host e access point.

Il payload + checksum (ICV) sono cifrati con RC4 usando un IV a 24 bit inviato in chiaro.

**Gravi vulnerabilità WEP**

* IV troppo corto: ripetizioni frequenti (attacco del compleanno)
* RC4 debole per alcuni IV
* ripetizione della chiave per lungo tempo
  Con IV ripetuti: C1 ⊕ C2 = P1 ⊕ P2 → analisi e recupero dei plaintext.

### Correzioni (WPA/WPA2)

802.11i (2004) introduce:
* handshake a 4 vie (PMK → PTK)
* TKIP: usa RC4 con chiavi temporanee (WPA)
* CCMP: usa AES in CTR/CBC-MAC (WPA2)

**Ancora vulnerabile**

KRACK (2017): attacco di reinstallazione della chiave che sfrutta la ritrasmissione dei messaggi dell'handshake.

In certe configurazioni permette decifrare e talvolta falsificare pacchetti.
