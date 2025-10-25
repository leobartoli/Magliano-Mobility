# 🚗 Magliano Smart Mobility

> **Sistema comunale open source per la gestione intelligente della mobilità pubblica e condivisa**  
> Un progetto del **Comune di Magliano in Toscana** per una Pubblica Amministrazione più efficiente, sostenibile e digitale.

---

## 📋 Indice

- [Obiettivo del progetto](#-obiettivo-del-progetto)
- [Struttura del sistema](#️-struttura-del-sistema)
- [Componenti Hardware](#-componenti-hardware)
- [Componenti Software](#-componenti-software)
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

**Magliano Smart Mobility** è un progetto di innovazione locale che mira a digitalizzare la gestione della **flotta comunale** e del **bike sharing cittadino**, integrando tracciamento GPS, pagamenti PagoPA e automazione dei processi in un sistema unico e completamente **open source**.

L’obiettivo è:
- migliorare l’efficienza della Pubblica Amministrazione,
- ridurre i costi e gli sprechi,
- promuovere la mobilità sostenibile,
- offrire ai cittadini un servizio moderno, trasparente e accessibile.

---

## ⚙️ Struttura del sistema

Il sistema si compone di due elementi principali:

1. **Hardware fisico** installato su veicoli e biciclette comunali (tracker GPS, QR code, sensori).
2. **Software gestionale** in esecuzione su server comunale o container Docker locale, con database e interfaccia di monitoraggio.

Tutto funziona **all’interno dell’infrastruttura comunale (intranet)**, garantendo sicurezza e controllo totale dei dati.

---

## 🔩 Componenti Hardware

### 🚗 Flotta comunale

| Componente | Descrizione | Funzione |
|-------------|--------------|-----------|
| **Tracker GPS (Concox GT06N)** | Dispositivo connesso al veicolo | Traccia posizione, km e accensioni |
| **SIM IoT dati** | Scheda dati low-cost | Invia le coordinate al server |
| **Alimentazione 12V** | Collegamento fisso all’auto | Funzionamento automatico |
| **QR interno** | Etichetta di servizio | Accesso rapido a scheda veicolo |

### 🚲 Bike sharing comunale

| Componente | Descrizione | Funzione |
|-------------|--------------|-----------|
| **E-bike con tracker** | Bicicletta elettrica con GPS integrato | Noleggio e monitoraggio spostamenti |
| **QR code** | Codice sul telaio | Prenotazione e pagamento via smartphone |
| **Lucchetto elettronico** | Sblocco automatico dopo pagamento | Sicurezza e controllo accessi |

### 🏢 Infrastruttura comunale

| Componente | Descrizione | Funzione |
|-------------|--------------|-----------|
| **Server locale Linux / Docker** | Mini PC o server comunale | Ospita il sistema software |
| **Rete LAN / Intranet** | Connessione interna | Sicurezza e affidabilità |
| **UPS** | Gruppo di continuità | Protezione elettrica |
| **Integrazione PagoPA** | Modulo software o token | Pagamenti sicuri e tracciabili |

---

## 💻 Componenti Software

| Software | Ruolo | Benefici |
|-----------|--------|-----------|
| 🛰️ **Traccar** | Riceve i dati GPS dei mezzi | Monitoraggio in tempo reale |
| ⚙️ **n8n** | Automazione dei flussi | Alert, report e integrazione PagoPA |
| 🗄️ **PostgreSQL / PostGIS** | Database geografico | Archivia percorsi e dati GPS |
| 📊 **Grafana** | Dashboard analitiche | Visualizzazione intuitiva dei dati |
| 🔒 **Keycloak** | Autenticazione centralizzata | Accesso sicuro integrato con AD comunale |
| 💳 **PagoPA Proxy** | Gateway per pagamenti | Incasso automatico dei noleggi |
| 🐳 **Docker Compose** | Gestione dei container | Semplicità di installazione e aggiornamento |

---

## 🔄 Funzionamento operativo

### 🚗 Flotta comunale

1. L’autista accende il veicolo → il GPS inizia il tracciamento.  
2. Il sistema registra automaticamente posizione, km, velocità e soste.  
3. n8n genera report giornalieri e notifiche (es. scadenze, tagliandi).  
4. Grafana mostra l’utilizzo dei mezzi in tempo reale.  

### 🚲 Bike sharing comunale

1. Il cittadino inquadra il QR code e apre la pagina di prenotazione.  
2. Il sistema genera un **codice IUV PagoPA** per il pagamento.  
3. Dopo il pagamento, la bici si sblocca automaticamente.  
4. Il percorso viene registrato e archiviato nel database.  

---

## 🏛️ Benefici per la Pubblica Amministrazione

| Area | Beneficio concreto |
|------|--------------------|
| **Efficienza** | Monitoraggio automatico di mezzi e spostamenti, con riduzione dei tempi di gestione. |
| **Riduzione dei costi** | Fino al 15% in meno di carburante e manutenzione, nessun canone software. |
| **Trasparenza** | Dati pubblici e verificabili: tracciati, km, orari, costi. |
| **Digitalizzazione** | Niente moduli cartacei: tutto automatizzato e archiviato digitalmente. |
| **Autonomia tecnologica** | Sistema self-hosted, senza fornitori esterni o cloud a pagamento. |
| **Pianificazione strategica** | Analisi su percorsi, consumi e manutenzioni per decisioni più efficaci. |

---

## 👥 Benefici per i cittadini

| Aspetto | Vantaggio |
|----------|------------|
| **Accesso semplice** | Prenotazioni via QR e smartphone, senza app dedicate. |
| **Pagamenti sicuri** | Tutto tramite PagoPA, con ricevuta immediata. |
| **Trasparenza amministrativa** | Dati accessibili e chiari sull’uso delle risorse pubbliche. |
| **Mobilità sostenibile** | Promozione di e-bike e mezzi a basso impatto. |
| **Comunicazione diretta** | Notifiche via Telegram o email su disponibilità e novità. |

---

## 🌱 Impatto ambientale e sociale

| Ambito | Beneficio |
|--------|------------|
| **Emissioni CO₂** | -2,5 tonnellate/anno grazie all’uso delle e-bike |
| **Consumo carburante** | -15% rispetto alla gestione tradizionale |
| **Coinvolgimento civico** | Partecipazione e consapevolezza ecologica dei cittadini |
| **Turismo sostenibile** | Incremento delle presenze e valorizzazione del territorio |
| **Immagine pubblica** | Comune innovativo, efficiente e attento all’ambiente |

---

## 🔐 Sicurezza e gestione

- Tutto ospitato in rete **intranet comunale**  
- **Accesso limitato** al personale tecnico (`Tecnici_IA`)  
- **Backup automatici** giornalieri  
- **Gestione utenti** tramite **Keycloak + Active Directory**  
- **Nessuna dipendenza da servizi esterni** o cloud privati  

---

## 🚀 Sintesi finale

**Magliano Smart Mobility** è una piattaforma integrata e sostenibile che:
- semplifica la gestione della mobilità comunale,  
- migliora i servizi ai cittadini,  
- riduce costi e tempi,  
- promuove la trasparenza amministrativa,  
- e contribuisce concretamente agli obiettivi ambientali europei.  

---

## 📄 Licenza

Rilasciato sotto licenza **MIT**  
© 2025 Comune di Magliano in Toscana  

---

## 🙏 Crediti

### Software Open Source
- [Traccar](https://www.traccar.org/)
- [n8n](https://n8n.io/)
- [PostgreSQL](https://www.postgresql.org/)
- [Grafana](https://grafana.com/)
- [Docker](https://www.docker.com/)

### Hardware consigliato
- [Concox GT06N](https://www.concox.com/)
- [Invoxia Bike Tracker](https://www.invoxia.com/)

### Servizi pubblici integrati
- [PagoPA](https://www.pagopa.gov.it/)
- [Comune di Magliano in Toscana](https://www.comune.maglianointoscana.gr.it/)

---

> 🏛️ *“Un piccolo Comune, una grande visione: mobilità intelligente, sostenibile e condivisa.”*  
> — **Comune di Magliano in Toscana — Ufficio Innovazione e Mobilità 
