
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

**Magliano Smart Mobility** è un progetto di innovazione locale che digitalizza la gestione della **flotta comunale** e del **bike sharing cittadino**, integrando tracciamento GPS, pagamenti PagoPA e automazione dei processi con un **agente IA** basato su **n8n**.

L’obiettivo è:
- migliorare l’efficienza amministrativa e ridurre i tempi operativi,  
- abbattere costi di gestione e sprechi,  
- promuovere una mobilità sostenibile e trasparente,  
- offrire ai cittadini un servizio accessibile e moderno.

---

## ⚙️ Struttura del sistema

Il sistema è composto da due parti principali:

1. **Hardware fisico**: dispositivi GPS, QR code, sensori e punti di ricarica.  
2. **Software gestionale con agente IA**: piattaforma locale in Docker, con automazione n8n e analisi in tempo reale.

Tutti i dati restano **intranet**, sotto il pieno controllo del Comune.

---

## 🔩 Componenti Hardware

### 🚗 Flotta comunale

| Componente | Descrizione | Funzione |
|-------------|-------------|----------|
| **Tracker GPS (Concox GT06N)** | Installato sui veicoli comunali | Tracciamento automatico in tempo reale |
| **SIM IoT dati** | Connettività 4G minima | Invio dei dati GPS al server comunale |
| **Alimentazione 12V** | Collegamento diretto all’auto | Funzionamento continuo |
| **QR interno** | Etichetta con scheda veicolo | Accesso rapido via smartphone |

### 🚲 Bike sharing comunale

| Componente | Descrizione | Funzione |
|-------------|-------------|----------|
| **E-bike con tracker GPS** | Bicicletta elettrica comunale | Tracciamento e sicurezza |
| **QR code** | Codice adesivo sul manubrio | Prenotazione e pagamento via smartphone |
| **Lucchetto elettronico** | Controllato via software | Sblocco solo dopo pagamento PagoPA |

### 🏢 Infrastruttura comunale

| Componente | Descrizione | Funzione |
|-------------|-------------|----------|
| **Server Linux / Docker** | Mini PC o server del Comune | Ospita il sistema Smart Mobility |
| **Rete LAN / Intranet** | Connessione interna | Sicurezza e affidabilità |
| **UPS** | Gruppo di continuità | Protezione elettrica |
| **Modulo PagoPA** | Gateway ufficiale o simulato | Pagamenti sicuri e tracciabili |

---

## 💻 Componenti Software

| Software | Ruolo | Benefici |
|-----------|--------|-----------|
| 🛰️ **Traccar** | Server GPS open source | Riceve e memorizza i dati dei veicoli |
| ⚙️ **n8n + Agente IA** | Automazione e assistenza intelligente | Analizza dati, risponde, propone azioni |
| 🗄️ **PostgreSQL / PostGIS** | Database geografico | Archivia percorsi e zone |
| 📊 **Grafana** | Dashboard pubbliche e interne | Statistiche, percorsi, emissioni |
| 🔒 **Keycloak** | Gestione identità e accessi | Integrazione AD comunale |
| 💳 **PagoPA Proxy** | Gateway o mock | Gestione automatica dei pagamenti |
| 🐳 **Docker Compose** | Contenitore unificato | Facilità di gestione e backup |

---

## 🤖 Gestione tramite Agente IA (n8n)

Il sistema include un **agente intelligente** integrato in **n8n**, che supporta il personale comunale nelle attività quotidiane di mobilità e manutenzione.

### 🧠 Compiti dell’agente IA

| Funzione | Descrizione |
|-----------|-------------|
| **Analisi predittiva** | Prevede scadenze di manutenzione, consumo carburante e anomalie nei percorsi |
| **Assistenza decisionale** | Suggerisce la redistribuzione delle e-bike in base ai dati di utilizzo |
| **Elaborazione automatica dati GPS** | Traduce i log di Traccar in report leggibili e pronti per il dirigente |
| **Dialogo con gli operatori** | Interazione via Telegram o pannello n8n per richieste tipo: “Mostra chilometri percorsi oggi dalla Panda AB123CD” |
| **Gestione manutenzioni** | Genera automaticamente segnalazioni o prenotazioni officina |
| **Interfaccia naturale** | Il personale può scrivere comandi in linguaggio naturale, l’agente esegue le azioni |

### 🗣️ Esempi d’uso

- “Quanti chilometri ha percorso la Punto del cantiere stradale questa settimana?”  
- “Mostrami le e-bike più usate nel mese di agosto.”  
- “Segnala un alert se un mezzo resta acceso oltre 2 ore consecutive.”  
- “Genera il report settimanale dei movimenti della flotta e invialo alla PEC mobilità.”

### 🔧 Benefici dell’Agente IA

| Area | Vantaggio |
|------|------------|
| **Efficienza operativa** | Riduzione drastica delle attività manuali di inserimento dati |
| **Decisioni basate su dati reali** | Suggerimenti e alert proattivi |
| **Formazione ridotta** | Interfaccia in linguaggio naturale, accessibile anche a personale non tecnico |
| **Ottimizzazione risorse** | Gestione automatica di flotte, turni e manutenzioni |
| **Semplificazione amministrativa** | Report e analisi pronti per la trasmissione agli uffici competenti |

---

## 🔄 Funzionamento operativo

### 🚗 Flotta comunale

1. L’autista accende il veicolo → il GPS invia i dati a Traccar.  
2. L’agente IA in n8n elabora automaticamente tragitti, soste e consumi.  
3. Se viene superata una soglia (es. 10.000 km), viene generato un alert manutenzione.  
4. Grafana e il dirigente possono visualizzare tutto in tempo reale.  

### 🚲 Bike sharing comunale

1. Il cittadino inquadra il QR code e prenota una bici.  
2. L’agente IA verifica disponibilità, genera un **IUV PagoPA**, e sblocca la bici dopo il pagamento.  
3. Durante il noleggio, il sistema registra posizione, durata e costo.  
4. Alla restituzione, tutto viene archiviato automaticamente.  

---

## 🏛️ Benefici per la Pubblica Amministrazione

| Area | Beneficio concreto |
|------|--------------------|
| **Efficienza** | Dati e automazioni riducono fino al 50% il tempo di gestione della flotta. |
| **Riduzione costi** | -15% carburante e manutenzioni; nessun canone software. |
| **Digitalizzazione** | Tutti i flussi (prenotazioni, pagamenti, manutenzioni) sono digitali. |
| **Trasparenza** | Dati accessibili in tempo reale, anche ai cittadini. |
| **Pianificazione intelligente** | L’agente IA fornisce analisi predittive su utilizzo e consumi. |
| **Autonomia** | Sistema locale, open source e autosufficiente. |

---

## 👥 Benefici per i cittadini

| Aspetto | Vantaggio |
|----------|------------|
| **Accesso semplice** | Prenotazione e pagamento in un solo passaggio, via QR. |
| **Pagamenti sicuri** | Gestione integrata con PagoPA. |
| **Comunicazione diretta** | Ricezione di notifiche, promozioni e aggiornamenti via Telegram. |
| **Servizi efficienti** | Nessuna attesa: sistema automatico e sempre disponibile. |
| **Partecipazione civica** | I dati aperti incentivano trasparenza e fiducia nella PA. |

---

## 🌱 Impatto ambientale e sociale

| Ambito | Beneficio |
|--------|------------|
| **Emissioni CO₂** | -2,5 tonnellate/anno grazie a mobilità elettrica e ottimizzazione viaggi |
| **Consumi** | -15% carburante e chilometri a vuoto |
| **Turismo sostenibile** | Incremento delle presenze grazie alle e-bike comunali |
| **Immagine pubblica** | Comune innovativo, efficiente e “verde” |
| **Partecipazione** | Coinvolgimento della comunità e delle scuole in progetti Smart City |

---

## 🔐 Sicurezza e gestione

- Tutti i dati **restano nella rete comunale (intranet)**  
- **Accesso controllato** tramite Keycloak e Active Directory  
- **Backup giornalieri automatici** su server comunale  
- **Nessun cloud esterno** o servizio commerciale  
- **Conformità GDPR** e tracciabilità completa dei dati  

---

## 🚀 Sintesi finale

**Magliano Smart Mobility** rappresenta un modello replicabile di innovazione per i piccoli comuni italiani:

✅ Gestione automatica e intelligente della mobilità pubblica  
✅ Integrazione completa con PagoPA e sistemi comunali  
✅ Decisioni guidate dai dati e dall’agente IA  
✅ Risparmio economico e ambientale concreto  
✅ Trasparenza e qualità del servizio ai cittadini  

---

## 📄 Licenza

Rilasciato sotto licenza **MIT**  
© 2025 Comune di Magliano in Toscana  

---

## 🙏 Crediti

### Software Open Source
- [Traccar](https://www.traccar.org/)
- [n8n](https://n8n.io/)
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
> — **Comune di Magliano in Toscana — Settore Tecnico
