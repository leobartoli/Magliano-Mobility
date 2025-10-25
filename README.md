# 🚗 Magliano Smart Mobility  
> **Sistema comunale open source per la gestione intelligente della mobilità pubblica e condivisa**  
> Un progetto del **Comune di Magliano in Toscana** per una Pubblica Amministrazione più efficiente, sostenibile e digitale.  

---

## 📋 Indice
- [Obiettivo del progetto](#🎯-obiettivo-del-progetto)
- [Struttura del sistema](#⚙️-struttura-del-sistema)
- [Componenti Hardware](#🔩-componenti-hardware)
- [Componenti Software](#💻-componenti-software)
- [Gestione tramite Agente IA (n8n)](#🤖-gestione-tramite-agente-ia-n8n)
- [MODULO A: Bike Sharing Gratuito e Anonimo](#🚲-modulo-a-bike-sharing-gratuito-e-anonimo)
- [MODULO B: Gestione Flotta Comunale](#🚗-modulo-b-gestione-flotta-comunale)
- [Benefici per la Pubblica Amministrazione](#🏛️-benefici-per-la-pubblica-amministrazione)
- [Benefici per i cittadini](#👥-benefici-per-i-cittadini)
- [Sicurezza e gestione](#🔐-sicurezza-e-gestione)
- [Sintesi finale](#🚀-sintesi-finale)
- [Licenza](#📄-licenza)
- [Crediti](#🙏-crediti)

---

## 🎯 Obiettivo del progetto

**Magliano Smart Mobility** è un progetto di innovazione locale che digitalizza la gestione della flotta comunale e del **bike sharing cittadino gratuito**, integrando **tracciamento GPS** e **automazione intelligente** tramite agente IA basato su **n8n**.

### Obiettivi principali
- 🚲 Fornire un servizio di **Bike Sharing a frizione zero**: gratuito, anonimo e gestito unicamente da GPS/Geofencing.  
- 🚐 Migliorare l'efficienza amministrativa e la **manutenzione predittiva** della Flotta Comunale.  
- 🌍 Ridurre costi e impatti ambientali, favorendo una gestione **autonoma, sicura e sostenibile** dei dati.

---

## ⚙️ Struttura del sistema

Il sistema è **modulare**, unendo dispositivi fisici (tracker GPS) e software intelligente su un’unica infrastruttura.

- 🛰️ **Hardware comunale:** tracker GPS su veicoli e e-bike.  
- 🧩 **Software locale Docker:** gestione, automazione e logica.  
- 🤖 **Agente IA n8n:** automatizza decisioni operative e flussi logici.  

> Tutto opera **all’interno dell’intranet comunale**, senza dipendenza da servizi esterni.

---

## 🔩 Componenti Hardware

| Componente | Descrizione | Funzione |
|-------------|-------------|----------|
| **Tracker GPS** | Installato su ciascun veicolo e su ciascuna e-bike. | Tracciamento in tempo reale e storico spostamenti. |
| **SIM IoT dati** | Connettività 4G. | Invio dati al server Traccar. |
| **QR Code (e-bike)** | Sul telaio. | Link al Bot Telegram per richiesta disponibilità o alla mappa pubblica. |
| **Server Linux / Docker** | Server locale o Mini PC. | Esecuzione dell’intero sistema. |
| **UPS** | Gruppo di continuità. | Protezione dell’alimentazione elettrica. |

---

## 💻 Componenti Software

| Software | Ruolo | Benefici |
|-----------|--------|----------|
| 🛰️ **Traccar** | Gestione GPS | Ricezione dati veicoli, definizione Geofence e invio Webhook. |
| ⚙️ **n8n + Agente IA** | Automazione e logica | Controllo flussi, gestione allarmi, interfaccia Telegram. |
| 🗄️ **PostgreSQL / PostGIS** | Database geografico | Archivia percorsi, stato bici e log di servizio (senza dati utente). |
| 📊 **Grafana** | Dashboard | Visualizzazione trasparente dei dati di utilizzo per la PA. |
| 🔒 **Keycloak** | Autenticazione | Accesso sicuro integrato con Active Directory locale. |
| 🐳 **Docker Compose** | Contenitore unico | Gestione e manutenzione semplificata. |

---

## 🤖 Gestione tramite Agente IA (n8n)

L’agente IA è il **cuore logico** del sistema.  
Analizza in tempo reale i dati provenienti da Traccar per automatizzare attività e supportare gli operatori comunali.

### 🧠 Compiti principali

| Funzione | Applicazione | Descrizione |
|-----------|--------------|-------------|
| **Logica di corsa (GPS)** | Bike Sharing | Avvia e termina la corsa in base a Geofence e Movimento/Sosta. |
| **Allarme perimetro (furto)** | Bike Sharing | Invia un’allerta immediata (Email/Telegram) se la bici esce dai confini comunali. |
| **Timeout e abbandono** | Bike Sharing | Rilascio forzato dello stato DISPONIBILE dopo inattività (es. 6 ore). |
| **Analisi predittiva** | Flotta Comunale | Anticipa scadenze di manutenzione e usura dei veicoli. |
| **Dialogo con operatori** | Entrambi | Risponde via Telegram o pannello web alle richieste del personale. |
| **Alert automatici** | Entrambi | Segnala anomalie, guasti e scadenze. |

### 🗣️ Esempi d’uso (Operatori PA)
- “Mostra le bici con batteria tracker bassa o manutenzione scaduta.”  
- “Qual è l'ultima posizione della Bici 103? È fuori dal Geofence del Comune?”  
- “Prepara il report sull'utilizzo della Flotta Comunale nell'ultima settimana.”

---

## 🚲 MODULO A: Bike Sharing Gratuito e Anonimo

Il sistema è progettato per la **massima semplicità e accesso universale** dei cittadini.

| Caratteristica | Dettaglio |
|----------------|-----------|
| **Modalità d’uso** | Pick-up and Go: la bici è sempre sbloccata. |
| **Identificazione** | Anonima: nessuna registrazione, app o documento richiesto. |
| **Logica del servizio** | Basata unicamente su Traccar Geofencing e n8n. |
| **Servizio utente** | Richiesta disponibilità tramite Bot Telegram `/biciposto`. |

### 🔄 Flusso Logico (n8n)

| Fase | Logica n8n | Servizio Utente |
|------|-------------|----------------|
| **Disponibilità** | Bici con stato *DISPONIBILE* e dentro un Geofence di rilascio. | Utente riceve la lista (es. “Bici 101: Piazza del Municipio”). |
| **Inizio corsa** | `geofenceExit` + `deviceMoving` rilevati da Traccar. | La corsa è registrata nel database logistico. |
| **Allarme furto** | `geofenceExit` dal perimetro comunale. | Allerta immediata al Comando di Polizia. |

---

## 🚗 MODULO B: Gestione Flotta Comunale

Supporta il personale nel **monitoraggio, analisi e manutenzione predittiva** dei mezzi di proprietà comunale.

| Funzionalità | Vantaggio |
|---------------|-----------|
| **Tracciamento completo** | Storico di tutti gli spostamenti ufficiali. |
| **Manutenzione predittiva** | Allerta automatica basata su km percorsi o ore di utilizzo. |
| **Ottimizzazione flotta** | Analisi dei percorsi più frequenti e utilizzo dei mezzi. |
| **Accesso sicuro** | Dashboard protetta da Keycloak (solo personale PA). |

---

## 👥 Benefici per i cittadini

| Aspetto | Vantaggio |
|----------|-----------|
| **Accesso rapido** | Nessuna app da scaricare, nessuna registrazione. |
| **Trasparenza** | Dati di posizione e disponibilità pubblici. |
| **Affidabilità** | Il Bot Telegram mostra solo bici correttamente rilasciate. |
| **Innovazione** | Servizio moderno, digitale e completamente gratuito. |

---

## 🏛️ Benefici per la Pubblica Amministrazione

| Area | Beneficio |
|-------|------------|
| **Gestione automatica** | Tutti i flussi logici digitalizzati e tracciati. |
| **Riduzione costi** | Nessun canone software, gestione autonoma. |
| **Sicurezza del patrimonio** | Allarmi geofencing per proteggere i mezzi. |
| **Indipendenza tecnologica** | Tutto open source e gestito internamente. |

---

## 🔐 Sicurezza e gestione

- Tutti i dati restano **all’interno dell’intranet comunale**.  
- **Anonimato totale** per gli utenti del Bike Sharing.  
- Accesso amministrativo gestito tramite **Keycloak + Active Directory locale**.  
- **Backup automatici** e versionamento container Docker.  

---

## 🚀 Sintesi finale

**Magliano Smart Mobility** è una piattaforma comunale **open source**, **autonoma** e **intelligente** che unisce:

- 🚲 Mobilità sostenibile (Bike Sharing gratuito e anonimo)  
- 🚐 Digitalizzazione amministrativa (Gestione Flotta PA)  
- 🤖 Intelligenza artificiale operativa (Logica di n8n)  

✅ Riduce costi, tempi e burocrazia  
✅ Aumenta trasparenza e fiducia  
✅ Promuove sostenibilità e innovazione  

---

## 📄 Licenza
Rilasciato sotto licenza **MIT License**  
© 2025 **Comune di Magliano in Toscana**

---

## 🙏 Crediti

### Software Open Source
- n8n  
- Traccar  
- PostgreSQL / PostGIS  
- Grafana  
- Docker  
- Keycloak  

### Hardware consigliato
- Concox GT06N  

---

> 🏛️ *“Un piccolo Comune, una grande visione: mobilità intelligente, sostenibile e condivisa.”*  
> **Comune di Magliano in Toscana — Ufficio Innovazione e Mobilità**