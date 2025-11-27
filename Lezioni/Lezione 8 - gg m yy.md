## Crittografia a chiave pubblica

La crittografia simmetrica usa una sola chiave condivisa: garantisce riservatezza, ma non impedisce falso attributo del messaggio né offre non ripudio. 

Il punto di svolta è la crittografia a chiave pubblica (Diffie–Hellman, 1976), basata su due chiavi distinte.

### Crittografia a chiave pubblica (PKC)

Usa due chiavi matematicamente correlate:
* **chiave pubblica (PU)**: può essere distribuita, serve per cifrare o verificare firme
* **chiave privata (PR)**: segreta, serve per decifrare o firmare

Proprietà fondamentali:
* da PU non è possibile ricavare PR
* cifrare e decifrare con la chiave corretta è facile
* invertire l'operazione senza PR è computazionalmente difficile
* basata su funzioni unidirezionali (facili in un senso, difficili nell'altro)

Applicazioni principali:
1. **Cifratura** per garantire segretezza
2. **Firme digitali** per autenticazione e non ripudio
3. **Scambio di chiavi** (creazione di chiavi di sessione)

### RSA

Algoritmo del 1977 (Rivest, Shamir, Adleman).

Si basa sulla difficoltà di _fattorizzare_ $n = p \cdot q$, con $p$ e $q$ primi grandi.

**Toziente di Eulero:**
* se $n = p \cdot q → φ(n) = (p − 1)(q − 1)$

**Generazione delle chiavi:**
1. scegliere grandi primi $p$, $q$
2. calcolare $n = p\cdot q$
3. calcolare $φ(n) = (p − 1)(q − 1)$
4. scegliere $e$ con $gcd(e, φ(n)) = 1$ ($e$ co-primo di $\varphi$)
5. calcolare $d$ tale che $e·d ≡ 1 mod φ(n)$
6. chiave pubblica = $(e, n)$
7. chiave privata = $(d, n)$

**Cifratura:**
$C = M^e \mod n$

**Decifratura:**
$M = C^d \mod n$

RSA funziona grazie al teorema di Eulero e al fatto che $e$ e $d$ sono inversi modulo $φ(n)$.

> [!example]
>Generazione e uso di chiavi RSA:
> 
> 1. Si scelgono i primi $p = 17$ e $q = 11$.
> 2. Si calcola $n = p·q = 187$.
> 3. Si calcola $φ(n) = (p − 1)(q − 1) = 160$.
> 4. Si sceglie $e$ tale che $gcd(e, 160) = 1$, ad esempio $e = 7$.
> 5. Si trova $d$ con $d·e ≡ 1 \mod 160$: $d = 23$.
> 
> Chiave pubblica: $(e, n) = (7, 187)$.
> Chiave privata: $(d, n) = (23, 187)$.
> 
> Cifratura del messaggio $M = 88$:
> $C = 88^7 \mod 187 = 11$.
> 
> Decifratura:
> $M = 11^{23} \mod 187 = 88$.

### Altri crittosistemi

* **ElGamal**: basato sul logaritmo discreto modulo un primo
* **Curve ellittiche (ECC)**: più sicure a parità di dimensione, chiavi molto più corte. Usate in ECDSA, ECDH, ecc.

### Sicurezza

La forza degli schemi a chiave pubblica deriva da:
* dimensioni molto grandi delle chiavi (>1024 bit)
* difficoltà di fattorizzazione (RSA) o logaritmo discreto (ElGamal, ECC)

Possibili attacchi:
* forza bruta (impraticabile)
* attacchi matematici (fattorizzazione)
* attacchi temporali
* attacchi a scelta del testo cifrato

La PKC è più lenta della crittografia simmetrica, quindi spesso viene usata solo per scambiare chiavi di sessione.

## Firme digitali

Garantiscono autenticità del mittente, integrità del messaggio, data/ora della firma e non ripudio.

Devono dipendere dal messaggio e da dati unici del firmatario, essere facili da generare e verificare, ma impossibili da falsificare.

Le firme digitali non garantiscono riservatezza: se serve cifratura, si firma prima e si cifra dopo, anche se nella pratica si firma solo il digest perché i sistemi asimmetrici sono lenti.

**Schema di firma**
Nella firma asimmetrica:
* si firma con la chiave privata
* si verifica con la chiave pubblica
  La firma del digest applica una funzione hash al messaggio e firma solo l'hash.

### Certificati a chiave pubblica

Risolvono il problema della distribuzione sicura delle chiavi pubbliche: associano in modo verificabile identità e chiave pubblica.

Sono firmati da un'autorità di certificazione (CA).

La CA crea i certificati ma non li conserva; ogni utente memorizza localmente il proprio certificato.

Le CA firmano certificati contenenti identità, chiave pubblica, periodo di validità e altri campi.

Per evitare chiavi compromesse si usano timestamp, CRL (liste di revoca) e OCSP (controllo online dello stato del certificato).

**X.509**

Standard che definisce struttura, contenuti e protocolli per certificati e autenticazione con chiavi pubbliche.

I certificati includono: versione, numero di serie, algoritmo di firma, emittente, validità, soggetto, chiave pubblica del soggetto, estensioni, firma della CA.

Le estensioni definiscono politiche d'uso e livelli di controllo dell'identità (classi 1–3), nomi alternativi, vincoli sul percorso di certificazione, ecc.

**Ottenere e revocare un certificato**

L'utente genera la coppia di chiavi, invia la chiave pubblica alla CA autenticandosi, e riceve il certificato firmato.

Può essere revocato prima della scadenza in caso di compromissione.

Le CA pubblicano CRL o rispondono tramite OCSP.

**Gerarchia delle CA**

Serve quando gli utenti non condividono direttamente la stessa CA. Le CA formano una catena di fiducia: ogni CA firma certificati dei propri clienti e certificati delle CA subordinate. 

Una catena di certificati permette di verificare chiavi pubbliche attraverso più livelli.

Se una CA superiore scade o viene compromessa, anche i suoi certificati figli vengono invalidati.

**CA radice (root)**

Sono autocertificate e devono essere distribuite in modo sicuro (tipicamente nei sistemi operativi o nei browser).

La fiducia nel sistema implica fiducia nelle CA radice.

## Sicurezza delle e-mail

**Problema della posta elettronica**

Le e-mail standard non sono sicure: possono essere lette, modificate, intercettate e falsificate.

Servono riservatezza, autenticazione, integrità e non ripudio.

L'invio usa SMTP tramite nodi MTA; la lettura usa POP/IMAP tramite MAA.

### Modello PGP

**PGP (Pretty Good Privacy)**

Standard di fatto per la sicurezza delle e-mail. Offre cifratura, firma digitale o entrambe.

Struttura delle chiavi:
* portachiavi pubblici: chiavi pubbliche degli altri utenti
* portachiavi privati: chiave privata dell'utente, protetta da passphrase

Autenticazione: si calcola l'hash del messaggio, lo si firma con la chiave privata, il destinatario verifica con la chiave pubblica.

Confidenzialità: cifratura simmetrica con chiave di sessione casuale; la chiave di sessione è cifrata con la chiave pubblica del destinatario.

PGP firma, comprime, cifra e codifica in radix-64 per compatibilità e-mail.

Gestione delle chiavi: modello distribuito “web of trust”.

Gli utenti firmano le chiavi degli altri, creando catene di fiducia non gerarchiche. Le chiavi possono essere revocate.

### S/MIME (Secure/Multipurpose Internet Mail Extensions)

Standard ufficiale (IETF) per la posta elettronica sicura. 

asato su certificati X.509 e simile a PGP nel funzionamento.

I client e-mail lo supportano nativamente e verificano automaticamente le firme (file smime.p7s).

### Posta Elettronica Certificata (PEC)

Estensione italiana basata su S/MIME.

Aggiunge il non ripudio del destinatario e un timestamp certificato dal server.

Il mittente riceve una ricevuta firmata alla consegna, con valore legale se i server sono accreditati.

## Gestione delle chiavi e autenticazione delle entità

L'autenticazione delle entità serve a provare l'identità di una parte (richiedente) verso un verificatore.

Si basa spesso sulla crittografia a chiave pubblica e richiede che le chiavi pubbliche usate siano corrette e non revocate.

Per evitare attacchi, molti protocolli impiegano nonces o timestamp.

### Autenticazione del messaggio e dell'entità

L'autenticazione dei messaggi riguarda singoli messaggi; quella delle entità riguarda l'intera sessione e porta alla creazione di un segreto condiviso.

Con la crittografia asimmetrica l'autenticazione può avvenire tramite protocolli challenge-response basati su nonces, sia unidirezionali sia bidirezionali.

### Procedure di autenticazione in X.509

X.509 definisce tre procedure di autenticazione, tutte basate su firme digitali:
- **One-Way**: A invia a B un messaggio firmato contenente timestamp, nonce, identità, eventuale chiave di sessione cifrata per B. B verifica la firma e recupera la chiave di sessione.
- **Two-Way**: B risponde con un messaggio firmato che include nonce di A, timestamp e proprio nonce. Stabilisce l'identità di entrambe le parti, ma è vulnerabile ai replay se i timestamp non sono verificati e gli orologi non sono sincronizzati.
- **Three-Way**: tre messaggi basati su nonces senza usare timestamp. Il terzo messaggio contiene la copia firmata del nonce di B, eliminando la dipendenza dalla sincronizzazione degli orologi e contrastando i replay.

Le procedure a tre vie riducono i problemi legati ai timestamp e rendono più difficile agli attaccanti riutilizzare vecchi messaggi o generare risposte valide senza le chiavi corrette.

## Diffie-Hellman Key Agreement

Il protocollo Diffie-Hellman permette a due parti di generare una chiave di sessione comune senza ricorrere a un centro di distribuzione.

Non serve per scambiare messaggi, ma solo per creare un segreto condiviso.

Si basa sull'esponenziazione modulo un grande primo e sulla difficoltà del logaritmo discreto.

I partecipanti concordano su un primo $(p)$ e su una radice primitiva $(g)$.

Ognuno sceglie una chiave segreta $(x)$ e pubblica $(R = g^x \bmod p)$.

La chiave condivisa è $(K = g^{xy} \bmod p)$, che può essere calcolata da entrambe le parti ma non da un attaccante, il quale dovrebbe risolvere un logaritmo discreto.

> [!example]
> Due utenti A e B eseguono Diffie–Hellman con parametri pubblici p = 353 e a = 3.  
> A sceglie la chiave segreta 97, B sceglie 233.
> 
> Chiavi pubbliche:
> - A calcola 3⁹⁷ mod 353 = 40
> - B calcola 3²³³ mod 353 = 248
>
> Chiave di sessione condivisa:
> - A calcola 248⁹⁷ mod 353 = 160
> - B calcola 40²³³ mod 353 = 160
>
> Entrambi ottengono la stessa chiave finale: 160.

### Attacco Man-in-the-middle

Diffie-Hellman semplice è vulnerabile a un attacco man-in-the-middle: senza autenticazione, un attaccante può instaurare due scambi DH separati con A e B, leggendo e modificando i messaggi.

Il problema non è la cifratura dei valori DH, ma l'assenza di garanzia sull'identità di chi invia le chiavi pubbliche.

### Accordo di chiave sicura da stazione a stazione

La soluzione è autenticare le chiavi DH con firme digitali o certificati, come nello schema “station-to-station”. Ogni parte firma la propria chiave pubblica DH e la invia, impedendo a un attaccante di sostituirla.

Approcci analoghi sono adottati in SSL/TLS e IPSec.

## Sicurezza Web

Il Web è vulnerabile a minacce come compromissione di integrità, riservatezza e autenticazione, quindi necessita di sicurezza aggiuntiva.

### Sicurezza a livello di trasporto (protocollo SSL/TLS)

 La protezione a livello di trasporto è fornita da SSL/TLS, che assicura riservatezza e integrità sul traffico TCP end-to-end.
 
 TLS è composto da due livelli: il Protocollo Record, che trasforma i dati dell'applicazione in sicurezza, e i protocolli Handshake, Change Cipher Spec e Alert, che gestiscono autenticazione e chiavi segrete.

### Architettura TLS

TLS distingue tra sessione (parametri crittografici condivisi tra client e server) e connessione (link transitorio associato a una sessione).

Lo stato di sessione include identificatore, certificati X.509, compressione, cifratura e Master Secret.

Lo stato di connessione contiene valori casuali, chiavi MAC, IV e numeri di sequenza.

Le cipher suite definiscono algoritmi per scambio chiavi, cifratura e hash (es.: SSL_DHE_RSA_WITH_DES_CBC_SHA).

### Protocollo Record

Il Protocollo Record utilizza la chiave condivisa per cifratura simmetrica dei dati e MAC per garantire integrità, includendo il numero di sequenza per prevenire attacchi replay.

TLS 1.3 ha eliminato algoritmi più deboli per migliorare la sicurezza.

## Protocollo Handshake

Il protocollo Handshake di TLS/SSL serve a stabilire il _Master Secret_ e i parametri di sessione, autenticare server e client, negoziare algoritmi di cifratura, MAC e chiavi crittografiche.

**Fasi principali:**
1. **Stabilire capacità di sicurezza:** client invia versione TLS, numeri casuali, ID sessione e cipher suite supportate; il server risponde scegliendo versione e cipher preferita.
2. **Autenticazione server e scambio chiavi:** il server si autentica e il client ottiene la chiave pubblica del server (es.: RSA o Diffie-Hellman).
3. **Autenticazione client e scambio chiavi:** il client invia parametri opzionali; insieme definiscono il **pre-master secret**. Le modalità includono RSA, Diffie-Hellman anonimo, fisso o effimero, quest'ultimo più sicuro.
4. **Attivazione cifratura:** si decide la suite di cifratura, si genera il Master Secret a partire dal pre-master secret e dai numeri casuali di client/server, e si derivano chiavi di sessione e MAC. Da questo momento tutti i dati sono cifrati e autenticati.

Ogni connessione derivata dalla sessione usa il Master Secret per generare il _key material_ e i segreti specifici della connessione, assicurando confidenzialità e integrità.