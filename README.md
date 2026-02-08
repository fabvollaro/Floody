# Floody
Un'infrastruttura di comunicazione offline basata su Intelligenza Artificiale Locale e radiofrequenze a lungo raggio. Progettata per ripristinare immediatamente il coordinamento tra cittadini e Protezione Civile negli scenari critici dove le reti tradizionali falliscono.


>  **DISCLAIMER:** Questo è un progetto Proof-of-Concept (PoC). Non è (ancora) un sostituto certificato per i canali di emergenza ufficiali (112/118).

Di seguito per l'applicazione dell'architettura verrà presa come esempio la città di Faenza, purtroppo già colpita molteplici volte da eventi climatici critici.

---

##  Obiettivo del Progetto

Garantire che nessun cittadino rimanga isolato durante un'alluvione.
Il sistema crea una **rete cittadina parallela** che permette di:
1.  **Chattare con un'IA Medica Locale** per primo soccorso.
2.  **Inviare SOS** geolocalizzati alla Protezione Civile.
3.  **Ricevere Bollettini Ufficiali** anti-panico.

---

## Architettura a 3 Livelli

Il sistema copre un'area di **215 km²** utilizzando una topologia ibrida Wi-Fi / LoRa (Meshtastic).

###  Livello 1: (Torri a lungo raggio)
**Hardware:** Gateways LoRa ad alta potenza su torri/colline.
* **Ruolo:** Ricevono i messaggi dai quartieri e li trasmettono al C.O.C. (Centro Operativo Comunale).
* **Posizioni Strategiche:** Torre Civica, Oriolo dei Fichi, Granarolo.
* **Copertura:** 10-15 km raggio.

###  Livello 2: (Nodi di quartiere con IA)
**Hardware:** Raspberry Pi 5 + Modulo LoRa + Pannello Solare.
* **Ruolo:** Forniscono una rete Wi-Fi locale (`SOS_FAENZA`) ai cittadini. Eseguono un LLM (Llama 3) offline per rispondere a domande di emergenza.
* **Posizioni Strategiche:** Borgo Durbecco, Ospedale, Stazione, PalaCattani, Zona Cappuccini, Centro Città.
* **Copertura:** 150-200 metri (Wi-Fi) / 3-5 km (LoRa verso L1).

###  Livello 3: (Dispositivi dei cittadini)
**Hardware:** Nodi Meshtastic (ESP32 / Heltec V3) portatili.
* **Ruolo:** Dispositivi economici distribuiti ai cittadini o volontari. Creano una rete mesh "salta-salta" per portare il segnale dalle cantine o case isolate fino al Livello 2.
* **Copertura:** Mesh dinamica (House-to-House).

---

## Hardware Requirements (BOM)

### Livello 2 (Nodo AI)
| Componente | Specifiche | Note |
| :--- | :--- | :--- |
| **SBC** | Raspberry Pi 5 (8GB RAM) | Compatibile con Llama 3.2 |
| **Cooling** | Active Cooler | Fondamentale per non surriscaldare |
| **Storage** | MicroSD 128GB A2 | Alta resistenza |
| **Radio Interface** | Heltec V3 (via USB) | Bridge Meshtastic <-> Python |
| **Power** | Batteria LiFePO4 12V 20Ah | Pannello Solare 50W |

### Livello 3 (Nodo Cittadino)
* **Board:** Heltec V3 LoRa 868MHz (o TTGO T-Beam)
* **Batteria:** 18650 Li-Ion (3000mAh)
* **Case:** Stampato 3D (PLA/PETG)

---

##  Software Stack

Il cuore del sistema gira su **Raspberry Pi OS (Bookworm)**.

1.  **LLM Engine:** [Ollama](https://ollama.com)
    * Modello: `llama3.2:3b` (Quantizzato 4-bit)
    * Knowledge Base (RAG): Manuali Protezione Civile, Croce Rossa, Mappe Locali.
2.  **Network Layer:** [Meshtastic](https://meshtastic.org)
    * Protocollo: LoRa 868MHz (EU868).
    * Python API per interfacciamento con l'IA.
3.  **User Interface:**
    * Captive Portal Wi-Fi (Nginx + Flask).
    * Interfaccia Chat "WhatsApp-style".

---
