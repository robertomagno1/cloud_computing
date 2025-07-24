# Cloud Computing: Automation & Orchestration

L'Autonomic Computing si basa su quattro proprietà fondamentali, tutte caratterizzate dal prefisso "self-":

1. **Self-Configuration** (Auto-configurazione): Il sistema è in grado di configurarsi automaticamente
2. **Self-Optimization** (Auto-ottimizzazione): Il sistema ottimizza le proprie prestazioni in modo autonomo
3. **Self-Healing** (Auto-riparazione): Il sistema rileva e risolve automaticamente i problemi
4. **Self-Protection** (Auto-protezione): Il sistema si difende autonomamente da minacce e attacchi

---

## **PARTE I: AUTOMATION**

### **IT Automation vs Autonomic Computing**

È importante distinguere tra questi due concetti spesso confusi:

**Autonomic Computing:**
- **Obiettivo**: Eliminare completamente l'intervento umano dal processo decisionale
- Gli umani forniscono solo obiettivi di alto livello
- Il sistema decide autonomamente come raggiungerli

**IT Operations Automation (ITOA):**
- **Obiettivo**: Ridurre (non eliminare) l'interazione umana e i processi manuali
- Gli umani definiscono le procedure, il sistema le esegue automaticamente
- Maggior controllo umano rispetto all'Autonomic Computing

### **Che cos'è l'IT Operations Automation?**

**Come funziona:**
Utilizza strumenti software e tecnologie per automatizzare vari processi IT organizzativi e operativi.

**Cosa automatizza:**
- Task ripetitivi e routinari
- Orchestrazione di workflow complessi
- Integrazione dell'automazione in sistemi e applicazioni esistenti

**Benefici principali:**

1. **Efficienza e Produttività Migliorate**
   - Riduce drasticamente il tempo necessario per task manuali
   - Libera il personale IT per attività strategiche ad alto valore aggiunto

2. **Riduzione degli Errori Umani**
   - Elimina i rischi di errori causati dall'intervento manuale
   - Garantisce risultati più accurati e consistenti

3. **Risparmio di Tempo e Costi**
   - Minimizza il carico di task ripetitivi e time-consuming
   - Riduce i costi operativi complessivi dell'organizzazione

### **Sfide nei Data Center Moderni**

I data center moderni affrontano numerose sfide che possono essere affrontate con automation o autonomic computing:

**Sfide principali:**
- **Consolidamento server** (migrazione VM)
- **Garanzia dei Service Level Agreements (SLA)**
- **Gestione guasti hardware/software** (rilevamento, risoluzione, correzione)
- **Aggiornamenti software**
- **Correzione di vulnerabilità e bug**

**Domanda di riflessione:** Quali di queste sfide possono essere affrontate con autonomic computing e quali con automation?

### **Sfide nelle Infrastrutture Cloud su Larga Scala**

**Problemi specifici:**
- **Configurazione nodi infrastruttura**
- **Configurazione componenti applicativi**
- **Aggiornamenti e patching** (devono essere eseguiti nell'ordine corretto)
- **Backup automatizzati**
- **CI/CD** (Continuous Integration/Continuous Delivery)

### **Strumenti di Automation Popolari**

Il mercato offre numerosi strumenti specializzati:
- **Ansible** (Red Hat)
- **Puppet**
- **Chef**
- **Terraform**
- **SaltStack**
- **CloudFormation** (AWS)

**Ambiti di automazione nei data center:**
- **Provisioning**: Creazione automatica di risorse
- **Configuration**: Configurazione automatica di sistemi
- **Patching**: Applicazione automatica di patch
- **Monitoring**: Monitoraggio continuo automatizzato
- **CI/CD**: Pipeline di sviluppo e deployment automatizzate

---

## **ANSIBLE: UN ESEMPIO PRATICO DI AUTOMATION**

### **Introduzione ad Ansible**

Ansible è uno strumento open source per:
- **Configuration management** (gestione configurazioni)
- **Deployment** (distribuzione applicazioni)
- **Orchestration** (coordinamento processi)

### **Architettura Agentless**

**Caratteristica distintiva di Ansible:**

```
Control Server ----SSH/WinRM----> Managed Hosts
```

**Vantaggi del modello agentless:**
- **Nessun software** da installare sui nodi gestiti
- **Zero overhead** quando la gestione non è attiva
- **Ideale per ambienti ad alta sicurezza** o prestazioni elevate
- **Nessuna preoccupazione** per stabilità o permanenza di agenti

### **Push Model**

**Come funziona:**
1. Le istruzioni di controllo vengono preparate e memorizzate sul server di controllo
2. Vengono trasferite (push) ai host da gestire via SSH (Linux/UNIX) o WinRM (Windows)
3. I moduli Ansible vengono passati alle macchine remote
4. Le macchine remote non possono vedere o influenzare la configurazione di altre macchine

**Nota importante:** Non tutti i nodi possono eseguire codice (es. dispositivi di rete)

### **Ansible Playbooks**

**Definizione:**
I Playbooks sono il cuore di Ansible, scritti in sintassi YAML, che permettono:

**Funzionalità:**
- **Gestione configurazioni semplice** e deployment multi-macchina
- **Ripetibilità e riutilizzo**
- **Dichiarazione di configurazioni**
- **Orchestrazione di processi manuali ordinati** su più set di macchine
- **Esecuzione sincrona** (default) o **asincrona** (per task long-running)
- **Definizione dello stato desiderato**
- **Idempotenza** (possono essere eseguiti più volte senza effetti collaterali)

**Struttura dei Playbooks:**

```yaml
Playbook
├── Play 1 (target: webservers)
│   ├── Task 1 (modulo: yum)
│   ├── Task 2 (modulo: template)
│   └── Task 3 (modulo: service)
└── Play 2 (target: databases)
    ├── Task 1
    └── Task 2
```

**Componenti:**
- **Playbook**: Contiene serie di "plays"
- **Play**: Definisce automazione su un set di host (inventory)
- **Task**: Singole operazioni che chiamano moduli Ansible
- **Moduli**: Piccoli pezzi di codice per task specifici

### **Core Modules e Desired State**

**Caratteristiche dei moduli core:**
- Scritti per permettere facile configurazione dello **stato desiderato**
- **Verificano che il task sia necessario** prima di eseguirlo
- **Esempio**: Se un task definisce l'avvio di un webserver, la configurazione viene eseguita solo se il webserver non è già avviato

**Benefici:**
- La configurazione può essere applicata ripetutamente **senza effetti collaterali**
- L'esecuzione è **rapida ed efficiente** quando già applicata

### **Esempio Pratico: Playbook per Web Server e Database**

```yaml
---
# Play 1: Configurazione Web Server
- name: Configure Web Servers
  hosts: webservers
  tasks:
    - name: Ensure httpd is latest version
      ansible.builtin.yum:
        name: httpd
        state: latest
    
    - name: Write Apache config file
      ansible.builtin.template:
        src: /srv/httpd.j2
        dest: /etc/httpd.conf
    
    - name: Ensure httpd is running
      ansible.builtin.service:
        name: httpd
        state: started

# Play 2: Configurazione Database Server
- name: Configure Database Servers
  hosts: databases
  tasks:
    - name: Ensure postgresql is latest version
      ansible.builtin.yum:
        name: postgresql
        state: latest
```

### **Caso d'Uso Complesso: Rolling Update di Applicazione Web a 3 Tier**

**Scenario:**
Consideriamo un'applicazione web tradizionale a tre livelli:
- **Application servers**
- **Database servers** 
- **Content servers**
- **Load balancers**
- **Sistema di monitoring** connesso a sistema di alert (notifiche pager)

**Processo di Rolling Update Automatizzato con Ansible:**

1. **Consultazione repository configurazioni** per informazioni sui server coinvolti
2. **Configurazione OS base** su tutte le macchine e applicazione dello stato desiderato
3. **Identificazione porzione** di application server da aggiornare
4. **Segnalazione al sistema di monitoring** della finestra di manutenzione prima di portare i server offline
5. **Segnalazione ai load balancer** per rimuovere gli application server dal pool bilanciato
6. **Arresto del web application server**
7. **Deploy/aggiornamento** del codice, dati e contenuti del web application server
8. **Avvio del web application server**
9. **Esecuzione test appropriati** sul nuovo server e codice
10. **Segnalazione ai load balancer** per rimettere gli application server nel pool bilanciato
11. **Segnalazione al sistema di monitoring** per riprendere gli alert su problemi rilevati sui server
12. **Ripetizione processo** per i rimanenti application server (rolling update)
13. **Ripetizione rolling update** per altri tier (database o content tier)
14. **Invio report email e logging** quando gli aggiornamenti sono completi

### **Automation e Autonomic Computing: L'Integrazione**

**Come l'automation supporta l'autonomic computing:**

- **Self-configuration**: Quando è decisa la riconfigurazione, un playbook può essere usato per deployare la nuova configurazione di sistema

- **Self-optimization**: Quando è deciso di aggiungere nuovi server, può essere eseguito un playbook che fa tutte le azioni richieste

- **Self-healing**: Se le prestazioni di un server/applicazione stanno degradando, può essere eseguito un playbook che scollega e riavvia il server/applicazione

- **Self-protection**: Se un sistema è sotto attacco a causa di una vulnerabilità, può essere deployato un playbook per rolling update

---

## **PARTE II: ORCHESTRATION**

### **Orchestration vs Automation**

**Distinzione fondamentale:**

**Automation:**
- Configurare **un singolo task** per essere eseguito autonomamente
- Focus: Task individuale

**Orchestration:**
- Automatizzare **una serie di task individuali** per lavorare insieme come processo o workflow
- Focus: Coordinamento di processi complessi

### **Tipi di Orchestration**

L'orchestration può essere specializzata a seconda del contesto:

1. **Service orchestration** (già presentato nelle lezioni precedenti su SOA)
2. **IT infrastructure orchestration**
3. **Container orchestration** (focus principale di questa lezione)

### **Container Orchestration**

**Definizione:**
Permette a provider cloud e applicazioni di definire come selezionare, deployare, monitorare e controllare dinamicamente la configurazione di applicazioni multi-container impacchettate nel cloud.

**Focus operativo:**
Si concentra sulla gestione runtime per supportare le fasi:
- **Deploy** (Distribuzione)
- **Run** (Esecuzione)
- **Maintain** (Manutenzione)

**Funzionalità principali offerte:**
- **Resource limit control** (controllo limiti risorse)
- **Scheduling** (pianificazione)
- **Load balancing** (bilanciamento carico)
- **Health check** (controllo stato salute)
- **Fault tolerance** (tolleranza ai guasti)
- **Auto-scaling** (scalabilità automatica)

### **Container Lifecycle**

**Fasi del ciclo di vita:**
1. **Acquire** (Acquisizione)
2. **Build** (Costruzione)
3. **Ship** (Spedizione)
4. **Deploy** (Distribuzione)
5. **Run** (Esecuzione)
6. **Maintain** (Manutenzione)

**Ruoli:**
- **Container Manager**: Fornisce API per supportare almeno dalle fasi di acquisizione all'esecuzione
- **Orchestrator**: Automatizza le fasi di deployment, esecuzione e manutenzione (runtime)

---

## **KUBERNETES: L'ORCHESTRATORE LEADER**

### **Introduzione a Kubernetes (K8s)**

**Definizione:**
Kubernetes è una piattaforma per gestire carichi di lavoro containerizzati e servizi.

**Cosa significa "gestire carichi di lavoro containerizzati"?**

1. **Service discovery e load balancing**
2. **Storage orchestration**: Mount automatico dello storage
3. **Automated rollouts e rollbacks**: Portare automaticamente i container deployati in uno stato desiderato (descritto)
4. **Automatic bin packing**: Adattare i container sui nodi cluster per il miglior uso delle risorse; a ogni container vengono date CPU e memoria necessarie
5. **Self-healing**: Riavvio, sostituzione, kill, non pubblicizzare container difettosi
6. **Secret e configuration management**

### **Architettura del Cluster Kubernetes**

**Struttura base:**
```
Control Plane (Master Node)
├── kube-apiserver
├── etcd
├── kube-scheduler
├── kube-controller-manager
└── cloud-controller-manager

Worker Nodes (VM o server fisici)
├── kubelet
├── kube-proxy
└── container runtime
```

### **Componenti del Control Plane**

**kube-apiserver:**
- Front-end del control plane di Kubernetes
- Scala orizzontalmente

**etcd:**
- Key-value store consistente e altamente disponibile
- Usato come backing store per tutti i dati del cluster

**kube-scheduler:**
- Monitora i Pod appena creati senza nodo assegnato
- Seleziona un nodo per la loro esecuzione
- Decisioni di scheduling basate su:
  - Requisiti di risorse individuali e collettivi
  - Vincoli hardware/software/policy
  - Specifiche di affinità e anti-affinità
  - Località dei dati
  - Interferenza tra workload
  - Scadenze

**kube-controller-manager:**
- Esegue processi controller, ad esempio:
  - **Node controller**: Rileva e risponde quando i nodi vanno down
  - **Job controller**: Monitora oggetti Job e crea Pod per completare i task
  - **EndpointSlice controller**: Popola oggetti EndpointSlice (collega Services e Pod)
  - **ServiceAccount controller**: Crea ServiceAccount default per nuovi namespace

**cloud-controller-manager:**
- Collega un cluster Kubernetes con le API specifiche del cloud provider

### **Componenti dei Node**

**kubelet:**
- Agente che gira su ogni nodo
- Controlla che i container descritti in un PodSpec siano in esecuzione e sani
- PodSpecs: input per il kubelet

**kube-proxy:**
- Proxy di rete che gira su ogni nodo
- Mantiene regole di rete sui nodi
- Queste regole permettono comunicazione di rete ai Pod del cluster da sessioni di rete dentro o fuori il cluster

**container runtime:**
- Software responsabile dell'esecuzione dei container
- Esempi: Docker, CRI-O

### **Concetti Fondamentali di Kubernetes**

#### **Objects**

**Definizione:**
Gli oggetti Kubernetes sono entità persistenti in K8s usate per rappresentare lo stato del cluster.

**Cosa possono descrivere:**
- **Applicazioni containerizzate** in esecuzione (e su quali nodi)
- **Risorse disponibili** per quelle applicazioni
- **Policy** che governano il comportamento delle applicazioni (restart policy, upgrade, fault-tolerance)

**Record of Intent:**
Un oggetto K8s è un "record di intenzione" - una volta creato, il sistema Kubernetes lavorerà costantemente per assicurare che quell'oggetto esista.

#### **Spec & Status**

**Due campi fondamentali di ogni oggetto:**

**spec (specifica):**
- Descrive lo **stato desiderato**
- Esempio: 3 istanze dell'applicazione

**status (stato):**
- Descrive lo **stato corrente** dell'oggetto
- Esempio: 2 istanze sono attualmente up&running

**Esempio pratico con Deployment:**
1. Crei un Deployment e imposti la spec: "voglio tre repliche dell'applicazione"
2. Il sistema Kubernetes legge la Deployment spec
3. Avvia tre istanze dell'applicazione desiderata
4. Aggiorna lo status per corrispondere alla spec
5. Se una di quelle istanze fallisce (cambio di status), K8s risponde alla differenza tra spec e status avviando un'istanza sostitutiva

#### **Pods**

**Definizione:**
Le unità più piccole deployabili di computing che possono essere create e gestite in Kubernetes.

**Caratteristiche:**
- Modella un "logical host" specifico dell'applicazione
- Contiene uno o più container applicativi relativamente strettamente accoppiati
- Condividono:
  - **Risorse di storage**
  - **Risorse di rete**
  - **Specifica di come eseguire i container**

**Contesto condiviso:**
- I contenuti di un Pod sono sempre co-localizzati e co-schedulati
- Girano in un contesto condiviso
- Il contesto condiviso di un Pod è un set di namespace Linux, cgroups
- Un Pod è simile a un set di container con namespace condivisi e volumi filesystem condivisi

#### **Nodes**

**Definizione:**
Una macchina virtuale o fisica, membro di un cluster Kubernetes.

**Caratteristiche:**
- Ogni nodo è gestito dal control plane
- Contiene i servizi necessari per eseguire Pod
- I nodi eseguono carichi di lavoro containerizzati (Pod)

**Come diventano membri del cluster:**
1. Il kubelet su un nodo si auto-registra al control plane (API-server)
2. Un utente umano aggiunge manualmente un oggetto Node all'API-server

**Validazione:**
Una volta creato un oggetto Node, il control plane verifica se il nuovo oggetto Node è valido (tutti i servizi sono in esecuzione). Se valido, può eseguire un Pod.

#### **Node Status**

**Descritto da:**

**Address:**
- Hostname, IP (esterno/interno)

**Condition:**
- Stato di salute del nodo
- Se status è unknown o false, il node controller attiva l'eviction API-initiated per tutti i Pod assegnati a quel nodo
- Timeout di eviction default: 5 minuti

**Capacity:**
- Indica le risorse disponibili sul nodo: CPU, memoria, numero massimo di pod schedulabili

**Allocatable:**
- Indica la quantità di risorse su un nodo disponibili per essere consumate dai Pod normali

**Info:**
- Informazioni generali: versione kernel, versione Kubernetes (kubelet e kube-proxy), dettagli container runtime, sistema operativo

### **Node Affinity e Anti-Affinity**

#### **Node Affinity**

**Definizione:**
Set di regole per specificare preferenze che influenzano come i pod sono posizionati sui nodi.

**Utilità:**
- **Conformità con leggi di sovranità dei dati**
- **Ottimizzazione latenza di rete** per sistemi distribuiti
- **Allocazione risorse** per High Performance Computing (HPC)
- **Gestione workload** con requisiti di storage specifici
- **Supporto multi-tenancy** e isolamento risorse

#### **Node Anti-Affinity**

**Definizione:**
Regole per prevenire lo scheduling di pod sugli stessi o diversi nodi.

**Utilità:**
- **Deployment ad alta disponibilità**: Distribuire pod su nodi/zone diverse per ridurre single point of failure
- **Separazione workload**: Tenere certi pod separati per prestazioni o sicurezza

### **Controllers in Kubernetes**

**Definizione:**
I controller sono control loop che monitorano lo stato di un cluster K8s, poi effettuano o richiedono cambiamenti dove necessario.

**Funzionamento:**
- Un controller traccia almeno un tipo di risorsa Kubernetes
- Questi oggetti hanno un campo spec che rappresenta lo stato desiderato
- I controller per quella risorsa sono responsabili di rendere lo stato corrente più vicino allo stato desiderato

#### **Esempio: Job Controller**

**Processo:**
1. **Job** è una risorsa Kubernetes che esegue un Pod (o più Pod) per portare a termine un task e poi si ferma
2. Quando il Job controller vede un nuovo job, si assicura che da qualche parte nel cluster, i kubelet su un set di nodi stiano eseguendo il numero giusto di Pod per completare il lavoro
3. Il Job controller dice all'API server di creare o rimuovere Pod
4. Altri componenti nel control plane agiscono sulle nuove informazioni
5. Una volta completato il lavoro, il Job controller aggiorna l'oggetto Job per marcarlo come Finished

#### **Esempio: Controllo Risorse Cluster Esterne**

**Cluster Autoscaler:**
- Per assicurare che ci siano abbastanza nodi nel cluster
- K8s ha un controller che scala orizzontalmente i nodi in un cluster
- I controller che interagiscono con stato esterno trovano il loro stato desiderato dall'API server, poi comunicano direttamente con un sistema esterno per portare lo stato corrente più in linea

### **Scheduler in Kubernetes**

**Definizione:**
Lo scheduling in K8s si riferisce all'assicurare che i Pod siano abbinati ai nodi così che Kubelet possa eseguirli.

**Funzionamento:**
- Uno scheduler monitora i Pod appena creati che non hanno nodo assegnato
- Per ogni Pod che lo scheduler scopre, diventa responsabile di trovare il nodo migliore per quel Pod

**kube-scheduler:**
- È lo scheduler di default
- È possibile implementare scheduler personalizzati

#### **Processo di Scheduling**

**Per ogni pod appena creato o non schedulato:**
1. Seleziona un nodo ottimale
2. Ogni container nei pod ha requisiti diversi per le risorse
3. Anche i pod hanno requisiti diversi per le risorse
4. I nodi esistenti devono essere filtrati secondo i requisiti di scheduling specifici

**Nodi fattibili:**
- Nodi che soddisfano i requisiti di scheduling per un Pod
- Se nessun nodo è adatto, il pod rimane non schedulato finché lo scheduler non riesce a posizionarlo

#### **Algoritmo di Node Selection**

**1. Filtering (Filtrazione):**
- Trova il set di nodi dove è fattibile schedulare il Pod
- **PodFitsResources filter**: Verifica se un nodo candidato ha abbastanza risorse disponibili per soddisfare le richieste specifiche di risorse del Pod
- Può esplorare tutti i nodi del cluster o solo un sottoset (es. 50% dei nodi)
- Produce la lista di nodi contenente qualsiasi nodo adatto
- Se la lista è vuota, quel Pod non è (ancora) schedulabile

**2. Scoring (Punteggio):**
- Lo scheduler assegna un punteggio a ogni nodo che è sopravvissuto al filtering
- Basandosi su regole di scoring attive
- Alla fine, kube-scheduler assegna il Pod al nodo con il ranking più alto
- Se c'è più di un nodo con punteggi uguali, kube-scheduler seleziona uno di questi a caso

**Fattori per decisioni di scheduling:**
- Requisiti di risorse individuali e collettivi
- Vincoli hardware/software/policy
- Località dei dati
- Interferenza tra workload

#### **Configurazione di Filtering e Scoring**

**Scheduling Policies (non supportate dalla v1.23):**
- Permettevano di configurare:
  - Predicates per filtering
  - Priorities per scoring

**Scheduling Profiles:**
- Permettono di configurare Plugin che implementano diverse fasi di scheduling:
  - **QueueSort**, **Filter**, **Score**, **Bind**, **Reserve**, **Permit**, e altri
- Usati per personalizzare kube-scheduler

---

## **CONCLUSIONI E OUTCOME**

Al termine di questa lezione completa, dovreste aver acquisito:

### **Concetti Generali**
- **Automation e Orchestration**: Differenze, applicazioni e benefici
- **Differenza tra Autonomic Computing e Automation**: Livelli di intervento umano
- **Differenza tra Automation e Orchestration**: Task singoli vs processi coordinati

### **Esempi Pratici**
- **Come funziona uno strumento di automation** (Ansible): Playbooks, moduli, desired state
- **Come funziona un orchestratore** (Kubernetes): Architettura, oggetti, scheduling

### **Competenze Acquisite**
- Capacità di progettare automazioni per infrastrutture IT
- Comprensione delle sfide di orchestrazione in ambienti cloud
- Conoscenza pratica di strumenti industry-standard
