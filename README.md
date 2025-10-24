# 🌐 Magliano Smart Mobility – Gestione Flotta Comunale e Pagamenti PagoPA

## 📍 Comune di Magliano in Toscana
Sistema integrato per la **gestione, tracciamento e tariffazione automatica** della flotta comunale:
- 🚗 Mezzi di servizio (auto, furgoni, Protezione Civile)
- 🚲 E-Bike per il servizio di bike-sharing cittadino
- 💳 Pagamenti digitali tramite **PagoPA**, in linea con le Linee Guida AgID

L’intero sistema è **open source**, **self-hosted** e accessibile solo sulla rete comunale.

---

## 🎯 Obiettivi Principali
- **Gestione unificata** della flotta comunale (veicoli e e-bike)
- **Monitoraggio in tempo reale** dei mezzi (posizione, velocità, stato)
- **Automazione completa** delle notifiche, allarmi e manutenzioni
- **Pagamenti digitali** automatici per i noleggi tramite PagoPA
- **Trasparenza e tracciabilità** per tutti i servizi di mobilità comunale

---

## ⚙️ Architettura Open Source

| Componente | Funzione | Tecnologia |
|-------------|-----------|------------|
| **Traccar** | Server di tracking per mezzi e e-bike | Docker `traccar/traccar` |
| **n8n** | Automazione flussi (noleggi, manutenzioni, pagamenti) | Docker `n8nio/n8n` |
| **PagoPA Proxy** | Gateway open source verso PagoPA | Docker `pagopa-proxy` |
| **PostgreSQL** | Database unico per noleggi, utenti, pagamenti | Docker `postgres:16` |
| **Adminer** | Pannello di amministrazione database | Docker `adminer` |

---

## 🧭 Funzionamento Semplificato

### 🚗 Mezzi Comunali
1. Ogni veicolo è dotato di tracker GPS/OBD-II compatibile con Traccar.  
2. Traccar raccoglie posizione, velocità, diagnostica motore.  
3. n8n riceve avvisi automatici (es. velocità eccessiva, uscita geofence).  
4. Gli alert vengono inviati via Telegram/Email al responsabile.  
5. Tutti gli eventi sono salvati nel database per la reportistica.

### 🚲 E-Bike per Bike-Sharing
1. Le e-bike sono equipaggiate con tracker GPS/GSM.  
2. Traccar segnala automaticamente **uscite/ingressi** dalle aree di parcheggio (geofence).  
3. n8n interpreta gli eventi come **inizio o fine noleggio**.  
4. Al termine del noleggio viene calcolata la tariffa e generato un **IUV PagoPA**.  
5. L’utente riceve via Telegram o email il link per pagare con PagoPA.  
6. Una volta effettuato il pagamento, n8n aggiorna il database e chiude la sessione.

---

## 💳 Pagamento PagoPA Integrato

### Flusso di Pagamento
| Step | Azione |
|------|---------|
| 1️⃣ | Traccar → n8n segnala “Fine noleggio” |
| 2️⃣ | n8n calcola durata e costo |
| 3️⃣ | Generazione **IUV** (Identificativo Univoco di Versamento) |
| 4️⃣ | Invio dati al gateway `pagopa-proxy` |
| 5️⃣ | L’utente riceve link PagoPA (via Telegram o Email) |
| 6️⃣ | Dopo il pagamento, stato aggiornato a `PAGATO` e ricevuta archiviata |

### Esempio di tabella `pagamenti`
```sql
CREATE TABLE pagamenti (
  id SERIAL PRIMARY KEY,
  user_id INT,
  mezzo_id INT,
  iuv VARCHAR(40) UNIQUE,
  importo NUMERIC(10,2),
  stato VARCHAR(20) DEFAULT 'IN_ATTESA',
  data_inizio TIMESTAMP,
  data_fine TIMESTAMP,
  data_pagamento TIMESTAMP,
  ricevuta_url TEXT
);
