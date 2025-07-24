
## **INTRODUZIONE AL DATA STORAGE NEL CLOUD**

Il **Data Storage nel Cloud** rappresenta uno dei pilastri fondamentali dell'architettura cloud moderna. Esistono diverse forme di storage nei sistemi cloud, ciascuna progettata per specifiche esigenze e casi d'uso.

### **Tipologie di Storage Cloud**

**1. Distributed File Systems (Sistemi di File Distribuiti)**
- **Google File System (GFS)**
- **Hadoop Distributed File System (HDFS)**
- Basati su concetti sviluppati nei Network File Systems e Parallel File Systems

**2. SQL Databases (Database Relazionali)**
- Database tradizionali con garanzie ACID
- Limitazioni di scalabilità in ambienti cloud

**3. NoSQL Databases (Database NoSQL)**
- Progettati per superare le limitazioni dei database relazionali
- Maggiore scalabilità e flessibilità

**4. Key-Value Storage Systems (Sistemi di Storage Chiave-Valore)**
- **Google BigTable**
- **Amazon Dynamo**
- Ottimizzati per accesso rapido e scalabilità massiva

### **Obiettivi Comuni dei Sistemi di Storage Cloud**

1. **Massive Scaling on Demand**: Capacità di scalare massivamente su richiesta
2. **High Availability**: Alta disponibilità e resilienza ai guasti
3. **Simplified Application Development**: Semplificazione dello sviluppo e deployment applicativo

---

## **PARTE I: CONCETTI FONDAMENTALI**

### **ATOMICITY (ATOMICITÀ)**

L'**atomicità** è un concetto fondamentale per garantire la consistenza dei dati in sistemi distribuiti.

#### **Atomic Transaction**

Una **transazione atomica** è un'operazione multi-step che deve completarsi senza interruzioni.

**Requisiti per l'atomicità:**

**1. Supporto Hardware:**
- **Test-and-set**: Scrive in una locazione di memoria e ritorna il vecchio contenuto come operazione non-interrompibile
- **Compare-and-swap**: Confronta il contenuto di una locazione di memoria con un valore dato e, solo se i due valori sono uguali, modifica il contenuto con un nuovo valore

**2. Meccanismi di Accesso a Risorse Condivise:**
- **Locks** (Blocchi)
- **Semaphores** (Semafori)  
- **Monitors** (Monitor)

#### **Due Tipi di Atomicità**

**1. All-or-Nothing Atomicity (Atomicità Tutto-o-Niente)**

Strutturata in **due fasi**:

**Fase Pre-commit:**
- Azioni preparatorie che possono essere annullate
- Allocazione risorse, fetch di pagine da storage secondario, allocazione memoria nello stack

**Transizione: Commit-point**

**Fase Post-commit:**
- Azioni irreversibili
- Alterazione dell'unica copia di un oggetto

**Gestione Consistenza:**
- Mantenimento della cronologia dei cambiamenti
- Log delle azioni per gestire fallimenti di sistema e consistenza

**2. Before-or-After Atomicity (Atomicità Prima-o-Dopo)**

Il risultato di ogni operazione di lettura o scrittura è lo stesso come se quella operazione fosse avvenuta completamente prima o completamente dopo qualsiasi altra operazione di lettura o scrittura.

**Esempio pratico:**
Se abbiamo una sequenza di operazioni read e write in una transazione, l'esecuzione concorrente di queste operazioni dovrebbe portare allo stesso risultato dell'esecuzione serializzata della transazione.

### **STORAGE MODELS (MODELLI DI STORAGE)**

#### **Physical Storage**
- Disco locale, disco USB rimovibile, disco accessibile via rete
- Solid state o magnetico

Un **modello di storage** descrive il layout di una struttura dati nel physical storage.

#### **Cell Storage Model**

**Assunzioni sul storage:**
- Celle della stessa dimensione
- Ogni oggetto si adatta esattamente in una cella
- Unità di lettura/scrittura è il settore o blocco

**Riflette l'organizzazione fisica dei media di storage comuni:**
- **Memoria primaria**: Organizzata come array di celle di memoria
- **Storage secondario**: Organizzato in settori o blocchi

**Garanzie:**
- **Read/Write Coherence**: Il risultato di una lettura di una cella M dovrebbe essere lo stesso dell'ultima scrittura su quella cella

#### **Journal Storage Model**

Un modello per memorizzare oggetti come record costituiti da campi multipli.

**Componenti:**
- **Manager**: Gestisce le operazioni
- **Cell Storage**: Storage fisico sottostante

**Caratteristiche:**
- L'intera cronologia di una variabile (non solo il valore corrente) è mantenuta nel cell storage
- Nessun accesso diretto al cell storage
- L'utente può richiedere al journal manager di:
  - Iniziare una nuova azione
  - Leggere il valore di una cella
  - Scrivere il valore di una cella
  - Committare un'azione
  - Abortire un'azione

**Funzionamento del Journal Manager:**

Il journal manager traduce le richieste dell'utente in comandi inviati al cell storage:
- Leggere una cella
- Scrivere una cella
- Allocare una cella
- Deallocare una cella

**Struttura del Log:**
- Contiene la cronologia di tutte le variabili nel cell store
- Ogni aggiornamento è un record aggiunto al log
- **Il log è autoritativo**: Permette di ricostruire il cell store

**Azione All-or-Nothing:**
1. Prima registra l'azione in un log nel journal storage
2. Poi installa il cambiamento nel cell storage sovrascrivendo la versione precedente

**Persistenza:**
- Il log è sempre mantenuto su storage non-volatile
- Il cell storage (considerevolmente più grande) risiede tipicamente su memoria non-volatile, ma può essere mantenuto in memoria per accesso real-time o usando write-through cache

#### **Atomicità e Storage Models**

**Cell Storage:**
- **NON garantisce** all-or-nothing atomicity
- Una volta che il contenuto di una cella è cambiato da un'azione, non c'è modo di abortire l'azione e ripristinare il contenuto originale
- **Garantisce:**
  - Read/write coherence
  - Before-or-after atomicity

**Journal Storage:**
- **Garantisce** all-or-nothing atomicity
- Quando un'azione è eseguita, prima è modificata la cronologia delle versioni (log), poi è modificato il cell store (commit)
- Se una delle due operazioni fallisce, l'azione è scartata (abort)
- All-or-nothing atomicity implica read/write coherence e before-or-after atomicity

### **CONSENSUS PROTOCOLS (PROTOCOLLI DI CONSENSO)**

#### **Definizione di Consenso**

Il **consenso** è il processo di accordarsi su una delle diverse alternative proposte da un numero di agenti.

Nei sistemi distribuiti, gli agenti sono un set di processi che devono raggiungere il consenso su un singolo valore proposto.

#### **Caratteristiche dei Protocolli di Consenso**

**Garanzie:**
- **Safety**: Libertà da inconsistenze
- **NO garanzia di fault-tolerance**: Non possono garantire progresso

**Paxos** è una famiglia di protocolli per raggiungere il consenso basata su un approccio finite state machine, fondamentale per supportare eventual consistency nei datastore NoSQL.

#### **Basic Paxos**

**Obiettivo:** Far raggiungere a un set di processi il consenso su un singolo valore proposto.

**Assunzioni sui Processori:**
1. Operano a velocità arbitrarie
2. Hanno storage stabile e possono rientrare nel protocollo dopo un fallimento
3. Possono inviare messaggi a qualsiasi altro processore

**Assunzioni sulla Rete:**
1. Può perdere, riordinare o duplicare messaggi
2. I messaggi sono inviati asincroni e possono impiegare tempi arbitrariamente lunghi per raggiungere la destinazione

**Nota sui Byzantine Failures:**
I Byzantine failures implicano nessuna restrizione su quali errori possono essere creati:
- Un nodo guasto può generare dati arbitrari
- Può apparire come nodo funzionante a un sottoinsieme di altri nodi

#### **Entità Paxos (Agenti)**

**Client:**
- Emette una richiesta e aspetta una risposta

**Proposer:**
- Sostiene una richiesta da un client
- Convince gli acceptor ad accordarsi sul valore proposto dal client
- Agisce come coordinatore per far avanzare il protocollo in caso di conflitti

**Acceptor:**
- Agisce come "memoria" fault-tolerant del protocollo

**Learner:**
- Agisce come fattore di replicazione del protocollo
- Prende azione una volta che una richiesta è stata concordata

**Leader:**
- Un proposer distinto

#### **Quorum e Proposal**

**Quorum:** Un sottoinsieme di tutti gli acceptor

**Proposal:** Consiste di una coppia: numero di proposta unico e valore proposto (pn, v)
- Proposte multiple possono proporre lo stesso valore v
- Un valore è scelto se una maggioranza semplice di acceptor l'ha accettato

#### **Algoritmo Basic Paxos**

**FASE I: Preparation**

**Proposal Preparation:**
1. Un proposer (leader) invia una proposta (pn = k, v)
2. Invia un messaggio prepare a una maggioranza di acceptor richiedendo:
   - a. che una proposta con pn < k non dovrebbe essere accettata
   - b. il pn < k del numero più alto di proposta già accettato da ogni acceptor

**Proposal Promise:**
- Un acceptor deve ricordare il numero di proposta più alto che ha mai accettato
- Deve ricordare il numero di proposta più alto a cui ha mai risposto
- L'acceptor può accettare una proposta con pn = k se e solo se non ha risposto a una richiesta prepare con pn > k

**FASE II: Accept**

**Accept Request:**
Se la maggioranza degli acceptor risponde, il proposer sceglie il valore v come segue:
- a. Il valore v della proposta con numero più alto selezionata da tutte le risposte
- b. Un valore arbitrario se nessuna proposta è stata emessa

Il proposer invia un messaggio accept request a un quorum di acceptor includendo (pn = k, v).

**Accept:**
Se un acceptor riceve un messaggio accept per una proposta con numero pn = k:
- Deve accettarla se e solo se non ha già promesso di considerare proposte con pn > k
- Se accetta, registra il valore v e invia messaggio accept al proposer e a ogni learner
- Se non accetta, ignora la richiesta

**Proprietà dell'Algoritmo:**
1. Il numero di proposta è unico
2. Due set qualsiasi di acceptor hanno almeno un acceptor in comune
3. Il valore inviato nella Fase II è il valore della proposta con numero più alto di tutte le risposte nella Fase I

---

## **PARTE II: DISTRIBUTED FILE SYSTEMS**

### **GOOGLE FILE SYSTEM (GFS)**

Il **Google File System** è stato progettato per fornire petabyte di storage utilizzando migliaia di sistemi costruiti con componenti commodity economici.

#### **Design Principles**

**Basato su:**
- **Alta affidabilità** contro guasti hardware, errori software di sistema, errori applicativi, errori umani
- **Analisi attenta** delle caratteristiche dei file e dei modelli di accesso

#### **Caratteristiche del Modello di Accesso Cloud**

**File Characteristics:**
- File da pochi GB a centinaia di TB
- **Append piuttosto che random write**
- **Lettura sequenziale**
- Tempo di risposta non è il requisito principale; i dati sono processati in bulk
- **Modello di consistenza rilassato** per semplificare l'implementazione del sistema

#### **Struttura dei File in GFS**

**Chunks:**
- I file GFS sono collezioni di segmenti di dimensione fissa chiamati **chunks**
- **Dimensione chunk: 64MB** (vs 0.5-2 MB dei file system normali)
- Memorizzati su Linux File System
- **Replicati su siti multipli** (3 di default, configurabile)

**Vantaggi della dimensione di chunk grande:**
- Ottimizza le prestazioni per file grandi
- Riduce la quantità di metadata mantenuti dal sistema
- Aumenta la probabilità che operazioni multiple siano dirette allo stesso chunk
- Riduce il numero di richieste per localizzare il chunk
- Mantiene una connessione di rete persistente con il server dove il chunk è localizzato
- Riduce la frammentazione del disco

**Svantaggi:**
- Chunk per file piccoli e ultimo chunk di file grandi sono solo parzialmente riempiti

#### **Architettura del Cluster GFS**

**Master:**
- Controlla un gran numero di chunk server
- Mantiene metadata come:
  - Nomi file
  - Informazioni di controllo accesso
  - Localizzazione di tutte le repliche per ogni chunk di ogni file
  - Stato dei singoli chunk server

**Gestione Localizzazioni Chunk:**
- Memorizzate solo nella struttura di controllo della memoria del master
- Aggiornate all'avvio del sistema o quando un nuovo chunk server si unisce al cluster
- Questa strategia permette al master di avere informazioni aggiornate sulla localizzazione dei chunk

#### **Affidabilità del Sistema**

**Recovery in caso di fallimento:**
- **Operation log**: Mantiene record storico dei cambiamenti metadata
- I cambiamenti sono atomici e non resi visibili ai client finché non sono registrati su repliche multiple su storage persistente
- Il master riproduce l'operation log

**Minimizzazione tempo di recovery:**
- Il master periodicamente fa checkpoint del suo stato
- Al momento del recovery riproduce solo i record del log dopo l'ultimo checkpoint

#### **Accesso ai File GFS**

**File Access:**
- Gestito dall'App e dal Chunk server
- Il master concede un lease a un chunk server (primary)
- Il primary chunk server è responsabile di gestire l'aggiornamento

**File Creation:**
- Gestita dal master

#### **Processo di Scrittura GFS**

1. **Client contatta il master** che assegna un lease a uno dei chunk server (primary) e risponde con ID dei chunk server primary e secondary

2. **Client invia dati a tutti i chunk server**
   - Chunk server memorizzano dati in buffer LRU
   - Chunk server inviano ack al client

3. **Client invia richiesta write al primary**
   - Primary applica mutazione al file

4. **Primary invia write ai secondary**

5. **Ogni secondary invia ack al primary** dopo che le mutazioni sono applicate

6. **Primary fa ack al client**

### **HADOOP DISTRIBUTED FILE SYSTEM (HDFS)**

**Apache Hadoop** è un sistema software per supportare applicazioni distribuite che processano volumi estremamente grandi di dati (applicazioni big data).

#### **Caratteristiche HDFS**

**Implementazione:**
- Sistema di file distribuito scritto in Java
- Portabile, ma non può essere montato direttamente su un sistema operativo esistente
- Non completamente conforme POSIX, ma altamente performante

**Configurazione:**
- **Dimensione blocco: 64-128MB**
- **Replica dati su nodi multipli** (tre repliche di default)
- **Dataset grandi distribuiti su molti nodi**

#### **Architettura HDFS**

**Master/Slave Architecture:**

**NameNode (Master):**
- Gestisce il File System Namespace
- Memorizza metadata
- Controlla accesso ai file
- Registra cambiamenti in un log
- Verifica la vitalità dei DataNode
- Secondary Name node per alta disponibilità

**DataNode (Slave):**
- Operazioni di lettura/scrittura di basso livello

#### **Protocollo di Scrittura HDFS**

**Tre stadi principali:**

**1. Setup della Pipeline**
- Stabilimento della catena di replicazione

**2. Data Streaming e Replication (Write Pipeline Stage)**
- Trasferimento dati e replicazione

**3. Shutdown della Pipeline (Acknowledgement Stage)**
- Conferma completamento operazioni

**Processo dettagliato:**
1. Client richiede al NameNode di scrivere file
2. NameNode verifica permessi e disponibilità
3. NameNode restituisce lista di DataNode per la pipeline
4. Client stabilisce pipeline con primo DataNode
5. Primo DataNode si connette al secondo, secondo al terzo
6. Client inizia streaming dati
7. Ogni DataNode riceve, memorizza e inoltra dati
8. Acknowledgment ritorna indietro nella pipeline
9. Client riceve conferma finale

---

## **PARTE III: NOSQL DATASTORES**

### **INTRODUZIONE AI DATABASE NEL CLOUD**

#### **Applicazioni Cloud e Database**

**Flusso tipico:** `applicazione ↔ database ↔ filesystem`

**Requisiti:**
- **Bassa latenza**
- **Scalabilità**
- **Alta disponibilità**
- **Consistenza**

#### **Database Management System (DBMS)**

**Dovrebbe:**
- Enforcing data integrity
- Gestire accesso dati e controllo concorrenza
- Supportare recovery dopo fallimento

#### **Relational DBMS**

**Caratteristiche:**
- Garantisce proprietà **ACID** (Atomicity, Consistency, Isolation, Durability)
- **Solitamente non scalabile** (in velocità e dimensione)

#### **NoSQL Database Models**

**Quando usarli:**
- La struttura dei dati non richiede un modello relazionale
- La quantità di dati è molto grande (big data)
- Possono non garantire le proprietà ACID

**Tipi:**
- **Key-value stores**
- **Document store databases**
- **Graph databases**

#### **Transaction Processing - NoSQL**

**Document stores e NoSQL databases:**
- Progettati per scalare bene
- Non presentano un single point of failure
- Hanno supporto built-in per decisioni basate su consenso (maggioranza dei voti)
- Supportano partitioning e replicazione come primitive di base

**Approccio "Soft-State":**
- Permette ai dati di essere inconsistenti
- Implementazione delle proprietà ACID è responsabilità dello sviluppatore applicativo
- I dati saranno **"eventually consistent"** in qualche punto futuro invece di enforcing consistenza al momento del commit

**Data Partitioning e Replication:**
- Aumentano disponibilità
- Riducono tempo di risposta  
- Migliorano scalabilità

### **GOOGLE BIGTABLE**

**BigTable** è un sistema di storage distribuito sviluppato da Google per dati strutturati.

#### **Obiettivi BigTable**

- **Wide applicability**: Ampia applicabilità
- **Scalability**: Scalabilità
- **High performance**: Alte prestazioni  
- **High availability**: Alta disponibilità

**Varietà di workload:**
- Da job di batch processing orientati al throughput
- A serving di dati latency-sensitive per utenti finali

#### **Data Model BigTable**

**Definizione:**
BigTable è una **sparse, distributed, persistent multidimensional sorted map**

**Indicizzazione:**
La mappa è indicizzata da row key, column key e timestamp:
```
(row:string, column:string, time:int64) → string
```

**Valore:**
Ogni valore nella mappa è un array non-interpretato di byte (string)

#### **Esempio: Webtable**

```
Row Key: com.google.www
├── Columns (families and keys)
│   ├── contents: (contenuto della pagina web)
│   ├── anchor:cnnsi.com = "CNN"
│   └── language:en = "true"
└── Timestamps: t3, t5, t6 (versioni associate a timestamp)
```

#### **Rows in BigTable**

**Row Key:**
- Stringa arbitraria fino a 64 KB (10-100 byte dimensione tipica)
- **R/W è atomica** indipendentemente dal numero di colonne
- Le righe sono ordinate lessicograficamente per row key

**Tablet:**
- Un range di righe è partizionato dinamicamente in **tablet**
- I tablet sono le unità per load balancing
- Lettura di range di righe brevi è efficiente

**Esempio Webtable:**
- Row key è il reverse hostname: `maps.google.com/index` → `com.google.maps/index`

#### **Columns in BigTable**

**Column Families:**
- Le column key sono raggruppate in set chiamati **column families**
- Tutti i dati memorizzati in una column family sono dello stesso tipo
- I dati di una family possono essere compressi
- Una family deve essere creata prima di memorizzare dati sotto qualsiasi column key

**Caratteristiche:**
- Numero di family dovrebbe essere piccolo (fino a centinaia)
- Le family dovrebbero cambiare raramente
- Numero di colonne in una family è illimitato
- Numero di colonne = numero di family × numero di key per family

**Column Key:** `family:qualifier`

**Esempi:**
- **Family Language**: `Language:ID` → contenuto cella: IT, EN, SW, FR...
- **Family Anchor**: `Anchor:<link_of_referring_site>` → contenuto: link text

**Controllo accesso e accounting** sono fatti a livello di column family.

#### **Timestamps in BigTable**

**Versioning:**
- Ogni cella può contenere versioni multiple degli stessi dati
- Le versioni sono indicizzate da timestamp (interi 64-bit)

**Assegnazione Timestamp:**
- **Da BigTable**: Rappresenta tempo reale in microsecondi
- **Dall'applicazione client**: Applicazioni che devono evitare collisioni devono generare timestamp unici

**Ordinamento:**
- Versioni diverse di una cella sono memorizzate in ordine decrescente di timestamp
- Le versioni più recenti possono essere lette per prime

**Garbage Collection automatico:**
- Solo le ultime n versioni di una cella sono mantenute
- Solo versioni abbastanza nuove sono mantenute (es. ultimi 7 giorni)

#### **Esempio BigTable per Gmail**

```
Row: "user123@gmail.com"
├── info:name = "John Doe"
├── mail:inbox = "list of inbox messages"
├── mail:sent = "list of sent messages"
└── settings:theme = "dark"
```

### **AMAZON DYNAMO**

**Amazon Dynamo** è un key-value store altamente disponibile e scalabile costruito per la piattaforma Amazon.

#### **Casi d'Uso Dynamo**

**Servizi che necessitano solo accesso primary-key:**
- Liste best seller
- Carrelli shopping
- Preferenze clienti
- Gestione sessioni
- Ranking vendite
- Catalogo prodotti

**Perché non RDB:**
- Database relazionali sarebbero inefficienti e limiterebbero scala e disponibilità

#### **Requisiti Affidabilità**

- Dynamo gestisce lo stato di servizi con requisiti di affidabilità molto alti
- **Outage hanno conseguenze finanziarie significative**
- Dynamo bilancia tra disponibilità, consistenza, cost-effectiveness e prestazioni

#### **Caratteristiche Dynamo**

**Eventually Consistent Data Store:**
- Tutti gli aggiornamenti raggiungono tutte le repliche eventualmente

**Tecniche di Replicazione Ottimistiche:**
- Usate per aumentare disponibilità in ambiente soggetto a fallimenti
- I cambiamenti possono propagare alle repliche in background
- Una read/write ha successo se un numero minimo di nodi R/W può completare con successo l'operazione
- Può portare a cambiamenti conflittuali che devono essere rilevati e risolti

#### **Requisiti Amazon Dynamo**

**Query Model:**
- Operazioni semplici di read e write su un data item identificato univocamente da una chiave

**Proprietà ACID:**
- Database ACID hanno scarsa disponibilità
- Dynamo lavora con consistenza debole per migliorare disponibilità

**Efficienza:**
- Gira su hardware commodity e deve soddisfare requisiti di latenza e throughput

**Sicurezza:**
- Nessun requisito di sicurezza specifico; progettato per lavorare internamente ad Amazon

**Scalabilità:**
- A centinaia di storage host

#### **Principi di Design Chiave**

**Data Replication:**
- Coordinamento repliche sincrone porta a scarsa disponibilità
- **Tecniche di replicazione ottimistiche** invece:
  - Permettono propagazione cambiamenti asincrona
  - Introducono problema di inconsistenza e necessità di rilevare/risolvere conflitti

**Risoluzione Conflitti:**

**Quando risolvere:**
- **Always writable**: Conflitti risolti al momento della lettura

**Chi risolve:**
- **Data store**: Limitato
- **Applicazione**: Più complesso ma flessibile

**Altri Principi:**
- **Incremental scalability**: Scalare un storage host alla volta
- **Symmetry**: Ogni nodo ha le stesse responsabilità dei peer
- **Decentralization**: Favorire tecniche peer-to-peer decentralizzate
- **Heterogeneity**: Sfruttare eterogeneità nell'infrastruttura

#### **Architettura di Sistema Dynamo**

**Componenti principali:**
- System interface
- Partitioning Algorithm  
- Replication
- Data Versioning

#### **System Interface**

**Get(key):**
- Localizza le repliche dell'oggetto associate alla chiave
- Ritorna un singolo oggetto o lista di oggetti con versioni conflittuali insieme a un context

**Put(key, context, object):**
- Determina dove le repliche dell'oggetto dovrebbero essere piazzate
- Scrive le repliche su disco
- Il **context** codifica metadata di sistema sull'oggetto, opaco al chiamante

#### **Partitioning Algorithm**

**Per scalare incrementalmente:**
- Partizionamento dinamico dei dati su un set di nodi
- **Consistent hashing** per distribuire il carico su storage host multipli

**Processo:**
- Ogni nodo è assegnato un valore random che rappresenta la sua "posizione" sull'anello
- Ogni data item è assegnato a un nodo hasando la chiave per ottenere la posizione sull'anello

**Sfide:**
- Distribuzione non-uniforme e sbilanciamento del carico

**Soluzione - Virtual Nodes:**
- **Se un nodo diventa non disponibile**: Il carico è disperso uniformemente sui nodi rimanenti
- **Quando un nodo diventa disponibile**: Accetta quantità equivalente di carico dagli altri nodi
- **Il numero di virtual node** può essere deciso basandosi sulla capacità, considerando eterogeneità

#### **Replication**

**Per alta disponibilità e durabilità:**
- Dati replicati su host multipli
- **Ogni data item replicato su N host**
- **Ogni chiave k è assegnata a un nodo coordinatore**:
  - Responsabile della replicazione dei data item nel suo range
  - Oltre a memorizzare localmente, il coordinatore replica su N-1 nodi successori clockwise nell'anello

#### **Data Versioning**

**Eventual Consistency:**
- Permette aggiornamenti propagati a tutte le repliche asincroni
- Una put() può ritornare prima che tutte le repliche siano aggiornate
- Una get() successiva può ritornare oggetto che non ha l'ultimo aggiornamento

**Applicazioni tolleranti:**
- Esempio: "add to cart" non può mai essere rifiutato
- Un cambiamento può essere fatto su versione vecchia/non-aggiornata del carrello
- Versioni divergenti saranno riconciliate

**Vector Clock:**
- Risultato di ogni modifica trattato come nuova versione immutabile
- **Vector clock (node, counter)** usati per marcare versione e catturare causalità

---

## **PARTE IV: OBJECT STORE - AMAZON S3**

### **AMAZON S3 - SIMPLE STORAGE SERVICE**

Amazon S3 rappresenta il servizio di object storage più utilizzato al mondo, progettato per fornire scalabilità, disponibilità e durabilità estreme.

#### **Storage Model S3**

**Struttura dati:**
- Dati memorizzati come "**oggetti**" con nome raggruppati in "**bucket**" con nome
- Mappatura: `bucket + key + version`

#### **Buckets**

**Caratteristiche:**
- **Nomi globalmente unici**
- Ogni utente può creare fino a **100 bucket**
- Devono essere esplicitamente creati (via API) prima di essere usati
- Possono essere listati e cancellati (via API)
- **ACL** per impostare permessi read/write

#### **Objects**

**Caratteristiche:**
- Possono memorizzare qualsiasi stringa di byte da **1 byte a 5 GB**
- **Object key** o key name è unico in un bucket
- **URI path name** per identificare l'oggetto

**Esempio URI:**
```
https://DOC-EXAMPLE-BUCKET.s3.us-west-2.amazonaws.com/photos/puppy.jpg
```

**Operazioni:**
- Memorizzati e recuperati interamente o per range specifici di byte con **PUT & GET**

#### **Gestione Errori Read/Write**

**Responsabilità applicazione:**
- L'applicazione deve gestire fallimenti e errori R/W
- Il client deve riprovare richieste R/W finché non hanno successo
- **MD5 checksum** ritornato nell'Etag usato per verificare integrità

#### **Write Failures**

**Write Retry:**
- Il servizio può fallire quando HTTP PUT è emesso, forzando un retry

**Write Error:**
- S3 calcola MD5 di ogni oggetto scritto
- Ritorna MD5 come parte dell'acknowledgment di scrittura in campo speciale **ETag**
- Client dovrebbero calcolare MD5 degli oggetti che scrivono e confrontare con ETag ritornato
- Se i due valori non corrispondono, oggetto corrotto durante trasmissione o storage
- Oggetto dovrebbe essere ri-scritto

#### **Read Failures**

**Read Retry:**
- Invece di ritornare dati richiesti, S3 può generare errore, forzando retry

**Read Error:**
- Quando S3 ritorna oggetto dati, MD5 dell'oggetto è ritornato nel campo ETag
- Se MD5 non corrisponde al valore originale memorizzato, errore di lettura è avvenuto

#### **Modello di Consistenza S3 per Objects**

**Strong Read-After-Write Consistency:**
- Per richieste **PUT e DELETE** di oggetti
- Sia scritture su nuovi oggetti che PUT che sovrascrivono oggetti esistenti
- **Aggiornamenti a singola chiave sono atomici**

**Concurrent Operations:**
- PUT su chiave esistente da un thread e GET sulla stessa chiave da secondo thread concorrentemente
- Ritornerà o i dati vecchi o nuovi, ma mai dati parziali o corrotti
- **PUT ritorna con successo dopo che tutte le repliche sono aggiornate**

**Strong Consistency per Read Operations:**
- Access control lists (ACL)
- Object Tags  
- Object metadata

#### **Limitazioni S3**

**Object Locking:**
- **Amazon S3 non supporta object locking** per writer concorrenti
- Se due PUT sono fatte simultaneamente sulla stessa chiave, la richiesta con timestamp più recente vince
- I programmatori devono costruire meccanismo di object-locking nell'applicazione

**Aggiornamenti Key-Based:**
- **Non c'è modo di fare aggiornamenti atomici across key**
- Non possibile rendere aggiornamento di una chiave dipendente dall'aggiornamento di un'altra chiave

#### **Modello di Consistenza S3 per Buckets**

**Eventual Consistency Model:**
- Configurazioni bucket hanno modello eventual consistency
- Dopo cancellazione bucket e listing immediato, bucket cancellato potrebbe ancora apparire nella lista
- Abilitare versioning su bucket per prima volta può richiedere tempo per propagazione completa
- **Tempo di attesa suggerito: 15 minuti** prima di eseguire richieste PUT/DELETE su oggetti

#### **Esempi di Comportamento Applicazioni Concorrenti**

**Case 1 - Write then Read:**
```
Thread 1: PUT(key, "new_value")
Thread 2: GET(key) → "new_value" (strong consistency)
```

**Case 2 - Multiple Concurrent Writes:**
```
Thread 1: PUT(key, "value1")  
Thread 2: PUT(key, "value2")
Result: Ultima scrittura vince (last-writer-wins)
```

**Case 3 - Complex Scenario:**
- S3 usa internamente semantica **last-writer-wins**
- Ordine in cui Amazon S3 riceve richieste e ordine acknowledgment non può essere predetto
- **Miglior modo per determinare risultato finale**: Interrogare oggetto dopo operazioni

---

## **CONCLUSIONI E OUTCOME**

### **Concetti Chiave Appresi**

**1. Atomicità e Consistenza:**
- All-or-nothing vs Before-or-after atomicity
- Cell storage vs Journal storage models
- Protocolli di consenso (Paxos)

**2. Distributed File Systems:**
- **GFS**: Ottimizzato per file grandi, append-heavy workloads
- **HDFS**: Java-based, integrato con ecosistema Hadoop

**3. NoSQL Datastores:**
- **BigTable**: Multidimensional sorted map, column families
- **Dynamo**: Eventually consistent, always writable

**4. Object Storage:**
- **S3**: Strong consistency, global durability, simple API

### **Trade-offs Fondamentali**

**CAP Theorem Implications:**
- **Consistency vs Availability**: NoSQL favorisce disponibilità
- **Partition Tolerance**: Tutti i sistemi cloud devono essere partition-tolerant

**Performance vs Consistency:**
- **Strong consistency**: Migliori garanzie, performance potenzialmente inferiori
- **Eventual consistency**: Migliori performance, complessità applicativa

### **Scelta del Sistema di Storage**

**Criteri di Selezione:**
1. **Consistency requirements**: ACID vs eventual consistency
2. **Scale requirements**: Volume dati, throughput, latenza
3. **Access patterns**: Sequential vs random, read vs write heavy
4. **Operational complexity**: Gestione, monitoring, backup

**Best Practices:**
- Comprendere i trade-off specifici di ogni sistema
- Progettare applicazioni tenendo conto del modello di consistenza
- Implementare retry logic e error handling robusti
- Monitorare prestazioni e pattern di accesso

Questi sistemi rappresentano l'evoluzione del data storage per supportare le esigenze massive del cloud computing moderno, ciascuno ottimizzato per specifici casi d'uso e pattern di accesso.
