# Aidon HAN to RS485 Adapter

A small PCB that reads data from the Aidon electricity meter's HAN port and converts it to an RS485 bus, enabling integration with home automation systems, data loggers, and industrial equipment.

**PCB size:** 45 × 25 mm, 4-layer  
**Designed:** November 2022 by Yyberi

---

## Background

Aidon smart electricity meters (used by many Nordic utilities) expose a **HAN port** — a standard RJ12 connector that outputs UART serial data (DLMS/COSEM P1 telegrams) at 115200 baud. This board taps into that port and re-transmits the data over RS485, which is suited for longer cable runs and multi-drop topologies.

---

## How It Works

```
Aidon meter
  HAN port (RJ12)
       │
       ├─ +5V ──────────────────── powers the board
       └─ TX (UART 115200 baud) ── NPN buffer (Q1) ──► MAX13487E ──► RS485 (A/B)
```

1. The meter provides 5 V on the HAN port — no external power supply needed.
2. The UART TX line from the meter is buffered through an NPN transistor (Q1) that also handles the logic-level inversion.
3. The MAX13487EESA+T RS485 transceiver with automatic direction control retransmits the data on the RS485 bus.
4. TVS diode (D3) protects the RS485 lines against ESD and transients.
5. Two LEDs indicate power (D2) and data transmission activity (D1).

---

## Connectors

| Ref | Connector | Function |
|-----|-----------|----------|
| J1  | RJ12 (DS1133-S60BPX) | HAN port — plugs into meter |
| J2  | 2-pin header 2.54 mm | Alternative/external power input (optional) |
| J3  | Phoenix Contact 3-pin 5.08 mm | RS485 output: A, B, GND |

### J1 — HAN Port Pinout (RJ12)

| Pin | Signal | Description |
|-----|--------|-------------|
| 1   | +5V    | Power from meter |
| 2   | –      | Not connected |
| 3   | DATA   | UART TX from meter (115200 8N1) |
| 4   | –      | Not connected |
| 5   | GND    | Ground |
| 6   | –      | Not connected |

### J3 — RS485 Output

| Pin | Signal | Board silkscreen (top→bottom) |
|-----|--------|-------------------------------|
| 1   | GND    | GND (bottom)                  |
| 2   | A (+)  | A (middle)                    |
| 3   | B (–)  | B (top)                       |

---

## Bill of Materials

| Ref | Value / Part | Package | LCSC # | Qty |
|-----|-------------|---------|--------|-----|
| IC1 | Maxim MAX13487EESA+T | SOIC-8 | C18347 | 1 |
| J1  | CONNFLY DS1133-S60BPX (RJ12) | Horizontal | C77859 | 1 |
| J2  | Molex 22284020 | 2×1 pin header 2.54 mm | C234182 | 1 |
| J3  | Phoenix Contact 1711738 | 3-pin 5.08 mm | C91156 | 1 |
| Q1  | MMS9013-H-TP (NPN) | SOT-23 | C90160 | 1 |
| D3  | ProTek PSM712-LF-T7 (TVS) | SOT-23 | C32677 | 1 |
| D1, D2 | NationStar NCD0805R1 (LED red) | 0805 | C84256 | 2 |
| R1, R3 | 510 Ω | 0805 | C17734 | 2 |
| R2  | 4.7 kΩ | 0805 | C17673 | 1 |
| R4  | 120 Ω (RS485 termination) | 0805 | C17437 | 1 |
| C1, C2 | 100 nF | 0805 | C49678 | 2 |

All LCSC part numbers are compatible with **JLCPCB SMT assembly**.

---

## Manufacturing

Gerber files, drill files, and pick-and-place position files are in the `AidonToRS485v2-gerbers/` folder.

### Ordering from JLCPCB

1. Zip the contents of `AidonToRS485v2-gerbers/`.
2. Upload to [jlcpcb.com](https://jlcpcb.com) and select **4 layers**.
3. For SMT assembly, upload the BOM (`BOM_AidonToRS485v2.xlsx`) and the position files (`*-top-pos.csv`).

**Recommended PCB stackup:** standard JLC04161H-7628 (or equivalent).

---

## Data Format

The Aidon meter outputs **DLMS/COSEM** P1 telegrams (similar to DSMR P1). Data is sent as UART at **115200 baud, 8N1** (8 data bits, no parity, 1 stop bit).

Libraries for parsing Aidon P1 data:
- [han-passthrough](https://github.com/turbokongen/hass-AMS) — Home Assistant integration
- [aidon-han-parser](https://github.com/roarfred/AmsToMqttBridge) — AMS to MQTT bridge

---

## Getting Started

1. **Plug** the RJ12 cable into the meter's HAN port and the board's J1.
2. **Connect** your RS485 device or bus to J3 (A, B, GND).
3. **Power** is sourced directly from the meter. No external supply needed.
4. Configure your RS485 receiver for **115200 baud, 8N1**.
5. Data telegrams arrive automatically — the meter pushes data every 10 seconds (or as configured by the utility).

> **Note:** Some utilities require you to request HAN port activation from your grid operator before data is available.

---

## Repository Contents

```
AidonToRS485_v2/
├── AidonToRS485.kicad_pro       # KiCad project file
├── AidonToRS485.kicad_sch       # Schematic
├── AidonToRS485.kicad_pcb       # PCB layout
├── AidonToRS485.step            # 3D model (STEP)
├── BOM_AidonToRS485v2.xlsx      # Bill of materials (JLCPCB format)
├── BOM_AidonToRS485.csv         # Bill of materials (CSV)
└── AidonToRS485v2-gerbers/      # Gerber + drill + position files
```

---

## License

This hardware design is released under the **CERN Open Hardware Licence v2 – Permissive (CERN-OHL-P)**. You are free to use, modify, and manufacture this design, with or without attribution, and without requiring derivatives to be open source.

See [ohwr.org/cern_ohl_p_v2.txt](https://ohwr.org/cern_ohl_p_v2.txt) for the full license text.
