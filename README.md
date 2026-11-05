# STM32 IIoT Gateway (Modbus RTU to MQTT)

Repo ini berisi firmware untuk membuat gateway Industrial IoT (IIoT) mandiri berbasis STM32. Fungsinya sederhana tapi krusial: membaca data dari sensor atau PLC lama yang menggunakan protokol Modbus RTU (via RS485), lalu meneruskannya ke server atau cloud menggunakan protokol MQTT.

Sistem ini dibangun di atas **FreeRTOS** agar proses *polling* Modbus dan *publishing* MQTT bisa berjalan paralel tanpa saling blokir.

## Arsitektur Sistem

graph TD
    subgraph Field[Lantai Pabrik Field]
        S1[Power Meter Modbus]
        S2[Sensor Suhu/Kelembaban]
        PLC[Legacy PLC]
    end

    subgraph Gateway[STM32 Gateway]
        RS485[RS485 Transceiver]
        MCU(STM32F4/F1 Core)
        NET[W5500 Ethernet / ESP32 Coprocessor]
        
        RS485 <-->|UART/DMA| MCU
        MCU <-->|SPI/UART| NET
    end

    subgraph Cloud[Cloud / Server]
        Broker((MQTT Broker))
        DB[(InfluxDB / Timescale)]
        Dash[Grafana / ThingsBoard]
    end

    S1 <-->|RS485| RS485
    S2 <-->|RS485| RS485
    PLC <-->|RS485| RS485

    NET -->|TCP/IP| Broker
    Broker --> DB
    DB --> Dash

## Spesifikasi Target
*   **MCU**: STM32F103 / STM32F407
*   **RTOS**: FreeRTOS (via CMSIS-V2)
*   **Jaringan**: W5500 (Ethernet SPI) atau modul seluler (SIM800/A7670)
*   **Modbus Stack**: FreeModbus (Mode Master)
*   **MQTT Client**: Paho MQTT / coreMQTT

## Struktur Repo
*   `src/` - Source code utama (konfigurasi RTOS, task Modbus, task MQTT).
*   `docs/` - Skematik dasar dan penjelasan arsitektur software.
*   `lib/` - (Placeholder) Library eksternal untuk Modbus dan MQTT.

## Build Instructions
Buka file `.ioc` menggunakan STM32CubeIDE. Setup ini mengandalkan HAL library dari ST. Pastikan library W5500 dan FreeModbus sudah di-include di path compiler.
