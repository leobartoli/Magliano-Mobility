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
