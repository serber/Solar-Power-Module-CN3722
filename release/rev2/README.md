# Release rev2

Prototype release, exported from EasyEDA Pro 3.2.209 on 2026-11-07. `bom-rev2.csv` was exported again on 2026-11-08 after the passive parts were moved to Samsung and Yageo; the Gerber and placement files were not changed by that. **Not validated and not ordered.** It has not been built or measured.

| File | Content |
|---|---|
| `gerber-rev2.zip` | Gerber and drill files, two layers. Upload this to the fab |
| `BOM.md` | Parts list with order links |
| `bom-rev2.csv` | EasyEDA BOM export, 42 fitted parts in 37 lines, UTF-16 |
| `placement-rev2.csv` | Part positions, all 49 parts, UTF-16 |
| `SHA256SUMS` | Checksums |

This is the default build: LiFePO4 2S, 7.21 V, 1 A, external NTC. Other builds change `R6`, `R7`, `C7`, `R10`, `L1`, `F1`, and the NTC parts, see [`docs/SPEC.md`](../../docs/SPEC.md#build-options).

`C11`, `R2`, `R15`, `R16`, `TP1`, `TP2` and `TP3` are not fitted. They are absent from the BOM but present in the placement file, so remove them if a fabricator assembles the board.

The order instruction is in the [root README](../../README.md#ordering).

## Checks on these files

- DRC on the saved PCB: 0 errors, immediately before export.
- Drill: 63 vias, 16 plated through-hole positions, 4 non-plated mounting holes.
- BOM holds 42 parts, placement holds 49.
- The author credit is on the bottom silkscreen and clears all vias and pads, checked in the Gerber.

## Not verified

- The fabricator's DFM check has not been run.
- Fit with an enclosure has not been checked.
- LCSC stock and prices have not been checked.
- See "Open items before ordering" and "Check on the first board" in [`docs/DESIGN-RECORD.md`](../../docs/DESIGN-RECORD.md#open-items-before-ordering).
