# VoxCard 🎙️

> Hands-free Work Order platform for frontline workers across industries, powered by ESP32, Azure IoT Hub, DPS zero-touch provisioning, and ASP.NET Core.

![Status: Work in Progress](https://img.shields.io/badge/Status-Work_in_Progress-yellow.svg)
![Version](https://img.shields.io/badge/Version-0.1.0_alpha-blue.svg)

## 📌 About The Project (Early Stage)

VoxCard is an IoT-based system designed to allow frontline workers (mechanics, field service, industry) to create Work Orders (WO) using voice commands. 

*Note: This project is currently in active development. Architecture is defined, and the foundation is being laid out.*

### 🔐 Key Architecture: Zero-Touch Whitelisting

To ensure enterprise-grade security and a seamless user experience, this project uses a **Zero-Touch Provisioning** model:

1. **Pre-authorization:** A mobile app scans the ESP32 (M5Stick) MAC address via Bluetooth and whitelists it in Azure SQL via an Entra ID-secured .NET API.
2. **Custom DPS Webhook:** When the device boots, it connects to Azure Device Provisioning Service (DPS). A custom Azure Function validates the MAC address against the SQL database.
3. **Secure Connection:** If whitelisted, the device is provisioned in Azure IoT Hub and begins streaming audio/telemetry. No hardcoded secrets exist on the device.

## 🛠️ Tech Stack

- **Hardware/Firmware:** M5StickC PLUS2 (ESP32), C++, PlatformIO, FreeRTOS
- **Backend:** .NET 8 ASP.NET Core Web API, Azure Functions (C#)
- **Cloud & IoT:** Azure IoT Hub, Azure DPS, Azure SQL
- **Identity:** Microsoft Entra ID (External ID) for B2B user auth
- **Infrastructure:** Docker (local dev), Bicep/GitLab CI (planned)
- **Mobile:** TBD (Flutter / .NET MAUI)

## 📂 Repository Structure

```text
├── docs/                      # Architecture diagrams and specifications
├── src/
│   ├── firmware/              # M5Stick (ESP32) C++ project
│   ├── mobile/                # Mobile app for BLE whitelisting
│   ├── backend/               # .NET 8 API & Azure Functions webhook
│   └── infrastructure/        # Bicep IaC templates
├── docker-compose.yml         # Local SQL Server for dev
└── README.md
```

## 🚀 Local Development (Work in Progress)

### Prerequisites

- [.NET 8 SDK](https://dotnet.microsoft.com/)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [Azure Functions Core Tools](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local)

### Running the Backend Locally

1. Start the local database:
   ```bash
   docker-compose up -d
   ```

2. Start the API:
   ```bash
   cd src/backend/src/WebApi
   dotnet run
   ```

## 🗺️ Roadmap

- [x] Architecture design and Proof of Concept planning
- [x] Define Azure DPS Custom Allocation logic
- [ ] Implement .NET 8 API with Entra ID Auth
- [ ] Implement local SQL database via Docker
- [ ] Build Azure Function Webhook for DPS
- [ ] ESP32 BLE MAC-address broadcasting and Wi-Fi provisioning
- [ ] IoT Hub integration and MQTT communication