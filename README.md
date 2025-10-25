# 🚗 Magliano Smart Mobility

> **Sistema comunale open source per la gestione intelligente della mobilità pubblica e condivisa**  
> Un progetto del **Comune di Magliano in Toscana** per una Pubblica Amministrazione più efficiente, sostenibile e digitale.

---

## 📋 Indice

- [Obiettivo del progetto](#-obiettivo-del-progetto)
- [Struttura del sistema](#️-struttura-del-sistema)
- [Componenti Hardware](#-componenti-hardware)
- [Componenti Software](#-componenti-software)
- [Gestione tramite Agente IA (n8n)](#-gestione-tramite-agente-ia-n8n)
- [Funzionamento operativo](#-funzionamento-operativo)
- [Benefici per la Pubblica Amministrazione](#-benefici-per-la-pubblica-amministrazione)
- [Benefici per i cittadini](#-benefici-per-i-cittadini)
- [Impatto ambientale e sociale](#-impatto-ambientale-e-sociale)
- [Sicurezza e gestione](#-sicurezza-e-gestione)
- [Sintesi finale](#-sintesi-finale)
- [Licenza](#-licenza)
- [Crediti](#-crediti)

---

## 🎯 Obiettivo del progetto

**Magliano Smart Mobility** è un progetto di innovazione locale che digitalizza la gestione della **flotta comunale** e del **bike sharing cittadino**, integrando tracciamento GPS, pagamenti PagoPA e **automazione intelligente** tramite **agente IA** basato su **n8n**.

L’obiettivo è:
- migliorare efficienza e trasparenza amministrativa,  
- ridurre costi e impatti ambientali,  
- semplificare i servizi di mobilità pubblica,  
- favorire una gestione autonoma e sicura dei dati.

---

## ⚙️ Struttura del sistema

Il sistema unisce **dispositivi fisici** e **software intelligente**:

1. **Hardware comunale**: GPS, QR code, e-bike e sensori.  
2. **Software locale Docker**: gestione, automazione, e reportistica.  
3. **Agente IA n8n**: supporta gli operatori e automatizza decisioni operative.

Tutto opera **all’interno dell’intranet comunale**, senza dipendenza da servizi esterni.

---

## 🔩 Componenti Hardware

### 🚗 Flotta comunale

| Componente | Descrizione | Funzione |
|-------------|-------------|----------|
| **Tracker GPS (Concox GT06N)** | Installato su ciascun veicolo comunale | Tracciamento e storico spostamenti |
| **SIM IoT dati** | Connettività 4G | Invio dati al server |
| **QR code interno** | Adesivo nel veicolo | Accesso a dati, scadenze e schede manutenzione |

### 🚲 Bike sharing comunale

| Componente | Descrizione | Funzione |
|-------------|-------------|----------|
| **E-bike con GPS** | Bicicletta elettrica comunale | Tracciamento percorsi e soste |
| **QR code** | Sul telaio | Prenotazione e pagamento via smartphone |
| **Lucchetto elettronico** | Blocco controllato da software | Sblocco solo dopo pagamento verificato |

### 🏢 Infrastruttura comunale

| Componente | Descrizione | Funzione |
|-------------|-------------|----------|
| **Server Linux / Docker** | Server locale o Mini PC | Esecuzione dell’intero sistema |
| **LAN / Intranet** | Connessione interna | Sicurezza e continuità |
| **UPS** | Gruppo di continuità | Protezione alimentazione |
| **Modulo PagoPA** | Gateway o simulazione | Gestione pagamenti pubblici |

---

## 💻 Componenti Software

| Software | Ruolo | Benefici |
|-----------|--------|-----------|
| 🛰️ **Traccar** | Gestione GPS | Ricezione dati veicoli e bici |
| ⚙️ **n8n + Agente IA** | Automazione e IA | Controllo flussi, tariffe e pagamenti |
| 🗄️ **PostgreSQL / PostGIS** | Database geografico | Archivia percorsi e coordinate |
| 📊 **Grafana** | Dashboard | Visualizzazione trasparente dati |
| 🔒 **Keycloak** | Autenticazione | Accesso sicuro integrato AD |
| 💳 **PagoPA Proxy** | Gateway | Pagamenti digitali tracciati |
| 🐳 **Docker Compose** | Contenitore unico | Gestione e manutenzione semplificata |

---

## 🤖 Gestione tramite Agente IA (n8n)

L’agente IA rappresenta il **cuore intelligente del sistema**.  
Basato su **n8n**, analizza in tempo reale i dati provenienti da Traccar, PostgreSQL e PagoPA per **automatizzare** attività tecniche e amministrative.

### 🧠 Compiti principali

| Funzione | Descrizione |
|-----------|-------------|
| **Analisi predittiva** | Anticipa scadenze manutenzioni e usura dei veicoli |
| **Ottimizzazione percorsi** | Suggerisce l’uso più efficiente dei mezzi e delle e-bike |
| **Gestione tariffe** | Calcola automaticamente il costo del servizio in base a tempo, distanza o fascia oraria |
| **Creazione IUV PagoPA** | Genera e assegna un codice IUV per ogni noleggio o servizio |
| **Verifica pagamenti** | Controlla lo stato dei pagamenti PagoPA e aggiorna la disponibilità dei mezzi |
| **Rendicontazione automatica** | Produce report contabili giornalieri e mensili per il responsabile del servizio |
| **Dialogo con gli operatori** | Risponde via Telegram o pannello web alle richieste del personale |
| **Alert automatici** | Segnala anomalie, guasti, scadenze o superamenti soglie di costo |

### 🗣️ Esempi d’uso

- “Calcola quanto ha incassato il bike sharing nel weekend.”  
- “Genera gli IUV per le prenotazioni di oggi e inviali via email.”  
- “Blocca la bici 03 se non risulta il pagamento entro 5 minuti.”  
- “Mostra i veicoli con manutenzione scaduta.”  
- “Prepara la rendicontazione per il bilancio settimanale.”

### 💰 Gestione tariffe e pagamenti

L’agente IA gestisce in autonomia il **ciclo completo di tariffazione**:

1. **Definizione tariffe comunali**: impostate tramite pannello n8n o database (es. €0,50 ogni 15 minuti).  
2. **Calcolo dinamico**: l’agente applica la tariffa in base alla durata effettiva, al tipo di veicolo e alla fascia oraria.  
3. **Generazione IUV PagoPA**: ad ogni prenotazione, crea un codice IUV e la richiesta di pagamento.  
4. **Verifica automatica**: monitora lo stato del pagamento e attiva il servizio solo dopo la conferma.  
5. **Rendicontazione**: al termine, registra incasso, mezzo usato e durata nel database contabile.  

Tutto il processo avviene **senza intervento manuale**, riducendo il carico sugli uffici e garantendo **trasparenza totale**.

### 🔧 Benefici dell’Agente IA

| Area | Vantaggio |
|------|------------|
| **Efficienza operativa** | Automatizza calcoli, verifiche e report |
| **Accuratezza contabile** | Elimina errori nei conteggi e nei pagamenti |
| **Decisioni basate sui dati** | Analisi in tempo reale di costi e utilizzo |
| **Riduzione tempi di lavoro** | Fino al -70% del tempo amministrativo |
| **Comunicazione naturale** | Interazione vocale o testuale via Telegram |
| **Scalabilità** | Aggiunta di nuovi mezzi o tariffe senza modifiche strutturali |

---

## 🔄 Funzionamento operativo

### 🚗 Flotta comunale
1. Il veicolo si accende → Traccar invia i dati.  
2. L’agente IA elabora tragitti e tempi di utilizzo.  
3. Se superate soglie chilometriche → avviso manutenzione.  
4. Grafana aggiorna dashboard e storico giornaliero.

### 🚲 Bike sharing comunale
1. Il cittadino scansiona il QR code.  
2. L’agente IA calcola la tariffa e genera un IUV PagoPA.  
3. Dopo il pagamento, la bici si sblocca.  
4. Al termine del noleggio, l’agente aggiorna i dati contabili e statistici.

---

## 🏛️ Benefici per la Pubblica Amministrazione

| Area | Beneficio |
|------|------------|
| **Gestione automatica** | Tutti i flussi (pagamenti, manutenzioni, tariffe) sono digitali |
| **Riduzione costi** | Meno ore di gestione e zero canoni software |
| **Trasparenza totale** | Tutti i dati e gli incassi visibili in tempo reale |
| **Contabilità semplificata** | Rendicontazione automatica integrata |
| **Scalabilità** | Sistema estendibile ad altri servizi comunali (parcheggi, bus, ecc.) |
| **Indipendenza** | Tutto open source e gestito internamente |

---

## 👥 Benefici per i cittadini

| Aspetto | Vantaggio |
|----------|-----------|
| **Accesso rapido** | Prenotazione e pagamento in pochi secondi |
| **Pagamenti sicuri** | Sistema PagoPA certificato |
| **Servizio trasparente** | Tariffe chiare e calcolate automaticamente |
| **Affidabilità** | Noleggi tracciati e monitorati |
| **Innovazione** | Esperienza moderna e digitale per tutti |

---

## 🌱 Impatto ambientale e sociale

| Ambito | Beneficio |
|--------|------------|
| **Emissioni CO₂** | -2,5 tonnellate/anno grazie all’uso di e-bike e ottimizzazione spostamenti |
| **Consumo carburante** | -15% grazie alla pianificazione intelligente |
| **Turismo sostenibile** | Accesso semplice a mezzi ecologici per visitatori |
| **Immagine pubblica** | Comune efficiente, innovativo e “green” |
| **Coinvolgimento** | Incentivo alla cittadinanza attiva e alla mobilità dolce |

---

## 🔐 Sicurezza e gestione

- Tutti i dati **restano all’interno dell’intranet comunale**  
- Accesso tramite **Keycloak e Active Directory locale**  
- **Backup automatici** e versionamento container Docker  
- **Conformità GDPR** e tracciabilità completa di ogni operazione  

---

## 🚀 Sintesi finale

**Magliano Smart Mobility** è una piattaforma comunale open source, autonoma e intelligente che unisce:
- Mobilità sostenibile  
- Digitalizzazione amministrativa  
- Intelligenza artificiale operativa  
- Pagamenti pubblici tracciati  

✅ **Riduce costi, tempi e burocrazia**  
✅ **Aumenta trasparenza e fiducia**  
✅ **Promuove sostenibilità e innovazione**  

---

## 📄 Licenza

Rilasciato sotto licenza **MIT**  
© 2025 Comune di Magliano in Toscana  

---

## 🙏 Crediti

### Software Open Source
- [n8n](https://n8n.io/)
- [Traccar](https://www.traccar.org/)
- [PostgreSQL / PostGIS](https://www.postgresql.org/)
- [Grafana](https://grafana.com/)
- [Docker](https://www.docker.com/)
- [Keycloak](https://www.keycloak.org/)

### Hardware consigliato
- [Concox GT06N](https://www.concox.com/)
- [Invoxia Bike Tracker](https://www.invoxia.com/)

### Servizi pubblici integrati
- [PagoPA](https://www.pagopa.gov.it/)
- [Comune di Magliano in Toscana](https://www.comune.maglianointoscana.gr.it/)

---

> 🏛️ *“Un piccolo Comune, una grande visione: mobilità intelligente, sostenibile e condivisa.”*  
> — **Comune di Magliano in Toscana — Ufficio Innovazione e Mobilità
