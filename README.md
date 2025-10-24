# 🌐 Progetto **Magliano Smart Mobility**
### Piattaforma Unificata di Tracciamento e Automazione *(Smart Fleet)*  
📍 **Comune di Magliano in Toscana**

Questo repository implementa una piattaforma centralizzata per il **monitoraggio e la gestione operativa** di tutti i mezzi del Comune  
(flotta di servizio, e-bike per bike-sharing, ecc.), utilizzando **esclusivamente software open source auto-ospitati (self-hosted)**.

---

## 📖 Sommario
- [🎯 Obiettivi del Progetto Esteso](#-obiettivi-del-progetto-esteso)
- [⚙️ Architettura Tecnologica (Open Source Stack)](#️-architettura-tecnologica-open-source-stack)
- [🚀 Guida all’Installazione (Docker Compose)](#-guida-allinstallazione-docker-compose)
- [💻 Flussi di Lavoro n8n](#-flussi-di-lavoro-n8n)
  - [🚖 Monitoraggio Flotta Comunale di Servizio](#-flusso-monitoraggio-flotta-comunale-di-servizio)
  - [🚲 Gestione Bike-Sharing (Check-in / Check-out)](#-flusso-gestione-bike-sharing-check-in--check-out)
  - [🔋 Notifica Manutenzione (Batteria E-Bike)](#-flusso-notifica-manutenzione-batteria-e-bike)
- [📈 Vantaggi per il Comune di Magliano](#-vantaggi-per-il-comune-di-magliano)
- [📬 Contatti](#-contatti)

---

## 🎯 Obiettivi del Progetto Esteso

- **Gestione Unificata della Flotta Comunale (Veicoli + E-Bike)**  
  Un unico punto di controllo per localizzazione, manutenzione e utilizzo di tutti gli asset motorizzati.

- **Monitoraggio Bike-Sharing Cittadino (E-Bike)**  
  Automatizzazione dei processi operativi di noleggio/condivisione (check-in/out, tariffazione, stato di carica).

- **Ottimizzazione Servizi Pubblici**  
  Miglioramento dell’efficienza dei percorsi dei mezzi di servizio e documentazione dei tempi di intervento (es. Protezione Civile).

- **Trasparenza e Analisi**  
  Fornitura di dati utili per ottimizzare i costi, migliorare la sicurezza e supportare la pianificazione urbanistica.

---

## ⚙️ Architettura Tecnologica (Open Source Stack)

L'infrastruttura di base è invariata, ma il ruolo di **Traccar** e **n8n** viene esteso.

| **Componente** | **Ruolo** | **Tecnologia** | **Dati Trattati** |
|----------------|------------|----------------|-------------------|
| **Flotta E-Bike** | Veicoli per Bike-Sharing | Tracker GPS/GSM (occultati) | Posizione, Velocità, Batteria Tracker |
| **Flotta Servizi** | Auto, Furgoni, Mezzi P.C. | Tracker OBD-II o cablati (Traccar-compatibili) | Posizione, Diagnostica Motore |
| **Traccar** | Server di tracking open source. Piattaforma unificata per tutti i dispositivi. | Docker (`traccar/traccar`) | Dati geospaziali (storico, geofencing, allarmi) |
| **n8n** | Automazione e logica di business: allerta manutenzione, logica bike-sharing, reportistica | Docker (`n8nio/n8n`) | Flussi di lavoro, integrazioni (DB, email, Telegram) |
| **Database Esterno** | Archivio storico, log noleggi, anagrafica utenti | PostgreSQL / MariaDB | Log noleggio, utenti, storico viaggi |

---

## 🚀 Guida all’Installazione (Docker Compose)

Il file `docker-compose.yml` è il cuore del sistema e rimane valido.  
Si raccomanda di aggiungere un **database dedicato** per i dati del bike-sharing gestiti da n8n.

### 1. File `docker-compose.yml`
Il file precedentemente definito (PostgreSQL, Traccar, n8n) è sufficiente,  
a patto che Traccar e n8n utilizzino lo stesso database o che n8n abbia accesso a un database separato per la logica di business.  
> 🔧 *Vedi la sezione precedente per la configurazione del `docker-compose.yml`.*

### 2. Configurazione Aggiuntiva Traccar
- **Aggiungere tutti i veicoli:** ogni mezzo (e-bike, furgone A, auto B) deve essere registrato come dispositivo, utilizzando l’IMEI del rispettivo tracker.  
- **Geofencing:** creare aree delimitate per:  
  - Aree di ritiro/riconsegna e-bike  
  - Sede comunale / parcheggi autorizzati  
  - Limiti amministrativi (monitoraggio uso mezzi di servizio)

---

## 💻 Flussi di Lavoro n8n

n8n utilizza le **API REST di Traccar** per accedere allo stato dei dispositivi e implementare la logica specifica per le due flotte (servizio e bike-sharing).

---

### 🚖 Flusso: Monitoraggio Flotta Comunale di Servizio

| **Step** | **Ruolo** | **Logica** | **Dati Usati** |
|-----------|------------|-------------|----------------|
| **Trigger** | Webhook | Attivato da Traccar (es. Geofence Out, velocità eccessiva) | `/traccar/api/notifications` |
| **Filtro** | If | Se (Tipo_Mezzo == “Auto Servizio”) e Velocità > 90 km/h | Posizione, Velocità, Tipo Mezzo |
| **Azione 1** | Telegram / Email | Invia notifica immediata al responsabile flotta | ID Veicolo, Tempo, Posizione |
| **Azione 2** | Database | Registra l’evento per reportistica o analisi disciplinare | Data/Ora, Violazione, Conducente |

---

### 🚲 Flusso: Gestione Bike-Sharing (Check-in / Check-out)

| **Step** | **Ruolo** | **Logica** | **Dati Usati** |
|-----------|------------|-------------|----------------|
| **Trigger** | Webhook (Traccar) | Attivato all’ingresso/uscita da Geofence (stazione di noleggio) | `/traccar/api/notifications` |
| **Check-out (Inizio)** | If | Se Evento = Uscita da Geofence “Stazione X” e bici è disponibile | ID E-Bike, Tempo |
| **Azione n8n** | Database / API | Registra inizio noleggio, invia SMS/Email all’utente | Utente ID, Ora Inizio |
| **Check-in (Fine)** | If | Se Evento = Ingresso in Geofence “Stazione X” e bici è in uso | ID E-Bike, Tempo |
| **Azione n8n** | Function / Database | Calcola durata, tariffa, invia riepilogo e aggiorna disponibilità | Ora Fine, Durata, Costo |

---

### 🔋 Flusso: Notifica Manutenzione (Batteria E-Bike)

| **Step** | **Ruolo** | **Logica** | **Dati Usati** |
|-----------|------------|-------------|----------------|
| **Trigger** | Schedule Trigger | Controllo ogni ora | N/A |
| **Controllo Stato** | HTTP Request | Interroga Traccar per stato E-Bike | `device_battery_level` |
| **Filtro** | If | Se (Tipo_Mezzo == E-Bike) e Batteria < 20% | ID E-Bike, Livello Batteria |
| **Azione** | Telegram / Email | Invia allarme al team manutenzione con link mappa | ID E-Bike, Posizione |

---

## 📈 Vantaggi per il Comune di Magliano

- **Tracciamento Unico** → Nessuna necessità di usare piattaforme diverse: Traccar gestisce tutto.  
- **Massima Personalizzazione** → n8n permette logiche di tariffazione, allertistica e reportistica su misura.  
- **Costi Contenuti** → Si paga solo l’hosting e i piani dati delle SIM.  
- **Privacy Garantita** → I dati restano completamente sotto il controllo del Comune.

---

## 📬 Contatti

- **Sviluppatore / Manutentore Tecnico:** [Il tuo Nome / Ruolo]  
- **Ufficio di Riferimento Comunale:** *Ufficio Mobilità Sostenibile*  

---
