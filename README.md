# 📡WIFI HUB for IR / RF / RFID V1.0

Hub WiFi con **ESP32-WROOM-32** y **ESPHome** para **Home Assistant**. Captura, analiza, guarda y reproduce señales **infrarrojas (IR)** y de **radiofrecuencia 433 MHz (RF)**, y lee tarjetas **RFID** con un módulo RC522 por I2C.

## Índice

1. [Hardware objetivo](#hardware-objetivo)
2. [Funciones principales](#funciones-principales)
3. [Signal Tools / IR](#signal-tools--ir)
4. [Galería](#galería)
5. [Componentes usados](#componentes-usados)
6. [Diagramas de conexiones completas](#diagramas-de-conexiones-completas)
7. [Pinouts de referencia](#pinouts-de-referencia)
8. [Tabla de conexiones](#tabla-de-conexiones)
9. [Botones](#botones)
10. [Diagrama visual de conexiones](#diagrama-visual-de-conexiones)
11. [Pin map rápido](#pin-map-rápido)
12. [Código YAML](#código-yaml)
13. [Límites conocidos](#límites-conocidos)

## Hardware objetivo

- **Placa:** ESP32-WROOM-32 (`esp32dev`), 4 MB de flash
- **Firmware:** ESPHome (archivo `esp32_ir_rf_rfid.yaml`)
- **Integración:** Home Assistant (API cifrada, sin contraseña OTA)
- **Nombre del dispositivo:** `ir-rf-rfid-wifi-hub`

## Funciones principales

- **IR:** captura raw, replay a 38 kHz, 4 slots guardados, controles virtuales, analizador, escaneo de protocolo y sniffer.
- **RF 433 MHz:** captura, replay y 1 slot guardado.
- **RFID (RC522):** lectura de UID, registro como tag en Home Assistant y UID guardado como copia.
- **Avisos:** notificación en la campana de Home Assistant y eventos `esphome.ir_detected` / `esphome.rf_detected` para automatizaciones.

## Signal Tools / IR

| Herramienta | Qué hace |
|---|---|
| **IR Raw Capture** | Captura señales raw de controles infrarrojos. |
| **IR Replay** | Reproduce la última captura con portadora de 38 kHz. |
| **Saved Captures** | Guarda capturas con nombre, y permite cargar, reproducir, renombrar o borrar. |
| **IR Remotes** | Controles virtuales con botones que apuntan a capturas guardadas. |
| **IR Analyzer** | Detector de actividad en vivo: `IDLE`, `FRAME`, `REPEAT`, `NOISE`. |
| **Protocol Scan** | Clasifica la señal como NEC, Samsung, LG, Sony, Panasonic, RC5, RC6 o RAW. |
| **IR Sniffer** | Registra eventos en vivo con protocolo, código, bits, duración y repeticiones. |
| **RF Capture / Replay** | Captura y reproduce señales RF de 433 MHz. |

## Galería

<table>
<tr>
<td align="center"><img src="https://raw.githubusercontent.com/babytoy28/WIFI-HUB-for-IR-RF-RFID-V1.0/refs/heads/main/ESP32_WROOM_32.jpg" width="240" alt="ESP32-WROOM-32"><br><sub>ESP32-WROOM-32</sub></td>
<td align="center"><img src="https://raw.githubusercontent.com/babytoy28/WIFI-HUB-for-IR-RF-RFID-V1.0/refs/heads/main/RFID_RC522.jpg" width="240" alt="Módulo RFID RC522"><br><sub>Módulo RFID RC522</sub></td>
<td align="center"><img src="https://raw.githubusercontent.com/babytoy28/WIFI-HUB-for-IR-RF-RFID-V1.0/refs/heads/main/RF_2.jpg" width="240" alt="Módulos RF 433 MHz"><br><sub>Módulos RF 433 MHz</sub></td>
</tr>
<tr>
<td align="center"><img src="https://raw.githubusercontent.com/babytoy28/WIFI-HUB-for-IR-RF-RFID-V1.0/refs/heads/main/IR_1.jpg" width="240" alt="Módulo IR"><br><sub>Módulo IR</sub></td>
<td align="center"><img src="https://raw.githubusercontent.com/babytoy28/WIFI-HUB-for-IR-RF-RFID-V1.0/refs/heads/main/FF_Cables.jpg" width="240" alt="Cables Dupont F/F"><br><sub>Cables Dupont F/F</sub></td>
<td align="center"><img src="https://raw.githubusercontent.com/babytoy28/WIFI-HUB-for-IR-RF-RFID-V1.0/refs/heads/main/RF_1.jpg" width="240" alt="Diagrama eléctrico RF"><br><sub>Diagrama eléctrico RF</sub></td>
</tr>
<tr>
<td align="center"><img src="https://raw.githubusercontent.com/babytoy28/WIFI-HUB-for-IR-RF-RFID-V1.0/refs/heads/main/IR_2.jpg" width="240" alt="Diagrama IR con USB-TTL"><br><sub>Diagrama IR con USB-TTL</sub></td>
<td></td>
<td></td>
</tr>
</table>

## Componentes usados

### 1. Cables Dupont hembra-hembra (F/F)

![Cables Dupont](https://raw.githubusercontent.com/babytoy28/WIFI-HUB-for-IR-RF-RFID-V1.0/refs/heads/main/FF_Cables.jpg)

Female to Female multicolored Dupont jumper ribbon cables. [Ver en Amazon](https://a.co/d/04rOQxWJ)

### 2. ESP32-WROOM-32

![ESP32-WROOM-32](https://raw.githubusercontent.com/babytoy28/WIFI-HUB-for-IR-RF-RFID-V1.0/refs/heads/main/ESP32_WROOM_32.jpg)

- Chip principal: ESP32-DOWDQ6-V3, MCU de 32 bits de doble núcleo con WiFi y Bluetooth
- 520 KB de SRAM, 448 KB de ROM, 16 KB de SRAM en RTC
- Almacenamiento externo: 4 MB
- Chip USB: CH340C (buena compatibilidad, descarga rápida y estable)
- Alimentación: VIN de 5-12 V (versión con batería, máximo 5,5 V), USB o 3,3 V externo

### 3. Módulo RFID RC522, 13,56 MHz

![RC522](https://raw.githubusercontent.com/babytoy28/WIFI-HUB-for-IR-RF-RFID-V1.0/refs/heads/main/RFID_RC522.jpg)

| Parámetro | Valor |
|---|---|
| Chip | MFRC522 |
| Voltaje de trabajo | 5 V |
| Corriente de trabajo | 13-100 mA / 5 V DC |
| Corriente en reposo | 10-13 mA / 5 V |
| Corriente en suspensión | < 80 µA |
| Corriente pico | < 100 mA |
| Frecuencia | 13,56 MHz |
| Protocolo | I2C |
| Dirección | 0x28 |
| Potencia | 0,5 W |
| Tamaño / peso | 56 × 40 mm / 7,6 g |
| Tarjetas | Mifare1 S50, S70, Ultralight, llaveros, monedas, tarjetas IC |

Referencia original (Arduino Uno): VCC→VCC, GND→GND, SCL→A5, SDA→A4. En el ESP32 se usan GPIO22 (SCL) y GPIO21 (SDA).

### 4. Módulo transmisor y receptor RF 433 MHz

![RF](https://raw.githubusercontent.com/babytoy28/WIFI-HUB-for-IR-RF-RFID-V1.0/refs/heads/main/RF_2.jpg)

Par de módulos para controles remotos, domótica, alarmas y aperturas de puertas.

| Parámetro | Valor |
|---|---|
| Voltaje del transmisor | 3,5-12 V DC |
| Potencia de transmisión | 10 mW |
| Frecuencia | 433,92 MHz |
| Modo | AM |
| Voltaje del receptor | 5 V DC |
| Corriente en reposo del receptor | 4 mA |
| Sensibilidad del receptor | -105 dB |
| Alcance | 20-200 m |
| Tamaño transmisor / receptor | 19 × 19 mm / 30 × 14 × 7 mm |
| Temperatura de trabajo | -25 a +85 °C |

Diagrama eléctrico:

![Diagrama RF](https://raw.githubusercontent.com/babytoy28/WIFI-HUB-for-IR-RF-RFID-V1.0/refs/heads/main/RF_1.jpg)

### 5. Módulo IR (decodificador, codificador, transmisor y receptor) 5 V

![IR](https://raw.githubusercontent.com/babytoy28/WIFI-HUB-for-IR-RF-RFID-V1.0/refs/heads/main/IR_1.jpg)

- Emisión y codificación infrarroja en formato NEC
- Interfaz de expansión para emisor infrarrojo
- Comunicación serie con nivel TTL
- Controla el 99 % de equipos NEC (TV, ventiladores, etc.)
- Chips NEC compatibles: uPD6121, uPD6122, TC9012, PT2221, PT2222, SC6121, SC6122, SC9012 y similares

Ejemplos de comandos serie:

| Acción | Comando |
|---|---|
| Transmitir NEC con código 1C 2F 33 (3 bytes de datos) | `{A1,F1,1C,2F,33}` |
| Cambiar la dirección serie a 0xA5 | `{A1,F2,A5,00,00}` |
| Cambiar la velocidad a 4800 bps (número 1) | `{A1,F3,01,00,00}` |

Diagrama de conexión con adaptador USB a TTL:

![Diagrama IR](https://raw.githubusercontent.com/babytoy28/WIFI-HUB-for-IR-RF-RFID-V1.0/refs/heads/main/IR_2.jpg)

## Diagramas de conexiones completas

Los diagramas eléctricos de cada módulo están en la sección [Componentes usados](#componentes-usados): `RF_1.jpg` (RF) e `IR_2.jpg` (IR con USB-TTL). El cableado completo al ESP32 se resume en las siguientes secciones.

## Pinouts de referencia

| Módulo | Pines |
|---|---|
| RC522 (I2C) | `SCL`, `SDA`, `V` (VCC), `G` (GND) |
| Receptor RF | `VCC`, `DATA`, `GND` |
| Transmisor RF | `VCC`, `DATA`, `GND` |
| Módulo IR | `VCC`, `GND`, señal de recepción, señal de emisión |

## Tabla de conexiones

| Función | Módulo | Pin ESP32 | Alimentación |
|---|---|---|---|
| I2C SDA | RC522 `SDA` | GPIO21 | |
| I2C SCL | RC522 `SCL` | GPIO22 | |
| RC522 VCC / GND | RC522 `V` / `G` | 5V (VIN) / GND | 5 V |
| IR RX | Módulo IR (salida del receptor) | GPIO14 | 5 V |
| IR TX | Módulo IR (entrada del emisor) | GPIO25 | 5 V |
| RF RX | Receptor RF `DATA` | GPIO32 | 5 V |
| RF TX | Transmisor RF `DATA` | GPIO26 | 3,5-12 V |

## Botones

Entidades que aparecen en Home Assistant:

| Grupo | Botones / interruptores |
|---|---|
| IR | IR Replay (última captura), IR Guardar en Slot, IR Reproducir Slot, IR Cargar Slot como Última, IR Borrar Slot |
| Controles IR | Control IR - Botón 1, 2, 3 y 4 (slots 1 a 4) |
| RF | RF Replay (última captura), RF Guardar Captura, RF Replay Guardada |
| RFID | RFID Guardar UID leído, RFID Escribir o Clonar |
| Interruptores | IR Capture Activa, RF Capture Activa, Notificar IR en HA, Notificar RF en HA |
| Selector / texto | IR Slot, IR Slot Nombre, RFID UID Guardado |

## Diagrama visual de conexiones 
(https://raw.githubusercontent.com/babytoy28/WIFI-HUB-for-IR-RF-RFID-V1.0/refs/heads/main/diagrama.png)

## Pin map rápido

| GPIO | Uso | Dirección |
|---|---|---|
| 14 | IR RX | Entrada |
| 25 | IR TX | Salida |
| 26 | RF TX | Salida |
| 32 | RF RX | Entrada |
| 21 | I2C SDA (RC522) | Bidireccional |
| 22 | I2C SCL (RC522) | Salida |

## Código YAML

Configuración completa de ESPHome para el hub.

**Descargar:** [wifi_hub_for_ir_rf_rfid_v1.0.yaml](https://github.com/babytoy28/WIFI-HUB-for-IR-RF-RFID-V1.0/blob/main/wifi_hub_for_ir_rf_rfid_v1.0.yaml)

### Uso

1. Descarga el archivo `wifi_hub_for_ir_rf_rfid_v1.0.yaml`.
2. Crea o edita tu `secrets.yaml` con `wifi_ssid`, `wifi_password` y `api_key`.
3. Cárgalo en ESPHome (Device Builder o `esphome run`) y flashea el ESP32 por USB la primera vez.
4. Añade el dispositivo en Home Assistant (Ajustes → Dispositivos y servicios → ESPHome).
5. Activa "Permitir que el dispositivo realice acciones de Home Assistant" para recibir los avisos.

### Pines configurables

```yaml
substitutions:
  name: ir-rf-rfid-wifi-hub
  friendly_name: "IR RF RFID WIFI HUB"
  ir_rx_pin: GPIO14
  ir_tx_pin: GPIO25
  rf_rx_pin: GPIO32
  rf_tx_pin: GPIO26
  sda_pin: GPIO21
  scl_pin: GPIO22
```

## Límites conocidos

- **IR guardado:** hasta 256 pulsos por captura y 4 slots. Los mandos largos (aire acondicionado) pueden quedar cortados.
- **RF guardado:** 1 slot de hasta 300 pulsos. El receptor de 433 MHz capta mucho ruido, por lo que se ignoran capturas de menos de 20 pulsos.
- **RFID:** el componente `rc522_i2c` de ESPHome solo lee UIDs. Escribir o clonar tarjetas requiere un componente externo y tarjetas "magic" (Gen1/Gen2). El botón "RFID Escribir o Clonar" es solo un marcador.
- **Niveles de voltaje:** el ESP32 trabaja a 3,3 V en sus GPIO. Los módulos alimentados a 5 V (RC522, receptor RF, módulo IR) pueden entregar señales de 5 V, así que verifica las salidas con un multímetro y usa un divisor de tensión o un convertidor de nivel si es necesario.
- **Módulo IR con chip NEC:** el firmware maneja el IR directamente por GPIO (receptor y emisor) y no usa el modo serie NEC del módulo. Confirma qué pines de señal expone tu placa.
- **Versión de ESPHome:** probado con la sintaxis de ESPHome reciente (`transmitter_id`, `homeassistant.action`, `non_blocking`).
- **Avisos a Home Assistant:** requieren activar "Permitir que el dispositivo realice acciones de Home Assistant" en la integración ESPHome.
