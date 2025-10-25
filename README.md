```markdown
# 🚗 Magliano Smart Mobility

> Sistema **open source** e **self-hosted** per la gestione della flotta comunale e bike sharing con pagamenti PagoPA

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Docker](https://img.shields.io/badge/Docker-Ready-2496ED?logo=docker)](https://www.docker.com/)
[![PagoPA](https://img.shields.io/badge/PagoPA-Compatible-0066CC)](https://www.pagopa.it/)

---

## 📋 Indice

- [Introduzione](#-introduzione)
- [Caratteristiche](#-caratteristiche)
- [Architettura](#️-architettura)
- [Hardware Consigliato](#-hardware-consigliato)
- [Requisiti](#-requisiti)
- [Installazione](#-installazione)
- [Configurazione](#️-configurazione)
- [Utilizzo](#-utilizzo)
- [Sicurezza](#-sicurezza)
- [Manutenzione](#-manutenzione)
- [FAQ](#-faq)
- [Supporto](#-supporto)

---

## 🎯 Introduzione

**Magliano Smart Mobility** è una piattaforma completa per il Comune di Magliano in Toscana che gestisce:

- 🚗 **Flotta comunale**: auto, furgoni, mezzi tecnici con tracciamento GPS
- 🚲 **E-bike sharing**: servizio di noleggio per cittadini con pagamento automatico
- 📍 **Monitoraggio GPS**: posizione real-time di tutti i mezzi
- 💳 **Pagamenti PagoPA**: integrazione nativa per incassi digitali conformi
- 🔔 **Notifiche automatiche**: Telegram/Email per eventi, scadenze e allarmi
- 📊 **Analytics**: dashboard e report sull'utilizzo della flotta

**Tutto self-hosted, dati sotto controllo comunale, zero dipendenze cloud commerciali.**

---

## ✨ Caratteristiche

### 🚗 Gestione Flotta Comunale

- ✅ **Tracciamento GPS in tempo reale** di tutti i veicoli
- ✅ **Storico percorsi** con chilometraggi e tempi di utilizzo
- ✅ **Geofencing intelligente** (allarme uscita da aree autorizzate)
- ✅ **Scadenzario manutenzioni** automatico con notifiche preventive
- ✅ **Report dettagliati** per mezzo, dipendente e reparto
- ✅ **Alert anomalie** (eccesso velocità, soste prolungate, batteria scarica)

### 🚲 E-Bike Sharing Cittadini

- ✅ **Rilevamento automatico** inizio/fine noleggio via geofencing
- ✅ **Calcolo tariffa dinamico** in base a durata e fasce orarie
- ✅ **Generazione automatica** pagamento PagoPA con IUV univoco
- ✅ **Link di pagamento** inviato via Telegram o Email
- ✅ **Verifica pagamento** automatica e archiviazione ricevute
- ✅ **Monitoraggio stato batteria** e posizione e-bike

### 🤖 Automazione Intelligente

- ✅ **Notifiche manutenzione** programmata (revisione, tagliando, gomme)
- ✅ **Allarmi batteria scarica** per e-bike con soglia configurabile
- ✅ **Report settimanali/mensili** automatici via email
- ✅ **Workflow personalizzabili** senza programmazione (no-code)
- ✅ **Integrazione Telegram** per comunicazioni immediate
- ✅ **Backup automatici** database su schedule configurabile

---

## 🏗️ Architettura
```

┌─────────────────────────────────────────────────────────────────┐
│                    CLOUDFLARE (Edge Layer)                      │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐            │
│  │   Tunnel    │  │   Access    │  │     WAF     │            │
│  │   (SSL)     │  │    (SSO)    │  │ (Protezione)│            │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘            │
└─────────┼─────────────────┼─────────────────┼───────────────────┘
│                 │                 │
HTTPS │            OAuth │          Rate   │ Limiting
▼                 ▼                 ▼
┌─────────────────────────────────────────────────────────────────┐
│              ZEROTIER NETWORK (10.147.x.x/16)                   │
│                    Rete Privata Virtuale                        │
│                                                                  │
│  ┌───────────────────────────────────────────────────────────┐ │
│  │  🖥️  SERVER COMUNALE (Docker Host)                        │ │
│  │     IP: 10.147.20.10                                       │ │
│  │                                                            │ │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐ │ │
│  │  │ Traccar  │  │   n8n    │  │ PagoPA   │  │PostgreSQL│ │ │
│  │  │  :8082   │  │  :5678   │  │  Proxy   │  │  :5432   │ │ │
│  │  │ GPS Hub  │◄─┤Automation│◄─┤  :3000   │◄─┤ Database │ │ │
│  │  └────┬─────┘  └──────────┘  └──────────┘  └──────────┘ │ │
│  │       │                                                   │ │
│  │  ┌────▼──────┐  ┌──────────┐  ┌──────────┐             │ │
│  │  │  Adminer  │  │ Grafana  │  │Cloudflared│            │ │
│  │  │  :8080    │  │  :3001   │  │  Daemon   │            │ │
│  │  │  DB UI    │  │Analytics │  │ Tunnel    │            │ │
│  │  └───────────┘  └──────────┘  └──────────┘             │ │
│  └───────────────────────────────────────────────────────────┘ │
│                                                                  │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐            │
│  │ 👨‍💼 Ufficio  │  │ 🚗 Auto GPS │  │ 🚲 E-Bike   │            │
│  │  Tecnico    │  │  Comunale   │  │  Tracker    │            │
│  │ (PC/Tablet) │  │  Traccar    │  │  GPS+GSM    │            │
│  └─────────────┘  └─────────────┘  └─────────────┘            │
└─────────────────────────────────────────────────────────────────┘

LEGENDA:
━━━━ Rete ZeroTier (traffico criptato peer-to-peer)
──── Comunicazione interna Docker (bridge network)

```
### 📦 Stack Tecnologico

| Componente | Funzione | Porta | Accesso | Licenza |
|------------|----------|-------|---------|---------|
| **Traccar** | Piattaforma GPS tracking open source | 8082 | Cloudflare Access | Apache 2.0 |
| **n8n** | Automazione workflow (no-code) | 5678 | Cloudflare Access | Apache 2.0 |
| **PagoPA Proxy** | Gateway pagamenti custom | 3000 | Cloudflare Tunnel | MIT |
| **PostgreSQL** | Database relazionale | 5432 | Solo ZeroTier | PostgreSQL |
| **Adminer** | Gestione database via web | 8080 | Solo ZeroTier | Apache 2.0 |
| **Grafana** | Dashboard e analytics | 3001 | Cloudflare Access | AGPL 3.0 |
| **Cloudflared** | Tunnel sicuro Cloudflare | - | Daemon | Apache 2.0 |

### 🔄 Flusso Dati Tipico (E-Bike)
```

1. Cittadino prende E-Bike dal parcheggio
   ↓
1. GPS rileva uscita da geofence → Traccar
   ↓
1. Traccar invia webhook → n8n
   ↓
1. n8n registra inizio noleggio → PostgreSQL
   ↓
1. Telegram invia conferma → Cittadino
   ↓
1. Cittadino riporta E-Bike
   ↓
1. GPS rileva ingresso geofence → Traccar
   ↓
1. n8n calcola durata e tariffa
   ↓
1. PagoPA Proxy genera IUV e link pagamento
   ↓
1. PostgreSQL salva dati pagamento
   ↓
1. Telegram invia link pagamento → Cittadino
   ↓
1. Cittadino paga via PagoPA
   ↓
1. Webhook PagoPA conferma → n8n
   ↓
1. PostgreSQL aggiorna stato → PAGATO ✅

```
---

## 🔧 Hardware Consigliato

### 🖥️ Server Comunale

#### Opzione 1: Mini PC (⭐ Consigliato)

**Modelli Testati:**

| Modello | CPU | RAM | Storage | Consumo | Prezzo | Valutazione |
|---------|-----|-----|---------|---------|--------|-------------|
| **Intel NUC 11** | i5-1135G7 | 16GB | 512GB NVMe | 15W | 550€ | ⭐⭐⭐⭐⭐ Ottimo |
| **Beelink SER5** | Ryzen 5 5560U | 16GB | 500GB NVMe | 15W | 450€ | ⭐⭐⭐⭐⭐ Best Buy |
| **Lenovo M720q** | i5-8500T | 16GB | 512GB SSD | 20W | 400€ | ⭐⭐⭐⭐ Ricondizionato |
| **HP EliteDesk 800 G4** | i5-8500 | 16GB | 256GB SSD | 25W | 350€ | ⭐⭐⭐⭐ Ricondizionato |

**✅ Vantaggi Mini PC:**
- Silenzioso (ideale per ufficio comunale)
- Basso consumo elettrico (~15W = 11€/anno)
- Compatto (10x10 cm circa)
- Prestazioni eccellenti per 50+ mezzi
- Affidabile per funzionamento 24/7

**Specifiche Minime:**
- CPU: 4 core / 8 thread (Intel i5 o AMD Ryzen 5)
- RAM: 16 GB DDR4
- Storage: 256 GB SSD (consigliato 512 GB NVMe per database)
- Rete: Gigabit Ethernet (WiFi opzionale come backup)
- OS: Ubuntu 22.04 LTS Server

#### Opzione 2: Raspberry Pi (Budget Limitato)

**Modelli:**

| Modello | RAM | Storage | Mezzi Max | Prezzo | Valutazione |
|---------|-----|---------|-----------|--------|-------------|
| **Raspberry Pi 5** | 8GB | microSD 128GB + SSD USB | ~15 mezzi | 180€ | ⭐⭐⭐ Test/Piccoli comuni |
| **Raspberry Pi 4** | 8GB | microSD 128GB + SSD USB | ~10 mezzi | 120€ | ⭐⭐ Solo test |

**⚠️ Limitazioni:**
- Performance limitate con molti mezzi contemporanei
- Database su SSD esterno obbligatorio (microSD troppo lenta)
- Consigliato solo per test o comuni molto piccoli (<10 mezzi)

**✅ Vantaggi:**
- Costo contenuto
- Consumo minimo (~5W)
- Ideale per proof-of-concept

#### Opzione 3: Server Rack (Grandi Comuni)

**Per flotte 100+ mezzi:**
- Dell PowerEdge R340 / HP ProLiant DL20
- CPU: Xeon E-2234 (4 core)
- RAM: 32 GB ECC
- Storage: 2x SSD 512GB RAID1 + HDD 2TB backup
- Prezzo: 1.200€ nuovo / 600€ ricondizionato

---

### 📡 GPS Tracker per Auto/Furgoni Comunali

#### Opzione 1: Concox GT06N - OBD-II Plug & Play (⭐ Consigliato)
```

┌─────────────────────────────────────────────┐
│         Concox GT06N                        │
├─────────────────────────────────────────────┤
│ 📍 GPS + GLONASS                            │
│ 📱 GSM Quad-band (2G)                       │
│ 🔌 Connessione OBD-II (Plug & Play)        │
│ 🔋 Batteria backup integrata (1 ora)       │
│ 📊 Rileva: accensione, spegnimento, shock  │
│ 🌐 Protocollo: GT06 (supportato Traccar)   │
│                                             │
│ 💰 Prezzo: 35-50€                           │
│ 📦 Dove: Amazon, AliExpress                 │
└─────────────────────────────────────────────┘

```
**✅ Vantaggi:**
- **Installazione immediata**: si inserisce nella presa OBD-II (sotto volante)
- **Nessun cablaggio**: prende alimentazione dalla porta diagnostica
- **Rileva accensione**: sa quando il veicolo è in uso
- **Economico**: ideale per flotte numerose
- **Invisibile**: nascosto sotto il cruscotto

**⚠️ Svantaggi:**
- Può essere facilmente rimosso (non adatto per antifurto)
- Richiede rete 2G (da verificare copertura operatore)
- Batteria backup limitata (1 ora)

**📋 Specifiche Tecniche:**
- Precisione GPS: 5-10 metri
- Frequenza invio posizione: configurabile (10s - 5min)
- Temperatura operativa: -20°C a +70°C
- Dimensioni: 52x48x24 mm
- Peso: 45g

**🛠️ Installazione:**
1. Localizza porta OBD-II (solitamente sotto volante lato guida)
2. Inserisci dispositivo
3. Accendi veicolo → LED lampeggia
4. Configura via SMS (vedi sezione Configurazione)

#### Opzione 2: Teltonika FMB120 - Professionale Cablato
```

┌─────────────────────────────────────────────┐
│      Teltonika FMB120                       │
├─────────────────────────────────────────────┤
│ 📍 GPS/GLONASS ad alta precisione          │
│ 📱 GSM/GPRS/EDGE                            │
│ 🔌 Installazione cablata permanente        │
│ 📊 Accelerometro 3 assi (stile guida)      │
│ 🔋 Batteria backup 220mAh (5 ore)          │
│ 💧 Impermeabile IP54                        │
│ 🌐 Protocollo: Teltonika (Traccar nativo)  │
│                                             │
│ 💰 Prezzo: 80-120€                          │
│ 📦 Dove: Distributori ufficiali            │
└─────────────────────────────────────────────┘

```
**✅ Vantaggi:**
- **Professionale**: usato da grandi flotte europee
- **Monitoraggio avanzato**: stile guida, frenate brusche, accelerazioni
- **Sicuro**: installazione permanente, difficile rimozione
- **Affidabile**: certificato automotive, garanzia 2 anni
- **Espandibile**: ingressi digitali per sensori aggiuntivi

**⚠️ Svantaggi:**
- Installazione richiede elettrauto (~50€/veicolo)
- Costo più elevato
- Configurazione più complessa

**📋 Specifiche Tecniche:**
- Precisione GPS: 2.5 metri
- Memoria interna: 16MB (eventi offline)
- Ingressi digitali: 4
- Uscite digitali: 2
- Temperatura: -40°C a +85°C

**🎯 Ideale per:**
- Veicoli di valore (es. mezzi operativi costosi)
- Necessità di monitoraggio comportamento guida
- Integrazione con sensori (portiere, rimorchio, PTO)

#### Opzione 3: Smartphone Android + Traccar Client (Gratis)
```

┌─────────────────────────────────────────────┐
│   Smartphone Android + App Traccar         │
├─────────────────────────────────────────────┤
│ 📱 Qualsiasi Android 7.0+                   │
│ 📍 GPS integrato                            │
│ 📶 Connessione 3G/4G/5G o WiFi             │
│ 🔋 Power bank per autonomia                │
│ 📲 App gratuita da Play Store              │
│                                             │
│ 💰 Prezzo: 0€ (riuso vecchi smartphone)    │
│ 📦 App: [play.google.com/traccar.client](http://play.google.com/traccar.client)     │
└─────────────────────────────────────────────┘

```
**✅ Vantaggi:**
- **Costo zero**: riutilizza smartphone dismessi
- **Immediato**: scarica app e configura in 5 minuti
- **Flessibile**: sposta tra veicoli facilmente

**⚠️ Svantaggi:**
- Richiede ricarica regolare (anche con power bank)
- Meno affidabile di tracker dedicati
- Visibile (può essere rubato/dimenticato)

**🎯 Ideale per:**
- Test iniziali del sistema
- Veicoli usati saltuariamente
- Budget ristrettissimo

---

### 🚲 Hardware per E-Bike Sharing

#### Soluzione A: Invoxia Bike Tracker (⭐ Consigliato)
```

┌─────────────────────────────────────────────────────────┐
│             Invoxia GPS Bike Tracker                    │
├─────────────────────────────────────────────────────────┤
│ 📍 GPS + WiFi + Bluetooth positioning                   │
│ 🔋 Batteria: 3-6 mesi autonomia                         │
│ 📱 SIM integrata (abbonamento incluso 3 anni)          │
│ 💧 Impermeabile IP67                                    │
│ 🔒 Montaggio sotto sella (kit incluso)                  │
│ 📊 Geofencing e allarmi movimento                       │
│ 🌐 App iOS/Android + API per integrazione              │
│                                                          │
│ 💰 Prezzo: 99€ + 25€/anno dopo 3 anni                   │
│ 📦 Dove: Amazon, sito ufficiale Invoxia                 │
│                                                          │
│ ⚙️ Dimensioni: 11.3 x 5 x 2.4 cm                       │
│ ⚖️ Peso: 92g                                            │
└─────────────────────────────────────────────────────────┘

```
**✅ Vantaggi:**
- **Zero manutenzione**: autonomia eccezionale
- **SIM inclusa**: nessun costo mensile per 3 anni
- **Facile installazione**: 2 viti sotto sella
- **Affidabile**: brand francese, migliaia di utenti
- **Multi-tecnologia**: GPS + WiFi (precisione in città)

**⚠️ Svantaggi:**
- Sistema proprietario (webhook via API per integrare Traccar)
- Frequenza aggiornamento limitata (1-2 posizioni/ora in modalità eco)
- Costo ricorrente dopo 3 anni

**🔧 Integrazione con Sistema:**
- Invoxia fornisce webhook API
- n8n riceve notifiche ingresso/uscita geofence
- Workflow identico a tracker standard

**🎯 Ideale per:**
- Comuni che vogliono soluzione "chiavi in mano"
- Necessità di zero manutenzione
- Budget disponibile per qualità

#### Soluzione B: Ruptela Plug5 - Compatto 4G
```

┌─────────────────────────────────────────────────────────┐
│             Ruptela Plug5                               │
├─────────────────────────────────────────────────────────┤
│ 📍 GPS/GLONASS                                          │
│ 📱 4G LTE + 2G fallback                                 │
│ 🔋 Batteria LiPo 2000mAh (7 giorni autonomia)          │
│ 🔌 Ricarica USB-C                                       │
│ 📊 Accelerometro, rileva movimento                      │
│ 💧 Resistente agli spruzzi IP54                         │
│ 🌐 Protocollo: Ruptela (supportato Traccar)            │
│                                                          │
│ 💰 Prezzo: 60€ + SIM (3€/mese)                         │
│ 📦 Dove: Distributori GPS tracking                      │
│                                                          │
│ ⚙️ Dimensioni: 88 x 57 x 23 mm                         │
│ ⚖️ Peso: 85g (con batteria)                            │
└─────────────────────────────────────────────────────────┘

```
**✅ Vantaggi:**
- **4G veloce**: aggiornamenti rapidi posizione
- **Ricaricabile**: USB-C standard
- **Compatto**: entra in borsello sotto sella
- **Economico**: buon rapporto qualità/prezzo
- **Traccar nativo**: integrazione immediata

**⚠️ Svantaggi:**
- Richiede ricarica settimanale
- SIM separata (costo mensile)
- Non impermeabile (necessita custodia)

**🔧 Installazione:**
1. Inserisci in borsello impermeabile sotto sella
2. Collega cavo alimentazione batteria e-bike (opzionale)
3. Attiva SIM IoT
4. Configura su Traccar

**🎯 Ideale per:**
- Comuni con personale per ricariche settimanali
- Budget contenuto
- Integrazione nativa Traccar desiderata

#### Soluzione C: Sistema Custom ESP32 (DIY)
```

┌─────────────────────────────────────────────────────────┐
│         Sistema Custom ESP32 + GPS                      │
├─────────────────────────────────────────────────────────┤
│ 🧠 Microcontrollore: ESP32-WROOM-32                     │
│ 📍 Modulo GPS: NEO-6M o NEO-7M                          │
│ 📱 Modulo GSM: SIM800L (opzionale)                      │
│ 🔋 Batteria: LiPo 3.7V 5000mAh                          │
│ ⚡ Gestione carica: TP4056                               │
│ 📦 Case: Box impermeabile IP65                          │
│ 🌐 Connessione: WiFi o GSM                              │
│                                                          │
│ 💰 Costo componenti: ~60€ per unità                     │
│ ⏱️ Tempo assemblaggio: 2-3 ore                         │
│ 🛠️ Competenze: Elettronica base + Arduino              │
└─────────────────────────────────────────────────────────┘

```
**📋 Bill of Materials (BOM):**

| Componente | Modello | Funzione | Prezzo |
|------------|---------|----------|--------|
| Microcontrollore | ESP32-WROOM-32 | Brain del sistema, WiFi/BT | 8€ |
| GPS Module | NEO-6M o NEO-7M | Posizionamento satellitare | 12€ |
| GSM Module | SIM800L (opzionale) | Connessione dati mobile | 8€ |
| Batteria | LiPo 3.7V 5000mAh | Autonomia 3-5 giorni | 15€ |
| Charge Controller | TP4056 | Ricarica sicura LiPo | 3€ |
| DC-DC Converter | LM2596 | Alimentazione da batteria e-bike | 4€ |
| Case impermeabile | IP65 Box 100x70x35mm | Protezione agenti atmosferici | 10€ |
| Cavi e connettori | Vari | Assemblaggio | 5€ |
| **TOTALE** | | | **~65€** |

**✅ Vantaggi:**
- **Massima personalizzazione**: adatta alle tue esigenze
- **Economico**: frazione del costo soluzioni commerciali
- **Espandibile**: aggiungi sensori (batteria, blocco ruota, ecc.)
- **Open source**: community ampia di supporto
- **Didattico**: ottimo per progetti STEM/scuole

**⚠️ Svantaggi:**
- Richiede competenze elettroniche
- Assemblaggio e test necessari
- Nessuna garanzia/supporto commerciale
- Certificazioni assenti

**🎯 Ideale per:**
- Comuni con personale tecnico interno
- Progetti pilota / sperimentazione
- Collaborazione con scuole tecniche locali
- Budget molto limitato

**📚 Risorse:**
- Tutorial assemblaggio: GitHub (link nel repository)
- Firmware Arduino pre-configurato incluso
- Schema elettrico e PCB design disponibili

#### Soluzione D: Smartphone Dedicato Budget
```

┌─────────────────────────────────────────────────────────┐
│    Smartphone Budget + Traccar Client + Accessori      │
├─────────────────────────────────────────────────────────┤
│ 📱 Xiaomi Redmi A2 / Samsung A04                        │
│ 📍 GPS integrato                                        │
│ 📶 4G LTE                                               │
│ 🔋 Power bank 10.000mAh nel borsello                    │
│ 💧 Custodia impermeabile da manubrio                    │
│ 📲 App Traccar Client                                   │
│                                                          │
│ 💰 Costo totale: ~100€ per e-bike                       │
│   - Smartphone: 60€                                     │
│   - Custodia impermeabile: 15€                          │
│   - Power bank: 20€                                     │
│   - SIM IoT: 3€/mese                                    │
└─────────────────────────────────────────────────────────┘

```
**✅ Vantaggi:**
- Rapidissimo da implementare
- GPS preciso e 4G veloce
- Facile sostituzione se danneggiato
- Utile anche per altre funzioni (es. bike computer)

**⚠️ Svantaggi:**
- Visibile e appetibile per furti
- Richiede ricarica frequente anche con power bank
- Meno robusto di tracker industriali

**🎯 Ideale per:**
- Test immediato del sistema (proof of concept)
- Soluzione temporanea in attesa di tracker dedicati
- E-bike in aree a basso rischio furto

---

### 📱 SIM Card IoT per Tracker

#### Operatori Consigliati (Italia)

| Operatore | Piano | Dati Inclusi | Costo | Copertura | Note |
|-----------|-------|--------------|-------|-----------|------|
| **Vodafone V-IoT** | V by Vodafone | 500MB/mese | 3€/mese | ⭐⭐⭐⭐⭐ | Ottima in tutta Italia |
| **TIM IoT** | TIM IoT Safe | 1GB/mese | 5€/mese | ⭐⭐⭐⭐⭐ | Include IP statico |
| **Fastweb IoT** | Basic | 500MB/mese | 2,5€/mese | ⭐⭐⭐⭐ | Solo dati, no voce |
| **WindTre IoT** | Smart IoT | 500MB/mese | 3€/mese | ⭐⭐⭐⭐ | Buona copertura 4G |
| **1NCE** | 10 anni flat | 500MB totali | 10€ una tantum | ⭐⭐⭐ | Roaming multi-operatore |

**💡 Raccomandazioni:**

**Per Auto/Furgoni:**
- **TIM IoT Safe** (5€/mese) → IP statico utile per accesso remoto
- **Vodafone V-IoT** (3€/mese) → Miglior copertura nazionale

**Per E-Bike:**
- **1NCE** (10€ una tantum) → Ideale! Nessun canone ricorrente per 10 anni
- **Fastweb IoT** (2,5€/mese) → Più economica con canone

**📊 Consumo Dati Stimato:**

| Scenario | Posizioni/Giorno | Consumo/​​​​​​​​​​​​​​​​
```
