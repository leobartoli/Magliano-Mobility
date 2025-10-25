```markdown
# 🚗 Magliano Smart Mobility

> Sistema **open source** e **self-hosted** per la gestione della flotta comunale e bike sharing con pagamenti PagoPA

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Docker](https://img.shields.io/badge/Docker-Ready-2496ED?logo=docker)](https://www.docker.com/)
[![PagoPA](https://img.shields.io/badge/PagoPA-Compatible-0066CC)](https://www.pagopa.it/)

---

## 📋 Indice

- [Cos'è](#-cosè)
- [Come Funziona](#-come-funziona)
- [Architettura](#️-architettura)
- [Hardware GPS](#-hardware-gps)
- [Installazione](#-installazione)
- [Configurazione](#️-configurazione)
- [Utilizzo](#-utilizzo)
- [Manutenzione](#-manutenzione)
- [FAQ](#-faq)

---

## 🎯 Cos'è

Sistema completo per gestire automaticamente la flotta comunale e il bike sharing del Comune di Magliano in Toscana.

### Funzionalità Principali

| Funzione | Descrizione |
|----------|-------------|
| 🚗 **Tracciamento Flotta** | GPS real-time su auto, furgoni e mezzi tecnici |
| 🚲 **Bike Sharing** | Gestione automatica noleggi e-bike con pagamento |
| 💳 **Pagamenti PagoPA** | Integrazione nativa per incassi conformi |
| 📍 **Geofencing** | Alert automatici uscita zone autorizzate |
| 🔔 **Notifiche** | Telegram per eventi, scadenze e anomalie |
| 📊 **Report** | Dashboard utilizzo flotta e incassi |

### Vantaggi

- ✅ **Self-hosted**: tutti i dati sotto controllo comunale
- ✅ **Open source**: nessun vendor lock-in
- ✅ **Automatico**: zero operazioni manuali
- ✅ **Economico**: ~40€/anno per veicolo
- ✅ **Sicuro**: ZeroTier + Cloudflare

---

## 🔄 Come Funziona

### 🚗 Flotta Comunale (Auto e Furgoni)
```

GPS Tracker (Concox GT06N) installato su veicolo
↓
Invia posizione ogni 30 secondi quando acceso
↓
Traccar riceve e memorizza: posizione, km, velocità
↓
Sistema registra automaticamente:
• Percorsi giornalieri
• Chilometraggi per mezzo e conducente
• Soste e utilizzo
↓
Notifiche automatiche per:
• Uscita zone autorizzate (geofencing)
• Scadenze manutenzione (tagliando, revisione)
• Anomalie (eccesso velocità, soste anomale)

```
**Esempio Pratico:**

Mario Rossi dell'Ufficio Tecnico usa la Panda AB123CD:
- **09:00** - Accende auto → GPS inizia tracciamento
- **09:30** - Percorso Municipio → Cantiere Via Roma (12 km)
- **12:30** - Auto esce confine comunale → **Alert Telegram** al responsabile
- **17:00** - Spegne auto → Sistema registra **42 km totali**
- **17:05** - Aggiorna database: km giornalieri Mario, km totali veicolo
- Se mancano <500 km a tagliando → **Notifica manutenzioni**

---

### 🚲 E-Bike Sharing Cittadini

#### Flusso Completo Noleggio

**1️⃣ INIZIO NOLEGGIO**
```

Cittadino prende e-bike dal parcheggio
↓
Invoxia rileva uscita da geofence → Webhook a n8n
↓
n8n registra nel database:
• ID utente (da prenotazione/tessera)
• ID e-bike
• Timestamp inizio
↓
Telegram invia conferma immediata:
“🚲 Noleggio iniziato! E-Bike #003
Ore 14:23 - Buon viaggio!”

```
**2️⃣ DURANTE IL NOLEGGIO**
```

Invoxia invia posizione ogni 5 minuti
↓
Sistema monitora:
• Posizione corrente
• Tempo trascorso
• Stato batteria (se integrato)
↓
Se e-bike ferma >30 min fuori zona:
“⚠️ E-Bike #003 ferma da 35 min in Via Garibaldi”

```
**3️⃣ FINE NOLEGGIO E PAGAMENTO**
```

Cittadino riporta e-bike nel parcheggio
↓
Invoxia rileva ingresso geofence → Webhook a n8n
↓
n8n calcola automaticamente:
• Durata: 2h 15min (135 minuti)
• Tariffa: €1,00 base + 135min × €0,10 = €14,50
↓
PagoPA Proxy genera:
• IUV univoco: 2024112815ABC123
• Link pagamento valido 7 giorni
• Posizione debitoria su PagoPA
↓
Database salva: noleggio, pagamento, scadenza
↓
Telegram invia messaggio al cittadino:
“🚲 Noleggio terminato!
⏱️ Durata: 2h 15min
💶 Importo: €14,50
🔗 Paga entro 7 giorni:
<https://checkout.pagopa.it/pay/2024112815ABC123>”

```
**4️⃣ CONFERMA PAGAMENTO**
```

Cittadino clicca link e paga (carta/conto/App IO)
↓
PagoPA processa pagamento e invia webhook
↓
Sistema aggiorna database: stato = PAGATO
↓
Telegram conferma:
“✅ Pagamento ricevuto!
Grazie per aver usato il bike sharing comunale.”
↓
Ricevuta digitale archiviata automaticamente

```
#### Calcolo Tariffa (Personalizzabile)
```

TARIFFA BASE:        €1,00  (minimo sempre applicato)
TARIFFA AL MINUTO:   €0,10
MASSIMO GIORNALIERO: €15,00 (cap)

Esempi:
5 minuti   → €1,00  (solo tariffa base)
30 minuti   → €4,00  (1,00 + 30×0,10)
135 minuti   → €14,50 (1,00 + 135×0,10)
200 minuti   → €15,00 (raggiunto cap massimo)

```
---

### 🤖 Automazione con n8n

n8n coordina tutto il sistema senza programmazione. Workflow principali:

#### 1. Gestione Noleggi E-Bike
```

Webhook da Invoxia (geofence exit/enter)
↓
SE tipo evento = “exit”:
→ INSERT INTO noleggi (mezzo, utente, inizio)
→ Telegram: “Noleggio iniziato”

SE tipo evento = “enter”:
→ UPDATE noleggi SET fine = NOW()
→ Calcola: tariffa = durata × €0,10
→ POST a PagoPA Proxy: genera IUV
→ INSERT INTO pagamenti (iuv, importo, url)
→ Telegram: “Paga qui: {url}”

```
#### 2. Scadenze Manutenzione
```

Schedule: Ogni lunedì ore 09:00
↓
Query: SELECT mezzi WHERE prossima_manutenzione < 30 giorni
↓
Per ogni mezzo trovato:
→ Email responsabile flotta
→ Telegram tecnici
→ Log notifica inviata

```
#### 3. Controllo Pagamenti Scaduti
```

Schedule: Ogni giorno ore 18:00
↓
Query: SELECT pagamenti WHERE
stato = ‘IN_ATTESA’ AND scadenza < OGGI
↓
Per ogni pagamento scaduto:
→ UPDATE stato = ‘SCADUTO’
→ Telegram: sollecito utente
→ Se 3 solleciti ignorati → Email responsabile

```
#### 4. Report Settimanale
```

Schedule: Venerdì ore 17:00
↓
Query: Statistiche settimana corrente
• Noleggi totali
• Incassi PagoPA
• Km percorsi flotta
• Mezzi più utilizzati
• E-bike con anomalie
↓
Email PDF a responsabile mobilità

```
---

### 💳 Integrazione PagoPA

Il sistema comunica con PagoPA tramite proxy custom dedicato.

#### Flusso Tecnico
```

1. n8n richiede nuovo pagamento a PagoPA Proxy
   Input:
   • Importo: €14,50
   • Descrizione: “Noleggio E-Bike #003”
   • CF utente: RSSMRA80A01H501Z
   ↓
1. Proxy genera IUV univoco (17 caratteri alfanumerici)
   Esempio: 2024112815ABC123
   ↓
1. Proxy chiama API PagoPA ufficiale:
   POST /gpd/debt-positions
   Headers: Ocp-Apim-Subscription-Key: {API_KEY}
   Body:
   {
   “iupd”: “2024112815ABC123”,
   “fiscalCode”: “12345678901”,  // CF Comune
   “amount”: 1450,  // in centesimi
   “debtor”: {
   “fiscalCode”: “RSSMRA80A01H501Z”,
   “fullName”: “Mario Rossi”,
   “email”: “mario@example.com”
   },
   “paymentOption”: [{
   “dueDate”: “2024-12-05”,  // oggi +7 giorni
   “amount”: 1450,
   “transfer”: [{
   “iban”: “IT60X0542811101000000123456”,
   “remittanceInformation”: “Noleggio E-Bike #003”
   }]
   }]
   }
   ↓
1. PagoPA risponde 201 Created → Posizione debitoria attiva
   ↓
1. Proxy costruisce URL pagamento:
   <https://checkout.pagopa.it/gp/debtposition/>
   12345678901/2024112815ABC123
   ↓
1. Proxy salva in database:
   INSERT INTO pagamenti (
   iuv, importo, url_pagamento,
   stato, data_scadenza
   ) VALUES (
   ‘2024112815ABC123’, 14.50, ‘https://…’,
   ‘IN_ATTESA’, NOW() + INTERVAL ‘7 days’
   )
   ↓
1. Ritorna URL a n8n → Telegram invia link al cittadino

```
#### Verifica Pagamento

**Metodo 1: Webhook (Real-time, preferito)**
```

PagoPA invia notifica POST quando pagamento completato
↓
PagoPA Proxy riceve webhook su /webhook/pagopa
↓
UPDATE pagamenti SET
stato = ‘PAGATO’,
data_pagamento = NOW()
WHERE iuv = {iuv_ricevuto}
↓
n8n notifica utente: “✅ Pagamento confermato”

```
**Metodo 2: Polling (Backup)**
```

n8n ogni ora controlla pagamenti IN_ATTESA
↓
Per ogni IUV chiama GET /gpd/debt-positions/{iuv}
↓
Se API risponde status = “PAID”:
→ Aggiorna database
→ Notifica utente

```
---

## 🏗️ Architettura

### Schema Completo Sistema
```

┌─────────────────────────────────────────────────────────┐
│              INTERNET (Cloudflare)                      │
├─────────────────────────────────────────────────────────┤
│  • SSL/TLS automatico                                   │
│  • DDoS protection                                      │
│  • Cloudflare Access (SSO email aziendale)              │
│  • Rate limiting                                        │
└──────────────────┬──────────────────────────────────────┘
│ Cloudflare Tunnel (criptato)
▼
┌─────────────────────────────────────────────────────────┐
│           ZEROTIER (Rete Privata VPN)                   │
├─────────────────────────────────────────────────────────┤
│  Range: 10.147.0.0/16                                   │
│    • Server:    10.147.20.10                            │
│    • Tracker:   10.147.21.x                             │
│    • Uffici:    10.147.22.x                             │
└──────────────────┬──────────────────────────────────────┘
▼
┌─────────────────────────────────────────────────────────┐
│         SERVER COMUNALE (Docker Compose)                │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  🗺️  TRACCAR (8082, 5013)                               │
│     • Riceve GPS da Concox (porta 5013)                │
│     • Gestisce geofencing e-bike                       │
│     • Invia webhook eventi a n8n                       │
│                                                          │
│  🤖 N8N (5678)                                          │
│     • Riceve webhook da Traccar/Invoxia/PagoPA         │
│     • Coordina logica business                         │
│     • Esegue workflow automatici                       │
│                                                          │
│  🗄️  POSTGRESQL (5432)                                  │
│     • Database centrale                                 │
│     • Tabelle: mezzi, utenti, noleggi, pagamenti       │
│     • Backup automatici                                 │
│                                                          │
│  💳 PAGOPA PROXY (3000)                                 │
│     • Genera IUV                                        │
│     • Crea posizioni debitorie                         │
│     • Riceve webhook conferme                          │
│                                                          │
│  📊 GRAFANA (3001)                                      │
│     • Dashboard real-time                               │
│     • Report utilizzo/incassi                          │
│                                                          │
│  🔧 ADMINER (8080)                                      │
│     • Gestione database via web                        │
│                                                          │
└─────────────────────────────────────────────────────────┘
▲                          ▲
│                          │
┌──────┴──────┐          ┌───────┴────────┐
│ Concox GT06N│          │ Invoxia Tracker│
│ Auto/Furgoni│          │    E-Bike      │
│  GSM → TCP  │          │  Cloud → HTTP  │
└─────────────┘          └────────────────┘

```
### Porte e Servizi

| Servizio | Porta | Accesso | Funzione |
|----------|-------|---------|----------|
| **Traccar** | 8082 | Cloudflare Access | Dashboard GPS |
| **Traccar GPS** | 5013 | Pubblico (GPS) | Ricezione Concox |
| **n8n** | 5678 | Cloudflare Access | Automazione |
| **PostgreSQL** | 5432 | Solo ZeroTier | Database |
| **PagoPA Proxy** | 3000 | Solo interno | API pagamenti |
| **Grafana** | 3001 | Cloudflare Access | Analytics |
| **Adminer** | 8080 | Solo ZeroTier | Admin DB |

---

## 🔧 Hardware GPS

### 🚗 Auto/Furgoni: Concox GT06N

**Caratteristiche:**
- Tipo: OBD-II plug & play
- GPS: GPS + GLONASS
- Connettività: GSM 2G
- Protocollo: GT06 (Traccar nativo)
- Installazione: 2 minuti
- Prezzo: **35-50€**

**SIM Consigliata:**
- Operatore: Vodafone V-IoT
- Dati: 500MB/mese
- Costo: **3€/mese**

**Dove comprare:** Amazon, AliExpress

**Costo totale 5 anni per veicolo:**
- Hardware: 45€
- SIM: 180€ (36€/anno × 5)
- **Totale: 225€** (45€/anno)

---

### 🚲 E-Bike: Invoxia Bike Tracker

**Caratteristiche:**
- Tipo: Tracker dedicato bici
- Posizionamento: GPS + WiFi + Bluetooth
- Autonomia: 3-6 mesi
- Ricarica: USB-C
- SIM: Inclusa (3 anni prepagati)
- Impermeabilità: IP67
- Installazione: 5 minuti (2 viti sotto sella)
- Prezzo: **99€**

**Abbonamento:**
- Anni 1-3: **Incluso** (0€)
- Anni successivi: **24,99€/anno**

**Dove comprare:** Amazon, invoxia.com

**Costo totale 5 anni per e-bike:**
- Hardware: 99€
- Abbonamento: 50€ (anni 4-5)
- **Totale: 149€** (30€/anno)

**Manutenzione:** Ricarica trimestrale (2 ore)

---

## 🚀 Installazione

### Prerequisiti

- Server con Ubuntu 22.04 LTS
- Docker e Docker Compose installati
- ZeroTier configurato
- Account Cloudflare con dominio
- Credenziali PagoPA

### 1. Installazione Base

```bash
# Aggiorna sistema
sudo apt update && sudo apt upgrade -y

# Installa Docker
curl -fsSL https://get.docker.com | sudo bash
sudo usermod -aG docker $USER

# Installa ZeroTier
curl -s https://install.zerotier.com | sudo bash
sudo zerotier-cli join YOUR_NETWORK_ID

# Riavvia
sudo reboot
```

### 2. Autorizza Server su ZeroTier

1. Vai su <https://my.zerotier.com>
1. Seleziona network
1. Autorizza il server
1. Assegna IP: `10.147.20.10`

### 3. Clone e Configurazione

```bash
# Clone repository
git clone https://github.com/comune-magliano/smart-mobility.git
cd smart-mobility

# Copia template configurazione
cp .env.example .env
nano .env
```

### 4. Configura .env

```bash
# Database
DB_PASSWORD=genera_password_forte
DB_NAME=mobility
DB_USER=mobility_admin

# n8n
N8N_PASSWORD=password_n8n
N8N_ENCRYPTION_KEY=chiave_32_caratteri

# Grafana
GRAFANA_PASSWORD=password_grafana

# PagoPA
PAGOPA_API_KEY=tua_chiave_api
PAGOPA_ENVIRONMENT=test  # o 'prod'
COMUNE_CF=12345678901
COMUNE_DENOMINAZIONE=Comune di Magliano in Toscana
COMUNE_IBAN=IT60X0542811101000000123456

# Telegram
TELEGRAM_BOT_TOKEN=bot_token_da_botfather
TELEGRAM_ADMIN_CHAT_ID=tuo_chat_id

# ZeroTier
ZEROTIER_IP=10.147.20.10
```

**Genera password sicure:**

```bash
openssl rand -base64 32  # per password
openssl rand -hex 32     # per encryption key
```

### 5. Avvia Sistema

```bash
docker-compose up -d
```

**Verifica:**

```bash
docker-compose ps
```

Tutti i servizi devono essere `Up`.

### 6. Cloudflare Tunnel

```bash
# Installa cloudflared
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb

# Login
cloudflared tunnel login

# Crea tunnel
cloudflared tunnel create magliano-mobility
```

**Configura** `/etc/cloudflared/config.yml`:

```yaml
tunnel: YOUR_TUNNEL_ID
credentials-file: /root/.cloudflared/YOUR_TUNNEL_ID.json

ingress:
  - hostname: traccar.comune-magliano.it
    service: http://10.147.20.10:8082
  
  - hostname: n8n.comune-magliano.it
    service: http://10.147.20.10:5678
  
  - hostname: grafana.comune-magliano.it
    service: http://10.147.20.10:3001
  
  - hostname: pagamenti.comune-magliano.it
    service: http://10.147.20.10:3000
  
  - hostname: webhook.comune-magliano.it
    service: http://10.147.20.10:5678
    path: /webhook/*
  
  - service: http_status:404
```

**Avvia:**

```bash
sudo cloudflared service install
sudo systemctl start cloudflared
sudo systemctl enable cloudflared
```

**Sul Cloudflare Dashboard:**

- Zero Trust → Tunnels → Configura DNS
- Zero Trust → Access → Proteggi traccar/n8n/grafana

-----

## ⚙️ Configurazione

### Traccar

**URL:** <https://traccar.comune-magliano.it>  
**Login iniziale:** `admin` / `admin`

⚠️ **Cambiare subito la password!**

#### Aggiungi Veicolo

1. **Settings** → **Devices** → **Add**
1. Compila:

- Name: `Auto Panda AB123CD`
- Identifier: IMEI tracker (15 cifre)
- Category: icona auto
- Group: `Mezzi Comunali`

#### Geofencing E-Bike

1. **Settings** → **Geofences** → **Add**
1. Name: `Parcheggio E-Bike Comunale`
1. Disegna area su mappa (50-100m)
1. Assegna a tutte le e-bike

#### Notifiche verso n8n

1. **Settings** → **Notifications** → **Add**
1. Configurazione:

- Type: `Web Request`
- Always: ✅
- URL: `http://n8n:5678/webhook/traccar-event`
- Events: `geofenceEnter`, `geofenceExit`, `deviceOffline`

-----

### Concox GT06N: Configurazione SMS

Invia SMS al numero SIM del tracker:

```sms
SERVER,IP,10.147.20.10,5013#
APN,internet.vodafone.it#
TIMER,30#
STATUS#
```

-----

### Invoxia: Webhook

1. Login su <https://www.invoxia.com>
1. Seleziona tracker
1. **Settings** → **Webhooks** → **Add**
1. URL: `https://webhook.comune-magliano.it/invoxia`
1. Events: `Zone Enter`, `Zone Exit`

-----

### n8n

**URL:** <https://n8n.comune-magliano.it>

#### Importa Workflow

```bash
cd ~/smart-mobility/n8n/workflows
docker cp ebike-rental.json n8n:/tmp/
docker cp maintenance-alerts.json n8n:/tmp/

docker exec -it n8n sh
n8n import:workflow --input=/tmp/ebike-rental.json
n8n import:workflow --input=/tmp/maintenance-alerts.json
exit
```

#### Credenziali PostgreSQL

1. **Credentials** → **Add** → **Postgres**
1. Host: `postgres`
1. Port: `5432`
1. Database: `mobility`
1. User: `mobility_admin`
1. Password: da `.env`

#### Credenziali Telegram

1. **Credentials** → **Add** → **Telegram API**
1. Access Token: da `.env`

-----

## 📱 Utilizzo

### Dipendente Comunale

**Dashboard Traccar:**

1. Accedi con credenziali personali
1. Visualizza mappa real-time
1. Seleziona veicolo assegnato
1. Verifica percorsi giornalieri

**Assegnazione Mezzo:**

- Responsabile flotta assegna via Traccar
- Notifica automatica al dipendente

### Cittadino (E-Bike)

**Registrazione:**

1. Richiesta al Comune (modulo cartaceo/online)
1. Operatore inserisce dati in database
1. Cittadino riceve istruzioni via email/Telegram

**Utilizzo:**

1. Prendi e-bike da parcheggio → Notifica inizio
1. Usa liberamente in città
1. Riporta al parcheggio → Ricevi link pagamento
1. Paga su PagoPA entro 7 giorni

### Responsabile Flotta

**Dashboard Grafana:**

- Mezzi attivi/inattivi
- Km percorsi per veicolo
- Noleggi e-bike giornalieri
- Incassi PagoPA
- Scadenze manutenzione

**Report:**

- Settimanale via email (venerdì)
- Mensile generato automaticamente
- Export dati su richiesta (Adminer)

-----

## 🔧 Manutenzione

### Routine Giornaliera

✅ **Automatica** - nessun intervento necessario

- Backup database (02:00)
- Verifica pagamenti scaduti (18:00)
- Log pulizia vecchi dati (03:00)

### Routine Settimanale

- **Lunedì 09:00**: Verifica alert manutenzioni
- **Venerdì 17:00**: Ricezione report automatico

### Routine Mensile

- **1° del mese**: Report mensile completo
- **15 del mese**: Verifica spazio disco server

### Routine Trimestrale

- Ricarica batterie Invoxia (tutte le e-bike)
- Verifica copertura SIM tracker
- Update Docker images

### Comandi Utili

```bash
# Verifica stato servizi
docker-compose ps

# Visualizza log
docker-compose logs -f traccar
docker-compose logs -f n8n

# Riavvia servizio specifico
docker-compose restart traccar

# Backup manuale database
docker exec postgres pg_dump -U mobility_admin mobility > backup_$(date +%F).sql

# Spazio disco
df -h
```

-----

## ❓ FAQ

### Generale

**Q: Quanto costa mantenere il sistema?**  
A: ~500€/anno per 10 auto + 10 e-bike (principalmente SIM tracker)

**Q: Serve personale tecnico dedicato?**  
A: No, solo verifiche occasionali. Sistema completamente automatico.

**Q: Funziona senza connessione internet?**  
A: Traccar locale funziona, ma PagoPA e notifiche richiedono internet.

### Tecnico

**Q: Cosa succede se salta corrente server?**  
A: Docker Compose riavvia automaticamente tutti i servizi.

**Q: I dati GPS sono conservati quanto?**  
A: Configurable, default 1 anno. Backup incrementali giornalieri.

**Q: Posso aggiungere altri comuni alla rete ZeroTier?**  
A: Sì, è possibile federare più comuni sulla stessa piattaforma.

### Pagamenti

**Q: Cosa succede se utente non paga?**  
A: Solleciti automatici, dopo 3 ignore notifica responsabile. Gestione manuale recupero crediti.

**Q: PagoPA ha costi transazione?**  
A: Sì, commissioni PagoPA (~0,50€ + 1% sul transato). A carico cittadino o comune (configurabile).

**Q: Posso modificare le tariffe?**  
A: Sì, modificabili da n8n senza programmazione.

### Hardware

**Q: Tracker Concox funziona senza rete 2G?**  
A: No, necessaria copertura 2G. Verificare con operatori locali.

**Q: Quanto dura batteria Invoxia realmente?**  
A: 3-4 mesi uso normale (bike sharing quotidiano).

**Q: Posso usare tracker diversi?**  
A: Sì, purché supportati da Traccar (180+ protocolli).

-----

## 📞 Supporto

### Documentazione

- **Repository**: <https://github.com/comune-magliano/smart-mobility>
- **Wiki**: <https://github.com/comune-magliano/smart-mobility/wiki>
- **Issues**: <https://github.com/comune-magliano/smart-mobility/issues>

### Community

- **Forum**: <https://forum.comune-magliano.it/smart-mobility>
- **Telegram**: @MaglianoSmartMobility

### Commerciale

- **Email**: innovazione@comune.magliano.gr.it
- **Telefono**: 0564 123456

-----

## 📄 Licenza

Questo progetto è rilasciato sotto licenza **MIT**.

Copyright (c) 2024 Comune di Magliano in Toscana

-----

## 🙏 Crediti

### Software Open Source Utilizzati

- [Traccar](https://www.traccar.org/) - Piattaforma GPS tracking
- [n8n](https://n8n.io/) - Workflow automation
- [PostgreSQL](https://www.postgresql.org/) - Database
- [Grafana](https://grafana.com/) - Analytics
- [Docker](https://www.docker.com/) - Containerizzazione

### Hardware Consigliato

- [Concox](https://www.concox.com/) - GPS Tracker GT06N
- [Invoxia](https://www.invoxia.com/) - Bike Tracker

### Servizi

- [Cloudflare](https://www.cloudflare.com/) - CDN e sicurezza
- [ZeroTier](https://www.zerotier.com/) - Virtual​​​​​​​​​​​​​​​​
