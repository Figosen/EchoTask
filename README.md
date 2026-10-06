# VoxCard 🎙️

> Hands-free Work Order platform for frontline workers across industries, powered by ESP32, Azure IoT Hub, DPS zero-touch provisioning, and ASP.NET Core.

![Status: Work in Progress](https://img.shields.io/badge/Status-Work_in_Progress-yellow.svg)
![Version](https://img.shields.io/badge/Version-0.1.0_alpha-blue.svg)

## 📌 About The Project (Early Stage)

VoxCard is an IoT-based system designed to allow frontline workers (mechanics, field service, industry) to create Work Orders (WO) using voice commands. 

*Note: This project is currently in active development. Architecture is defined, and the foundation is being laid out.*

### 🔐 Key Architecture: Zero-Touch Whitelisting
Diagram source: `docs/architecture/`

To ensure enterprise-grade security and a seamless user experience, this project uses a **Zero-Touch Provisioning** model:

1. **Pre-authorization:** A mobile app scans the ESP32 (M5Stick) MAC address via Bluetooth and whitelists it in Azure SQL via an Entra ID-secured .NET API.
2. **Custom DPS Webhook:** When the device boots, it connects to Azure Device Provisioning Service (DPS). A custom Azure Function validates the MAC address against the SQL database.
3. **Secure Connection:** If whitelisted, the device is provisioned in Azure IoT Hub and begins streaming audio/telemetry. No hardcoded secrets exist on the device.

## How it works

```
Speech  →  Audio upload  →  Transcription  →  Structuring  →  Trade-specific logic  →  Work-card API  →  Status feedback
           (Blob Storage)   (Whisper)         (GPT-4o-mini)   (mechanic / electrician  (customer's        (app now,
                                              → JSON           / gardener handler)     system)             LED later)
```

Example (illustrative only):

| Input (speech) | Output (structured JSON) |
|---|---|
| *"Replaced the front brake pads and checked the fluid level, took about forty minutes."* | `{ "action": "repair", "parts": ["front brake pads"], "checks": ["brake fluid"], "duration_minutes": 40 }` |

> 🚧 The real JSON schema is not defined yet.

### Components

| Component | Responsibility | Phase |
|---|---|---|
| **App** | Authentication, recording (Phase 1), onboarding of devices, viewing upload history and status | 1 |
| **ASP.NET Core API** | Users, tenants, integrations, uploads. **Not** in the audio hot path | 1 |
| **Blob Storage** | Stores audio files, uploaded directly via short-lived SAS URLs | 1 |
| **Event Grid** | Detects new uploads and triggers processing | 1 |
| **Service Bus** | Decouples upload from processing. Retries and dead-letter queue | 1 |
| **Azure Function** | Orchestrates transcription, structuring and the work-card call | 1 |
| **Whisper** | Speech-to-text | 1 |
| **GPT-4o-mini** | Turns transcripts into structured JSON using prompts and prompt dictionaries | 1 |
| **Work-card integration** | Calls the customer's work-card API | 1 |
| **Hardware stick** | Microphone, button, LED. Records and uploads | 2 |
| **IoT Hub + DPS** | Device identity, provisioning, file upload, C2D status messages, OTA | 2 |

## Tech stack

| Area | Choice | Status |
|---|---|---|
| Backend API | ASP.NET Core | Planned |
| Orchestration | Azure Functions | Planned |
| Messaging | Azure Event Grid + Service Bus | Planned |
| Storage | Azure Blob Storage | Planned |
| Database | SQL (Azure SQL) | Planned |
| Secrets | Azure Key Vault | Planned |
| Speech-to-text | OpenAI Whisper | Planned |
| Structuring | GPT-4o-mini | Planned |
| Mobile / config app | React Native | Open |
| Hardware | M5StickC PLUS2 (ESP32) | Phase 2 |
| Firmware | C++ | Phase 2 |
| Device cloud | Azure IoT Hub + DPS | Phase 2 |
| Infrastructure as code | Bicep | Open |

## 📂 Repository Structure

```text
├── docs/
│   ├── VocabillArchitectureMVP.pdf
│   └── VocabillArchitectureProd.pdf
├── src
│   ├── Vocabill.Api
│   ├── Vocabill.App
│   ├── Vocabill.Common
│   └── Vocabill.Functions
├── firmware     
├── infra
├── promts
├── docker-compose.yml
└── README.md
```

## 🚀 Local Development (Work in Progress)

### Prerequisites
- .NET 10 SDK
- Azure Functions Core Tools
- Azure CLI
- OpenAI Account
- React Native
- Docker Desktop
- Azure Functions Core Tools

### Run locally
Docker is in the making, but isn't set up yet.
```bash
# 1. Clone
git clone https://github.com/<org>/vocabill.git
cd vocabill

# 2. Configure (see Configuration)
cp .env.example .env

# 3. Run the API
dotnet run --project src/Vocabill.Api

# 4. Run the functions
cd src/Vocabill.Functions && func start
```

Local development should use **Azurite** (storage emulator) and a **mock work-card API**, so nobody needs real customer credentials to develop.

## 🗺️ Roadmap

- [x] Not updated