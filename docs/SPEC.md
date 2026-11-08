# Solar Power Module CN3722 — PCB specification

The board charges a LiIon or LiFePO4 battery from a solar panel and feeds a load. It is based on [Malte Pöggel's CN3722 MPPT charger](https://www.maltepoeggel.de/?site=solar-mppt-cn3722).

## 1. Requirements

- Keep every option of the original board: chemistry, charge current, MPPT voltage, NTC modes, SMD or through-hole `C1`, and the FOCV tracker add-on.
- Carry up to 3 A of charge current.
- Survive reversed panel and battery wiring.
- Use EasyEDA Pro, with LCSC part codes for fitted parts.

## 2. Build options

The board is one layout. The values below select the build.

### Chemistry

`Vout = 2.416 × (1 + R7/R6) + 50 nA × R7`, `C7 = 8 × R6/R7` pF.

| Battery | Charge voltage | R6 | R7 | C7 |
|---|---|---:|---:|---:|
| LiIon 1S | 4.2 V | 48.7 kΩ | 36 kΩ | 12 pF |
| LiIon 2S | 8.4 V | 48.7 kΩ | 120 kΩ | 3.3 pF |
| LiIon 3S | 12.6 V | 48.7 kΩ | 205 kΩ | 2.2 pF |
| LiFePO4 1S | 3.6 V | 48.7 kΩ | 23.7 kΩ | 18 pF |
| LiFePO4 2S, default | 7.2 V | 101 kΩ | 200 kΩ | 4.7 pF |
| LiFePO4 3S | 10.8 V | 48.7 kΩ | 169 kΩ | 3.3 pF |

Parts in the board's series (Yageo `RT0805BRD07` 0.1 %, Samsung `CL21C` C0G 50 V), found in the EasyEDA library:

| Value | MPN | LCSC |
|---|---|---|
| 23.7 kΩ | `RT0805BRD0723K7L` | C309659 |
| 36 kΩ | `RT0805BRD0736KL` | C865432 |
| 48.7 kΩ | Not found in this series | — |
| 101 kΩ | `RT0805BRD07101KL` | C7302896 |
| 120 kΩ | `RT0805BRD07120KL` | C865161 |
| 169 kΩ | `RT0805BRD07169KL` | C706218 |
| 200 kΩ | `RT0805BRD07200KL` | C728645 |
| 205 kΩ | Not found in this series | — |
| 2.2 pF | Not found in this series | — |
| 3.3 pF | `CL21C3R3CBANNNC` | C170106 |
| 4.7 pF | `CL21C4R7CBANNNC` | C85304 |
| 12 pF | `CL21C120JBANNNC` | C170110 |
| 18 pF | `CL21C180JBANNNC` | C170109 |

The table comes from the original design, except the default pair. Use 0.1% resistors. With 1% resistors and the CN3722 FB tolerance, a 4.2 V build can reach 4.29 V. Even with 0.1% parts the worst case is about 4.26 V, so measure each board. `R16` lowers the voltage if needed.

### Charge current

`Ich = 200 mV / R10`.

| Current | R10 | L1 | F1 |
|---|---:|---|---|
| 1 A, default | 200 mΩ | 33 µH, `CDRH125NP-330MC` | 1.5 A, `2920L150DR` |
| 2 A | 100 mΩ | 33 µH, `CDRH125NP-330MC` | 2.6 A, `2920L260/24DR` |
| 3 A | 68 mΩ | 15 µH, `744771115` | 3 A, `2920L300/16GR` |

`CDRH125NP-330MC` saturates at 3.08 A, so it is not used at 3 A. All three inductors share the same footprint. All fuses use the 2920 footprint. `2920L300/16GR` is rated for 16 V, which is enough for 3S with little margin.

### Temperature sensor

| Mode | R2 | R12 | D5 | K4 |
|---|---|---|---|---|
| External NTC, default | Not fitted | 200 Ω | Fitted | Fitted |
| On-board NTC | 10 kΩ NTC | 0 Ω | Not fitted | Not fitted |
| No sensor | 10 kΩ resistor | 0 Ω | Not fitted | Not fitted |

The external sensor is the default because only it measures the battery. With no sensor connected, the charger does not start.

### Other options

| Part | Option |
|---|---|
| `C1`, `C11` | SMD or through-hole, 3.5 mm pitch. Fit one |
| `R15` | 20 kΩ across the input, if diode leakage disturbs sleep mode |
| `R14` | 0 Ω by default (`RC0805JR-070RL`). Use 4.7–10 Ω if the gate rings: `RC0805FR-074R7L` (C137513) or `RC0805FR-0710RL` (C96347) |
| `R2` as a resistor | 10 kΩ, `RC0805FR-0710KL` (C84376) |

## 3. Differences from the original

| Item | Original | This board |
|---|---|---|
| Size | 60 × 34 mm | 64 × 42 mm |
| Battery connector | JST XH, keyed | Screw terminal, with reverse protection on `T3` |
| Panel reverse protection | None | `T2`, `D6`, `R13` |
| Input surge protection | None | `D7`, SMBJ24A |
| `T1` | SI4435 | AO4419 |
| `C6` | Tantalum, 6032 | 10 µF 50 V X7R, 1210 |
| Passive parts | Mixed manufacturers | All ceramics Samsung CL, all chip resistors Yageo |
| `F1` | 1812 | 2920 |
| `R1`, `R5` | 2.7 kΩ | 6.8 kΩ, for 28 V at 0805 |
| Input bypass | `C8` only | `C8`, plus `C9`, `C10` at the switch |
| Gate resistor | None | `R14` |
| Test points | `TP1` | `TP1`, `TP2`, `TP3` |

## 4. Input limit

`D7` starts conducting at 26.7 V. A panel with Voc above about 26 V at the coldest temperature overloads it. A standard 36-cell panel fits. 60-cell and 72-cell panels do not.

## 5. Sources

- [Original project, Malte Pöggel](https://www.maltepoeggel.de/?site=solar-mppt-cn3722)
- [FOCV tracker, Malte Pöggel](https://www.maltepoeggel.de/?site=solar-mppt-focv&lang=en)
- [CN3722 data sheet, Rev 1.1](https://www.maltepoeggel.de/data/solar-mppt-cn3722/cn3722_datasheet_1702868102946736.pdf)
- [CDRH125 data sheet](https://products.sumida.com/products/pdf/CDRH125.pdf)
- [NTCS0805E3 data sheet, Vishay](https://www.vishay.com/docs/29044/ntcs0805e3t.pdf)
