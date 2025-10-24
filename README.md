🌐 Progetto "Magliano Smart Mobility": Piattaforma Unificata di Tracciamento e Automazione (Smart Fleet)
📍 Comune di Magliano in Toscana
Questo repository implementa una piattaforma centralizzata per il monitoraggio e la gestione operativa di tutti i mezzi del Comune (flotta di servizio, e-bike per bike-sharing, ecc.), utilizzando esclusivamente software open source auto-ospitati (self-hosted).
🎯 Obiettivi del Progetto Esteso
 * Gestione Unificata della Flotta Comunale (Veicoli + E-Bike): Avere un unico punto di controllo per la localizzazione, la manutenzione e l'utilizzo di tutti gli asset motorizzati.
 * Monitoraggio Bike-Sharing Cittadino (E-Bike): Automatizzare i processi operativi del servizio di noleggio/condivisione delle e-bike (check-in/out, tariffazione, stato di carica).
 * Ottimizzazione Servizi Pubblici: Migliorare l'efficienza dei percorsi dei mezzi di servizio e documentare i tempi di intervento (es. Protezione Civile).
 * Trasparenza e Analisi: Fornire dati per ottimizzare i costi, migliorare la sicurezza e supportare la pianificazione urbanistica.
⚙️ Architettura Tecnologica (Open Source Stack)
L'infrastruttura di base è invariata, ma il ruolo di Traccar e n8n viene espanso:
| Componente | Ruolo | Tecnologia | Dati Trattati |
|---|---|---|---|
| Flotta E-Bike | Veicoli per Bike-Sharing. | Tracker GPS/GSM (Occultati) | Posizione, Velocità, Batteria Tracker. |
| Flotta Servizi | Auto, Furgoni, Mezzi P.C. | Tracker OBD-II o cablati (Traccar-compatibili) | Posizione, Diagnostica Motore (su alcuni modelli). |
| Traccar | Server di Tracking Open Source. Piattaforma unificata per tutti i dispositivi. | Docker (traccar/traccar) | Dati Geospaziali (Storico, Geofencing, Allarmi). |
| n8n | Piattaforma di Automazione e Logica di Business. Gestisce: 1. Allerte Manutenzione, 2. Logica di Noleggio Bike-Sharing, 3. Reportistica. | Docker (n8nio/n8n) | Flussi di lavoro, Integrazione (Database, Email, Telegram). |
| Database Esterno | (Nuovo) Database per l'archiviazione dei dati storici, dei log di noleggio e dell'anagrafica utenti. | PostgreSQL / MariaDB (opzionale a fianco di Traccar) | Log Noleggio, Utenti, Storico Viaggi. |
🚀 Guida all'Installazione (Docker Compose Aggiornato)
Il file docker-compose.yml è il cuore del sistema e rimane valido. Si raccomanda di aggiungere un Database dedicato per i dati del Bike-Sharing gestiti da n8n.
1. File docker-compose.yml
Il file precedentemente definito (postgres, traccar, n8n) è sufficiente, a patto che Traccar e n8n utilizzino lo stesso database o che n8n abbia accesso a un database per la sua logica di business.
(Vedi la sezione precedente per la configurazione del docker-compose.yml)
2. Configurazione Aggiuntiva Traccar
 * Aggiungere tutti i Veicoli: In Traccar, ogni mezzo comunale (e-bike, furgone A, auto B) deve essere aggiunto come nuovo dispositivo, utilizzando l'IMEI del rispettivo tracker.
 * Geofencing: Creare aree delimitate (Geofence) in Traccar per:
   * Aree di Ritiro/Riconsegna E-Bike.
   * Sede Comunale/Parcheggi Autorizzati.
   * Limiti Amministrativi (per monitorare l'uso dei veicoli di servizio).
💻 Flussi di Lavoro n8n per la Gestione (Workflow Essenziali)
n8n utilizza le API REST di Traccar per accedere allo stato dei dispositivi e implementare la logica specifica per le due flotte (Servizio e Bike-Sharing).
A. 🚖 Flusso: Monitoraggio Flotta Comunale di Servizio
| Step | Ruolo | Logica | Dati Usati |
|---|---|---|---|
| Trigger | Webhook | Attivato da Traccar se si verifica un allarme (es. Geofence Out, Velocità Eccessiva). | traccar/api/notifications |
| Filtro | If | Se (Tipo_Mezzo == Auto Servizio) E Velocità > 90 km/h. | Posizione, Velocità, Tipo Mezzo. |
| Azione 1 | Telegram/Email | Invia notifica immediata al responsabile della flotta. | ID Veicolo, Tempo, Posizione (Link Mappa). |
| Azione 2 | Database | Log dell'evento nel Database per la reportistica disciplinare o statistica. | Data/Ora, Violazione, Conducente (se noto). |
B. 🚲 Flusso: Gestione Bike-Sharing (Check-in/Check-out)
Questo flusso gestisce la tariffazione e lo stato di disponibilità delle E-Bike.
| Step | Ruolo | Logica | Dati Usati |
|---|---|---|---|
| Trigger | Webhook (Traccar) | Attivato da Traccar quando un'E-Bike entra o esce da una Geofence (la stazione di noleggio). | traccar/api/notifications |
| Check-out (Inizio Noleggio) | If | Se (Evento == Uscita da Geofence 'Stazione X') E Stato_Bici == Disponibile. | ID E-Bike, Tempo. |
| Azione n8n | Database/API Esterna | Registra l'inizio del noleggio: Segna la bici come In Uso nel database/sistema di gestione utenti. Invia SMS/Email all'utente con l'ora di inizio. | Utente ID, Ora Inizio. |
| Check-in (Fine Noleggio) | If | Se (Evento == Ingresso in Geofence 'Stazione X') E Stato_Bici == In Uso. | ID E-Bike, Tempo. |
| Azione n8n | Function/Database | Calcola Tariffa e Termina Noleggio: Calcola la durata totale, applica la tariffa e invia il riepilogo del costo all'utente. Segna la bici come Disponibile e aggiorna lo storico. | Ora Fine, Durata, Costo. |
C. 🔋 Flusso: Notifica Manutenzione (Carica E-Bike)
| Step | Ruolo | Logica | Dati Usati |
|---|---|---|---|
| Trigger | Schedule Trigger | Esegue il controllo ogni ora. | N/A |
| Controllo Stato | HTTP Request | Interroga Traccar per tutti i dispositivi E-Bike. | device_battery_level (batteria del tracker) |
| Filtro | Filter/If | Filtra i risultati: Se (Tipo_Mezzo == E-Bike) E Batteria_Tracker < 20%. | ID E-Bike, Livello Batteria. |
| Azione | Telegram/Email | Invia allarme al team di manutenzione: "E-Bike [ID] necessita di ricarica/manutenzione. Ultima posizione: [Link Mappa]". | ID E-Bike, Posizione. |
📈 Vantaggi per il Comune di Magliano
 * Tracciamento Unico: Non è necessario formare il personale su piattaforme diverse (una per i mezzi, una per le e-bike). Traccar gestisce tutto.
 * Massima Personalizzazione: n8n permette al Comune di creare logiche di tariffazione, allertistica e reportistica personalizzate in base alle normative locali, senza vincoli di licenze software proprietarie.
 * Costi Contenuti: Si paga solo l'hosting del server (VPS) e i piani dati delle SIM card, eliminando costose licenze software per la gestione flotte.
 * Privacy: I dati di tracciamento dei cittadini (bike-sharing) restano sotto il controllo del Comune.
Contatti:
 * Sviluppatore/Manutentore Tecnico: [Il tuo Nome/Ruolo]
 * Ufficio di Riferimento Comunale: [Ufficio Mobilità Sostenibile]

