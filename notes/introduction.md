

## **INTRODUZIONE STORICA AL CLOUD COMPUTING**

### **Una Curiosità Iniziale**
Prima di immergerci nella definizione tecnica, riflettiamo su una domanda fondamentale: **quando pensate sia nato il concetto di cloud computing?**

Le opzioni potrebbero essere:
- 2000
- 1980  
- 1970
- 1960

La risposta potrebbe sorprendervi!

### **Le Radici Storiche del Cloud Computing**

#### **1961: John McCarthy - Stanford University**
Il **vero pioniere** del concetto di cloud computing:

*"Se i computer del tipo che ho sostenuto diventano i computer del futuro, allora l'informatica potrebbe un giorno essere organizzata come un'utilità pubblica proprio come il sistema telefonico è un'utilità pubblica... L'utilità informatica potrebbe diventare la base di una nuova e importante industria."*

**John McCarthy** - l'inventore del linguaggio LISP - aveva già intuito nel **1961** quello che oggi chiamiamo cloud computing!

#### **1969: Leonard Kleinrock - Progetto ARPANET**
Il "nonno di Internet" aveva una visione straordinariamente lungimirante:

*"Al momento, le reti di computer sono ancora nella loro infanzia, ma man mano che cresceranno e diventeranno sofisticate, probabilmente vedremo la diffusione di 'utilità informatiche', che, come le attuali utilità elettriche e telefoniche, serviranno case e uffici individuali in tutto il paese."*

#### **2005/2006: Jeff Bezos - Amazon**
La realizzazione pratica della visione:
- Permettere agli utenti di **affittare storage dati e tempo di server** da Amazon come un'utilità
- Lancio di **Elastic Compute Cloud (EC2)**
- Lancio di **Simple Storage Service (S3)**

### **Utility Computing e Cloud Computing**

**Oggi i servizi di computing sono facilmente disponibili on-demand**, proprio come altri servizi di pubblica utilità.

**Gli utenti (consumatori):**
- Pagano i provider solo quando accedono ai servizi
- Non hanno investimenti o difficoltà nel costruire e mantenere infrastrutture IT complesse
- Accedono ai servizi basandosi sui loro requisiti senza riguardo a dove i servizi sono ospitati

**Questo modello** è stato definito **utility computing** o, dal 2007, **cloud computing**.

---

## **DEFINIZIONE UFFICIALE NIST DEL CLOUD COMPUTING**

### **NIST SP 800-145: La Definizione Standard**

**Il Cloud Computing è un modello per abilitare accesso di rete ubiquo, conveniente, on-demand a un pool condiviso di risorse computazionali configurabili (es. reti, server, storage, applicazioni e servizi) che possono essere rapidamente fornite e rilasciate con sforzo di gestione minimo o interazione del service provider.**

**Componenti del modello cloud:**
- **Cinque caratteristiche essenziali**
- **Tre modelli di servizio**  
- **Quattro modelli di deployment**

### **Discussione Critica: Ubiquità e Localizzazione**

**Ubiquitous** significa che server e dati possono essere localizzati ovunque e accessibili da ovunque.

**Domande critiche da considerare:**
- È importante o no dove server e dati sono localizzati? Dovremmo avere controllo su questo?
- La localizzazione del cliente rispetto alla localizzazione del server/dati può impattare le prestazioni percepite/qualità dell'esperienza?

---

## **LE CINQUE CARATTERISTICHE ESSENZIALI**

### **1. ON-DEMAND SELF-SERVICE**

**Non è richiesto intervento umano** per il provisioning delle risorse.

**Interfacce disponibili:**
- **Web interface**: OpenStack, AWS, Google Cloud Console
- **Dedicated shell**: AWS CLI, Azure CLI
- **Programming API**: AWS SDK per Java, Python, .NET, Ruby, Go, ecc.

**Benefici:**
- Velocità di provisioning
- Riduzione errori umani
- Disponibilità 24/7
- Automazione completa

### **2. BROAD NETWORK ACCESS**

Si riferisce alla **possibilità di accedere alle risorse cloud da Internet** utilizzando dispositivi diversi.

**Tecnologie abilitanti:**
- Reti broadband
- Tecnologie Internet standard
- Protocolli di rete aperti

**Considerazioni critiche:**
- **Problemi di prestazioni e sicurezza** si trovano nella rete e ai margini (edge)
- Dipendenza dalla connettività Internet
- Latenza di rete come fattore limitante

### **3. RESOURCE POOLING**

**[NIST]** Le risorse computazionali sono raggruppate per servire **consumatori multipli** usando un **modello multi-tenant**.

**Caratteristiche:**
- Risorse fisiche e virtuali diverse assegnate e riassegnate dinamicamente secondo la domanda del consumatore
- **Location independence**: Nessun controllo o conoscenza della localizzazione esatta
- Possibilità di specificare localizzazione a livello di astrazione più alto (paese, stato, datacenter)

#### **Multi-Tenant Model - Approfondimento**

**Multi-tenancy (delle applicazioni):**
- **Utenti multipli (tenant)** accedono alla stessa logica applicativa simultaneamente
- **Ogni tenant ha la propria vista** dell'applicazione come istanza dedicata
- **Inconsapevole di altri tenant**

**Proprietà delle applicazioni multitenant:**
- **Usage Isolation**: Prestazioni e disponibilità
- **Data Security**: Sicurezza e isolamento dati
- **Recovery from failures**: Ripristino da guasti
- **Application Upgrades**: Aggiornamenti applicativi
- **Scalability**: Scalabilità
- **Metered Usage**: Uso misurato
- **Data Tier Isolation**: Isolamento del livello dati

**Applicazione al Cloud:**
```
Hardware Fisico
├── VM1 (App1) ─ Tenant A
├── VM2 (App1) ─ Tenant A  
├── VM3 (App1) ─ Tenant B
└── VM4 (App2) ─ Tenant C
```

### **4. RAPID ELASTICITY**

#### **Definizione di Elasticità**

**L'elasticità è il grado in cui un sistema è capace di adattarsi ai cambiamenti del carico di lavoro** tramite provisioning e deprovisioning di risorse in modo automatico, tale che in ogni momento le risorse disponibili corrispondano alla domanda corrente il più possibile.

**Relazione con Scalabilità:**
- **Horizontal Scaling**: Scaling out (aggiungere istanze) e scaling in (rimuovere istanze)
- **Vertical Scaling**: Scaling up (aumentare risorse per istanza) e scaling down (diminuire risorse)

#### **Casi d'Uso della Rapid Elasticity**

**Esempi pratici:**
- **Sito e-commerce** durante la settimana del Black Friday
- **Sito web di notizie** in caso di breaking news
- **Eventi su larga scala**: Olimpiadi, campionati mondiali
- **Applicazioni di intrattenimento**: Giochi online, streaming di musica e film

#### **Implementazione Pratica - Esempio AWS**

**Scenario IaaS con AWS:**

**Scaling Manuale (Non Rapid):**
- Creazione manuale di VM con servizio EC2
- Aggiunta manuale di VM (istanze EC2)
- Possibilità di scalare ma non rapidamente seguendo la domanda

**Scaling Automatico (Rapid):**
```
Automatismo richiesto:
1. Monitorare uso CPU delle VM (continuamente)
2. Se uso CPU > 70% per 1 min → aggiungi 1 VM
3. Con più VM, decisioni basate su uso CPU medio
4. AWS Auto-scaling + Elastic Load Balancing
```

### **5. MEASURED SERVICE**

#### **Modello di Pricing Cloud**

**I provider cloud addebitano ai clienti per:**
- **Tempo**: Ore/minuti/secondi di utilizzo VM o altre risorse virtualizzate
- **Storage**: MB di storage utilizzato / MB di trasferimento I/O
- **Network**: MB/GB trasferiti sulla rete
- **Altri parametri specifici**

**Esempio Pricing AWS:**
```
t2.small:  1 CPU, 2 GB RAM → $0.023/ora
m4.large:  2 CPU, 8 GB RAM → $0.1/ora
```

#### **Service Level Agreements (SLA)**

**I provider cloud devono garantire SLA specifici:**
- **99.9% uptime** 
- **Bandwidth garantita**
- **Quota CPU specifica**

**Esempio SLA AWS:**
```
Monthly Uptime Percentage    Service Credit Percentage
< 99.95% ma ≥ 99.0%         10%
```

**Requisiti di misurazione/monitoraggio per:**
- **Billing appropriato**
- **Gestione SLA**

#### **Monitoraggio per i Consumatori**

**I consumatori devono monitorare l'uso delle loro risorse per:**

**Task di gestione automatici:**
- **Scaling**: Soglie di autoscaling
- **Controllo costi**: Alert di spesa
- **Garanzia SLA applicativi**: Throughput, tempo di risposta
- **Aumento resilienza**

#### **Resilienza - Approfondimento**

**Resilienza:** La capacità di fornire e mantenere un livello accettabile di servizio di fronte a guasti e sfide all'operazione normale.

**Esempi pratici (concetti che vedremo in dettaglio):**

**AWS Elastic Load Balancer:**
- Se un nodo è down, viene automaticamente escluso dal pool

**AWS Autoscaling:**
- Configurazione del numero minimo di server sempre up nonostante i guasti

**Container Orchestration (Kubernetes, Swarm):**
- Definizione del numero desiderato di container sempre up e running nonostante i guasti

---

## **I TRE MODELLI DI SERVIZIO**

### **Panoramica dei Service Models**

I modelli di servizio definiscono **il livello di controllo del consumatore** e la **separazione delle responsabilità** tra consumatore e provider.

**Tendenza generale:**
- **Movimento da IaaS a SaaS**:
  - Il controllo del consumatore sulle risorse **diminuisce**
  - L'expertise necessaria per gestire il servizio cloud **diminuisce**  
  - La facilità d'uso **aumenta**

### **Infrastructure as a Service (IaaS)**

**Controllo del Consumatore:**
- **Sistemi operativi**
- **Storage** 
- **Applicazioni deployate**
- **Controllo limitato** di alcuni componenti di networking

**Responsabilità del Cliente:**
- Gestione completa del sistema operativo
- Patching e manutenzione
- Configurazione applicazioni
- Gestione sicurezza a livello OS

**Esempi:** Amazon EC2, Google Compute Engine, Microsoft Azure VMs

### **Platform as a Service (PaaS)**

**Controllo del Consumatore:**
- **Applicazioni deployate**
- **Possibili configurazioni** dell'ambiente di hosting applicazioni

**Responsabilità del Provider:**
- Sistema operativo sottostante
- Runtime environment
- Middleware
- Servizi di base

**Esempi:** Google App Engine, Microsoft Azure App Service, Heroku

### **Software as a Service (SaaS)**

**Controllo del Consumatore:**
- **Solo configurazioni applicative** specifiche dell'utente

**Responsabilità del Provider:**
- Intera infrastruttura
- Piattaforma
- Applicazione
- Manutenzione e aggiornamenti

**Esempi:** Gmail, Office 365, Salesforce, Dropbox

### **Separazione delle Responsabilità di Sicurezza**

#### **Responsabilità del Cloud Provider (Security OF the Cloud)**

**Sicurezza fisica dei data center:**
- Accesso controllato basato sulla necessità
- Sicurezza perimetrale

**Infrastruttura hardware e software:**
- Decommissioning storage, logging accesso host OS, auditing

**Infrastruttura di rete:**
- Rilevamento intrusioni
- Protezione rete

**Infrastruttura di virtualizzazione:**
- Isolamento delle istanze

#### **Responsabilità del Cliente (Security IN the Cloud)**

**Dati del cliente:**
- Crittografia lato client
- Integrità dati
- Autenticazione

**Applicazioni e IAM:**
- Gestione identità e accessi
- Configurazioni applicative

**Sistema operativo, rete e configurazione firewall:**
- Patching e manutenzione
- Configurazioni OS
- Firewall host-based
- Sistemi di rilevamento/prevenzione intrusioni

**Protezione traffico di rete:**
- Crittografia
- Integrità
- Identità

**Configurazioni configurabili dal cliente:**
- Gestione account
- Impostazioni login e permessi per ogni utente

---

## **I QUATTRO MODELLI DI DEPLOYMENT**

### **Public Cloud**

**Caratteristiche:**
- **Accessibile a tutti**
- Risorse condivise tra utenti multipli
- Gestito da provider di terze parti
- Scalabilità massima
- Costi ridotti

**Esempi:** AWS, Google Cloud, Microsoft Azure

### **Private Cloud**

**Definizione:** Qualsiasi cloud dedicato a un cliente specifico e in isolamento logico/fisico con altri clienti.

**Tipologie:**

**On-Premise:**
- Cloud ospitato nei data center dell'organizzazione
- Controllo completo
- Investimento di capitale significativo

**Virtual Private Cloud (VPC):**
- **Amazon VPC e Google Cloud VPC**: Focus su funzionalità VPN per connettere risorse
- **IBM, HP Clouds**: Maggiore isolamento fisico

### **Community Cloud**

**Caratteristiche:**
- Condiviso da diverse organizzazioni con requisiti simili
- Può essere gestito internamente o da terze parti
- Costi e rischi condivisi

**Esempi:** Cloud per settore sanitario, cloud per istituzioni governative

### **Hybrid Cloud**

**Caratteristiche:**
- Combinazione di cloud pubblici e privati
- Dati e applicazioni possono muoversi tra ambienti
- Maggiore flessibilità
- Ottimizzazione costi

**Casi d'uso:**
- Workload sensibili su private cloud
- Workload variabili su public cloud
- Backup e disaster recovery

---

## **CRITERI PER DETERMINARE SE UN SERVIZIO È CLOUD**

### **NIST SP 500-322: Valutazione dei Servizi**

**Framework di valutazione basato su NIST 800-145** per determinare:

#### **Worksheets di Valutazione**

**1. Cloud Service Worksheet:**
- Verifica se un servizio è effettivamente un servizio cloud
- Valutazione delle 5 caratteristiche essenziali

**2. Cloud Service Model Worksheet:**
- Determinazione del modello di servizio utilizzato (IaaS/PaaS/SaaS)

**3. Cloud Deployment Model Worksheet:**
- Identificazione del modello di deployment (Public/Private/Community/Hybrid)

---

## **NIST SP 500-292: ARCHITETTURA DI RIFERIMENTO CLOUD**

### **I Ruoli nel Cloud Computing**

#### **Cloud Consumer (Consumatore Cloud)**
- **Utente finale** che utilizza servizi cloud
- Negozia e usa servizi cloud
- Gestisce uso dei servizi

#### **Cloud Provider (Provider Cloud)**
- **Fornitore di servizi** cloud
- Mantiene infrastruttura cloud
- Gestisce servizi cloud

#### **Cloud Auditor (Auditor Cloud)**
- **Valutazione indipendente** dei servizi cloud
- Verifica prestazioni, sicurezza, privacy
- Conformità agli standard

#### **Cloud Broker (Broker Cloud)**
- **Intermediario** che facilita uso, gestione e negoziazione dei servizi
- Tre tipi di servizi:
  - **Service Intermediation**
  - **Service Aggregation** 
  - **Service Arbitrage**

#### **Cloud Carrier (Carrier Cloud)**
- **Provider di connectività** e trasporto
- Fornisce accesso ai servizi cloud tramite rete

### **Interazioni tra gli Attori**

```
Cloud Consumer ↔ Cloud Broker ↔ Cloud Provider
     ↓              ↓              ↓
Cloud Carrier ← → Cloud Auditor → Security/Compliance
```

### **Cloud Broker - Approfondimento**

#### **Service Intermediation**
- **Miglioramento di capacità specifiche**
- **Fornitura di servizi a valore aggiunto**
- Enhancement di sicurezza, prestazioni, usabilità

#### **Service Aggregation**
- **Combinazione e integrazione** per implementare nuovo servizio
- Orchestrazione di servizi multipli
- Creazione di soluzioni composite

#### **Service Arbitrage**
- **Aggregazione dinamica**
- Selezione ottimale tra provider multipli
- Ottimizzazione costi e prestazioni

### **Attività del Cloud Service Provider (CSP)**

**Livelli di servizio:**
- **SaaS Management**: Gestione applicazioni software
- **PaaS Management**: Gestione piattaforme di sviluppo
- **IaaS Management**: Gestione infrastruttura

**Service Orchestration trasversale** a tutti i livelli

### **Service Orchestration nel Cloud**

**Cloud Service Orchestration** si riferisce alla composizione di componenti di sistema per supportare le attività dei Cloud Provider nella disposizione, coordinamento e gestione delle risorse computazionali per fornire servizi cloud ai Cloud Consumer.

**Struttura a livelli:**
```
SaaS Orchestration
├── PaaS Orchestration  
├── IaaS Orchestration
└── Physical Layer
```

**Nota importante:** Questo è diverso dalla Web Service Orchestration e Microservice Orchestration che vedremo in seguito.

### **Cloud Service Management**

**Include tutte le funzioni relative ai servizi** necessarie per la gestione e operazione di quei servizi richiesti o proposti ai cloud consumer.

**Aree di gestione:**
- **Business Support**: Fatturazione, accounting, supporto clienti
- **Provisioning/Configuration**: Setup e configurazione servizi
- **Portability/Interoperability**: Migrazione e integrazione

---

## **CONSIDERAZIONI FINALI E PROSPETTIVE**

### **Vantaggi del Cloud Computing**

**Per le Organizzazioni:**
- **Riduzione costi** di infrastruttura IT
- **Scalabilità** elastica basata sulla domanda
- **Focus sul core business** invece che su IT infrastructure
- **Accesso a tecnologie avanzate** senza investimenti iniziali

**Per i Provider:**
- **Economie di scala** massive
- **Utilizzo ottimizzato** delle risorse
- **Revenue model** ricorrente
- **Innovazione continua** dei servizi

### **Sfide e Considerazioni Critiche**

**Sicurezza e Privacy:**
- Controllo sui dati ridotto
- Conformità normativa complessa
- Gestione identità e accessi

**Dipendenza e Lock-in:**
- Vendor lock-in tecnologico
- Dipendenza dalla connettività
- Portabilità dati e applicazioni

**Prestazioni e Disponibilità:**
- Latenza di rete
- Disponibilità del servizio
- SLA management

### **Trend Futuri**

**Edge Computing:**
- Avvicinamento del computing agli utenti finali
- Riduzione latenza per applicazioni critiche

**Serverless Computing:**
- Astrazione completa dell'infrastruttura
- Focus esclusivo sulla logica applicativa

**Multi-Cloud e Hybrid Cloud:**
- Distribuzione workload su provider multipli
- Ottimizzazione costi e rischi

**AI/ML as a Service:**
- Democratizzazione dell'intelligenza artificiale
- Servizi cognitivi ready-to-use

---

## **DOMANDE DI RIFLESSIONE**

1. **Localizzazione vs Prestazioni**: Come bilanciare la necessità di controllo sulla localizzazione dei dati con i benefici del cloud computing?

2. **Modelli di Servizio**: In che scenari scegliereste IaaS vs PaaS vs SaaS? Quali sono i trade-off?

3. **Sicurezza Condivisa**: Come garantire che le responsabilità di sicurezza siano chiaramente definite e implementate?

4. **Vendor Lock-in**: Quali strategie adottare per mantenere flessibilità e portabilità?

5. **Cloud Economics**: Come ottimizzare i costi cloud mantenendo prestazioni e disponibilità?

Il cloud computing rappresenta una **trasformazione fondamentale** nel modo in cui concepiamo, implementiamo e gestiamo l'IT. La comprensione profonda dei suoi principi, modelli e caratteristiche è essenziale per chiunque voglia operare efficacemente nell'era digitale moderna.

**La visione di John McCarthy del 1961 si è finalmente realizzata**: l'informatica è diventata davvero una utility pubblica, accessible, scalabile e misurata, proprio come aveva profeticamente immaginato oltre 60 anni fa.
