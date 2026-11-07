# Solar Power Module CN3722 — design record

Evidence labels used here: **calculated**, **CAD check**, **measured**. Nothing on this board has been measured.

## Gate log

All decisions are dated 2026-11-07.

| Step | Decision | Changes from the reviewer |
|---|---|---|
| Review of V2 | Approved | Keep every universal option. Author's name to be added later |
| 4 Schematic | Approved | |
| 5 Rules, stack-up | Approved | |
| 6 Placement | Approved after rework | TP1 left of R11. No overlaps, silkscreen included. Board may grow, no tight placement. Reviewer moved parts by hand |
| 7 Routing | Approved, mode A, block gates kept | Use copper on both layers |
| 8 Verification | Accepted | Repository structure to follow `serber/tiny-gps-logger` |
| 9 Delivery | Approved | README must list every difference from the original and the reason. Reviewer added the author credit on the bottom silkscreen, release exported again |

## Tool inventory

| Tool | Where | Version |
|---|---|---|
| EasyEDA Pro | Reviewer's computer, driven through the WebSocket bridge | 3.2.209 |

## Findings on the first V2 layout

These drove the revision.

| Finding | Evidence | Fix |
|---|---|---|
| `R2` was a 33 kΩ NTC. The charge window moved to about 29–89 °C | Data sheet, calculated | 10 kΩ NTC, not fitted by default |
| Power tracks 0.25–0.38 mm held about 1.2–1.6 A | Calculated, IPC-2221 | Copper pours |
| CSP tapped 6.5 mm from `R10`, adding about 12.6 mΩ. Current was 0.94 A at 1 A and 2.48 A at 2.94 A | Calculated | Kelvin connection |
| No reverse battery protection, and `C6` tantalum exposed | Analysis | `T3`, MLCC |
| `R6`, `R7`, `C7` differed between schematic and PCB | DRC netlist error | Imported, 0.1% parts |
| `R1`, `R5` at 0.25 W on 0805 at 28 V | Calculated | 6.8 kΩ |

## Architecture

Non-synchronous buck: `T1` switches, `D1` blocks reverse current, `D2` freewheels. `T2` protects the input and `T3` the battery return.

The commutation loop is `C9`/`C10` → `T1` → `D1` → `D2` → GND → `C9`/`C10`.

`T3` has its drain on `BAT_N`, source on GND and gate fed from `V_BAT`. The gate is fed from `V_BAT`, not from the battery, so a pack at 0 V can still be charged.

## Electrical net table

Worst case: 3 A charge, 12.6 V battery, 18 V input, D about 0.7. All values are calculated.

### Branches

| Branch | Current | Layout constraint |
|---|---|---|
| Input to `T1` | 2.1 A average, 2.5 A RMS | Polygon, at least 1 mm |
| SW, `L1` | 3 A plus 0.4 A ripple | Short polygon, small area |
| `L1` to `R10` to outputs | 3 A | Polygon, at least 1 mm |
| Battery return through `T3` | 3 A | Polygon, at least 3 vias at the source |
| CSP, BAT | About 10 µA | 0.25 mm pair from the inner pad edges |

### Sensitive nets

| Net | Risk | Layout rule |
|---|---|---|
| FB | High impedance | Short, away from SW, taken at `C6` |
| CSP, BAT | 200 mV signal | Kelvin at `R10`, run together |
| COM1–3 | Loop stability | Return to U1 ground |
| MPPT | Temperature drift of U1 | U1 away from heat sources |

## Step 4 audit

- ERC: 0 errors, 1 warning. The warning text is not available through the API. Read it in the DRC panel.
- All nets checked against the schematic netlist.
- `K1` net loss after a port change was found this way and fixed.
- Footprint check: `L1` pads for `CDRH125NP-330MC` and `744771115` differ by 0.2 mm in spacing, same size, so one footprint serves both.
- `D6` polarity: pad 1 is the cathode, by the footprint marking. `D8` confirms it, its pins are named.

## Step 5: rules and stack-up

2 layers, FR-4, 1.6 mm, 1 oz copper (assumed, not stored in the project).

All nets stay below 30 V and no safety standard applies, so insulation was not derived.

| Rule | Value |
|---|---|
| `HICUR` track | 1.0 mm min, 1.5 mm default |
| `POWER` track | 0.254 mm min, 1.0 mm default |
| `Default` track | 0.254 mm |
| Clearance | 0.2 mm, 0.254 mm to pours, 0.3 mm to the board edge |
| Via | 0.3/0.6 mm signal, 0.4/0.8 mm power |
| Pour connection | Direct on SMD pads, 0.5 mm spokes on through-hole pads |

`POWER` keeps a 0.254 mm minimum because CSP and BAT share nets with power copper. DRC therefore does not check the wide copper of those nets, so its width was measured in step 8.

## Step 6: placement

- `L1` pad 1 sits next to the `D1` and `D2` cathodes, so SW stays small.
- `C9` and `C10` close the loop between the `T1` source and the `D2` anode.
- U1 is 6 mm or more from `D2` and `T1`.
- `C11` is centred on `C1`. They are alternatives.
- Parts were first checked by pads only, which missed real overlaps. Silkscreen is now checked from Gerber: minimum gap between parts 0.49 mm (CAD check).

## Step 7: routing

Mode A with block gates. Result: 63 vias, 46 of them GND. DRC 0 errors, 0 unconnected (CAD check).

| Deviation | Reason |
|---|---|
| CSP dives under BAT for 1.8 mm near `R10` | BAT is above CSP at U1 and below it at `R10`, so the pair must cross |
| `T1` gate on Bottom, 18 mm | Leaves the Top return path whole. It cuts a 0.8 × 10 mm slot under `C9`. If the gate rings, fit `R14` |
| COM3 about 13 mm | `C2` is far from pin 11 |
| FB, CHRG, DONE, TEMP on Bottom | Crossings. Top GND stays continuous there |

## Step 8: verification

### Copper

Width was measured on the poured copper with a 2 mil grid. Capacity is calculated with IPC-2221, external layer, 1 oz, 20 °C rise.

| Branch | Min width | Capacity | Needed |
|---|---:|---:|---:|
| `K1` to `T2` | 1.22 mm | 3.7 A | 2.1 A |
| `T2` to `C1`+ to `T1` | 2.0 mm | 5.4 A | 2.5 A |
| `T1` drain to `D1` | 1.4 mm | 4.2 A | 2.5 A |
| `D1`, `D2` to `L1` | 1.4 mm | 4.2 A | 3.4 A |
| `L1` to `R10` | 1.12 mm | 3.5 A | 3.4 A |
| `R10` to `F1` | 1.7 mm | 4.8 A | 3 A |
| `R10` to `K3`, `F1` to `K2`, `K2` to `T3` | 1.22 mm | 3.7 A | 3 A |
| `C9` to `D2` return | 3.6 mm | 8.1 A | 2.5 A |

The Bottom layer was reviewed in the 3D view: solid GND, with slots only along the Bottom signal tracks. The longest is the CHRG and DONE pair, run side by side.

This check found a 0.5 mm neck in `V_BAT` and 0.5 mm spokes on the power terminals, which DRC does not report. Both were fixed. GND is one piece on each layer.

### Losses at 3 A

Calculated, with Vf 0.5 V, Rds 20 mΩ, 80 ns switching and an assumed `F1` resistance.

| | 1S, 18 V in | 3S, 18 V in |
|---|---:|---:|
| `D1` / `D2` | 0.4 / 1.1 W | 1.1 / 0.4 W |
| `T1` | 0.7 W | 0.8 W |
| `L1` / `R10` | 0.25 / 0.6 W | 0.25 / 0.6 W |
| Total | 3.6 W | 3.9 W |
| Efficiency | 78% | 90% |

The hottest diode is estimated at 50–75 °C above ambient and `T1` at about 40 °C, from typical package data. This is close to the limit in a closed enclosure.

### Loop

Commutation loop about 137 mm², estimated 15–20 nH, about 1.5 V overshoot at 3 A. These are rough calculations, not an extraction.

### Open items before ordering

- Run the fabricator's DFM check.
- Check LCSC stock, prices and links.
- Read the data sheets of `AO4419-JSM`, `AO3416A-HXY` and `2920L300/16GR`.
- Check the `3296W` pinout against the `R11` footprint.
- Read the ERC warning in the EasyEDA DRC panel.
- Check part heights and enclosure fit.
- Check clearance for the FOCV tracker above `R11`.

### Check on the first board

Build it at 1 A first.

- Charge voltage, current and termination, for each chemistry used.
- Reversed panel and reversed battery.
- NTC limits at 0 °C and 50 °C.
- SW and gate waveforms, ringing, loop stability.
- Temperatures at 1 A, 2 A and 3 A.
- EMC is not assessed.
