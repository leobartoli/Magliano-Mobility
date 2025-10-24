# 🚗 Magliano Smart Mobility  
### Gestione Open Source della Flotta Comunale e Pagamenti PagoPA

## 📍 Comune di Magliano in Toscana
Sistema **open source**, **self-hosted** e **conforme a PagoPA**, per la gestione e il monitoraggio della flotta comunale:
- 🚗 Mezzi di servizio (auto, furgoni, veicoli tecnici)  
- 🚲 E-bike per bike sharing comunale  
- 💳 Pagamenti automatici tramite **PagoPA**

Il sistema è progettato per funzionare **in rete locale (intranet)**, con dati gestiti interamente dal Comune.

---

## 🎯 Obiettivi
- Gestione unificata di tutti i mezzi comunali
- Monitoraggio in tempo reale (GPS, stato, manutenzioni)
- Automazione dei flussi operativi con **n8n**
- Pagamenti digitali via **PagoPA**
- Sicurezza e tracciabilità dei dati
- Compatibilità con **Active Directory / Keycloak**

---

## ⚙️ Architettura

| Componente | Funzione | Tecnologia |
|-------------|-----------|------------|
| **Traccar** | Rilevamento posizione GPS e stato veicoli | Docker `traccar/traccar` |
| **n8n** | Orchestrazione flussi (noleggi, allarmi, pagamenti) | Docker `n8nio/n8n` |
| **PagoPA Proxy** | Integrazione open source con PagoPA | Docker `pagopa-proxy` |
| **PostgreSQL** | Database unico per mezzi, utenti, pagamenti | Docker `postgres:16` |
| **Adminer** | Gestione grafica database | Docker `adminer` |

---

## 🧭 Funzionamento

### 🚗 Mezzi Comunali
1. Ogni mezzo ha un dispositivo GPS compatibile con Traccar.  
2. Traccar invia dati di posizione, velocità e diagnostica.  
3. n8n riceve notifiche di eventi (geofence, manutenzione, anomalie).  
4. Le notifiche arrivano via Telegram o Email al responsabile.  
5. Tutti i log vengono archiviati nel database PostgreSQL.

### 🚲 E-Bike Comunali
1. Le e-bike inviano automaticamente posizione e stato batteria.  
2. Quando un’area di parcheggio viene lasciata, n8n registra **inizio noleggio**.  
3. Al rientro in area, viene registrata la **fine noleggio**.  
4. n8n calcola la tariffa in base alla durata e genera un **IUV PagoPA**.  
5. L’utente riceve via Telegram/Email il link di pagamento PagoPA.  
6. Dopo il pagamento, il sistema aggiorna lo stato a **PAGATO** e archivia la ricevuta.

---

## 💳 Integrazione PagoPA

### Flusso Automatico
| Step | Azione |
|------|---------|
| 1️⃣ | Fine noleggio segnalata da Traccar |
| 2️⃣ | n8n calcola importo e genera **IUV** |
| 3️⃣ | Invio al gateway `pagopa-proxy` |
| 4️⃣ | Creazione posizione debitoria PagoPA |
| 5️⃣ | Invio link all’utente (Telegram o Email) |
| 6️⃣ | Verifica pagamento e archiviazione ricevuta |

### Esempio struttura tabella `pagamenti`
```sql
CREATE TABLE pagamenti (
  id SERIAL PRIMARY KEY,
  utente_id INT,
  mezzo_id INT,
  iuv VARCHAR(40) UNIQUE,
  importo NUMERIC(10,2),
  stato VARCHAR(20) DEFAULT 'IN_ATTESA',
  data_inizio TIMESTAMP,
  data_fine TIMESTAMP,
  data_pagamento TIMESTAMP,
  ricevuta_url TEXT
);
