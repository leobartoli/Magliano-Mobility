# 🚗 Magliano Smart Mobility

> **Sistema open source per la gestione intelligente della mobilità pubblica e condivisa.**  
> Un progetto del **Comune di Magliano in Toscana** per una Pubblica Amministrazione più efficiente, sostenibile e digitale.

---

## 📘 Panoramica

**Magliano Smart Mobility** è una piattaforma comunale open source per la **mobilità sostenibile e la gestione flotta**.  
Unisce il **Bike Sharing gratuito e anonimo** e la **manutenzione predittiva della flotta comunale**, sfruttando GPS, geofencing e automazione intelligente tramite **n8n**.

### 🎯 Obiettivi
- 🚲 Offrire un servizio di **Bike Sharing a frizione zero**, gratuito e anonimo.  
- 🚐 Digitalizzare la **gestione della flotta comunale**.  
- 🌍 Ridurre costi e impatti ambientali.  
- 🤖 Automatizzare attività operative con un **Agente IA locale**.  

---

## ⚙️ Architettura del sistema

Il sistema è **modulare**, completamente **autonomo** e basato su tecnologie **open source**.  
Opera interamente **all’interno dell’infrastruttura comunale**, senza dipendenze da servizi cloud.

### Componenti principali
- 🛰️ **Tracker GPS** su veicoli e biciclette.  
- ⚙️ **Software Docker** per gestione e automazione.  
- 🤖 **Agente IA n8n** per la logica operativa.  
- 🗄️ **PostgreSQL/PostGIS** come database geografico.  
- 📊 **Grafana** per dashboard e trasparenza pubblica.  

---

## 🔩 Hardware consigliato

| Componente | Descrizione | Funzione |
|-------------|-------------|----------|
| **Tracker GPS** | Installato su veicoli e biciclette. | Tracciamento in tempo reale. |
| **SIM IoT (4G)** | Scheda dati con APN privato. | Trasmissione dati GPS. |
| **QR Code (e-bike)** | Sul telaio. | Link rapido al bot Telegram o alla mappa pubblica. |
| **UPS** | Gruppo di continuità. | Protezione alimentazione. |

---

## 💻 Software stack

| Software | Ruolo | Benefici |
|-----------|--------|----------|
| 🛰️ **Traccar** | Gestione GPS | Ricezione dati, Geofencing e Webhook verso n8n. |
| ⚙️ **n8n + Agente IA** | Automazione e logica | Controllo flussi, gestione allarmi e bot Telegram. |
| 🗄️ **PostgreSQL / PostGIS** | Database | Archivia percorsi, stati e log. |
| 📊 **Grafana** | Dashboard | Visualizzazione e analisi dati. |
| 🐳 **Docker Compose** | Containerizzazione | Gestione e manutenzione semplificata. |

---

## 🤖 Agente IA (n8n)

L’**Agente IA** è il cuore logico del sistema:  
riceve eventi GPS da Traccar, li elabora in tempo reale e coordina tutte le automazioni operative (bike sharing e flotta).

### Compiti principali

| Funzione | Applicazione | Descrizione |
|-----------|--------------|-------------|
| **Logica di corsa (GPS)** | Bike Sharing | Avvio/terminazione corsa basati su movimento e geofence. |
| **Allarme perimetro (furto)** | Bike Sharing | Notifica immediata via Telegram o email. |
| **Timeout e abbandono** | Bike Sharing | Rilascio automatico stato *DISPONIBILE* dopo inattività. |
| **Analisi predittiva** | Flotta Comunale | Previsione manutenzioni e anomalie. |
| **Dialogo operativo** | Entrambi | Interazione con operatori via Telegram. |
| **Alert automatici** | Entrambi | Segnalazioni di guasti e scadenze. |

---

## 🧩 Prompt dell’Agente IA

L’agente è programmato con un prompt che definisce la logica decisionale e i limiti operativi.

```text
🎯 Prompt Agente IA - Magliano Smart Mobility

Tu sei l’agente operativo del Comune di Magliano in Toscana.
Gestisci due ambiti:
  - Bike Sharing gratuito e anonimo
  - Flotta comunale con manutenzione predittiva

Regole operative:
1. Non utilizzare dati personali, solo ID veicoli/bici.
2. Tutte le informazioni provengono da Traccar e PostgreSQL/PostGIS.
3. Le azioni avvengono tramite flussi n8n.
4. Se ricevi “geofenceExit” o “deviceMoving”, aggiorna lo stato.
5. Se una bici esce dai confini comunali, invia allerta a Polizia Locale.
6. Genera report solo su richiesta esplicita.
7. Risposte brevi, tecniche e chiare per gli operatori comunali.

Esempi di comandi Telegram
	•	Mostra bici inattive da oltre 6 ore
	•	Report utilizzo flotta settimanale
	•	Allerta bici fuori geofence

⸻

🚲 Modulo A — Bike Sharing Gratuito e Anonimo

Un servizio pubblico senza app né registrazione, basato su GPS e automazione locale.

Caratteristica	Dettaglio
Modalità d’uso	Pick-up & Go: bici sempre sbloccate.
Identificazione	Nessun dato personale, solo ID bici.
Logica del servizio	Geofence e movimento gestiti da n8n.
Richiesta disponibilità	Bot Telegram /biciposto.

Flusso operativo

Fase	Evento	Azione
Disponibilità	Bici nel Geofence di rilascio.	Mostrata come “disponibile” nel bot.
Inizio corsa	geofenceExit + deviceMoving.	Avvio automatico della corsa.
Allarme furto	geofenceExit dal confine comunale.	Notifica immediata alla Polizia Locale.


⸻

🚐 Modulo B — Gestione Flotta Comunale

Gestione integrata e predittiva dei mezzi comunali.

Funzionalità	Descrizione
Tracciamento completo	Storico spostamenti e consumi.
Manutenzione predittiva	Allerta automatica su km/ore d’uso.
Ottimizzazione percorsi	Analisi utilizzo mezzi.
Alert operativi	Notifiche automatiche su guasti o anomalie.


⸻

👥 Benefici per i cittadini

Aspetto	Vantaggio
Accesso immediato	Nessuna app, nessuna registrazione.
Anonimato garantito	Nessun dato personale registrato.
Trasparenza	Dati di disponibilità consultabili pubblicamente.
Sostenibilità	Servizio gratuito, a zero emissioni e manutenzione automatizzata.


⸻

🏛️ Benefici per la Pubblica Amministrazione

Area	Beneficio
Automazione logistica	Tutti i flussi digitalizzati e monitorati.
Riduzione costi	Nessun canone, software 100% open source.
Sicurezza asset	Controllo automatico tramite Geofencing.
Autonomia tecnologica	Sistema gestito localmente, senza cloud.


⸻

🔐 Sicurezza e gestione
	•	Tutti i dati restano all’interno dell’infrastruttura comunale.
	•	Il sistema non elabora dati personali (anonimato garantito).
	•	Backup e versionamento automatici via Docker.
	•	Accesso tecnico riservato al personale interno.

⸻

🚀 Sintesi finale

Magliano Smart Mobility è una piattaforma pubblica open source, autonoma e intelligente che integra:
	•	🚲 Bike Sharing gratuito e anonimo
	•	🚐 Gestione e manutenzione flotta comunale
	•	🤖 Automazione e IA locale tramite n8n

✅ Riduce costi, tempi e burocrazia
✅ Aumenta trasparenza e fiducia
✅ Promuove sostenibilità e innovazione

⸻

📄 Licenza

Rilasciato sotto licenza MIT License
© 2025 Comune di Magliano in Toscana

⸻

🙏 Crediti

Software Open Source
	•	n8n
	•	Traccar
	•	PostgreSQL / PostGIS
	•	Grafana
	•	Docker

Hardware consigliato
	•	Concox GT06N (GPS Tracker)

⸻

🏛️ “Un piccolo Comune, una grande visione: mobilità intelligente, sostenibile e condivisa.”
Comune di Magliano in Toscana — Ufficio Innovazione e Mobilità