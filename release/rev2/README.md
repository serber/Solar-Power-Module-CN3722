# Release rev2

Prototype, exported from EasyEDA Pro 3.2.209 on 2026-11-07. BOM exported again on 2026-11-08 after the passives change; Gerber and placement unchanged. **Not validated and not ordered.**

| File | Content |
|---|---|
| `gerber-rev2.zip` | Gerber and drill, 2 layers. Upload this to the fab |
| `BOM.md` | Parts list with order links |
| `bom-rev2.csv` | EasyEDA BOM export, 42 fitted parts, UTF-16 |
| `placement-rev2.csv` | Part positions, all 49 parts including DNF, UTF-16 |
| `SHA256SUMS` | Checksums |

Default build: LiFePO4 2S, 7.21 V, 1 A, external NTC. The order instruction is in the [root README](../../README.md#ordering).

## Checks on these files

- DRC on the saved PCB: 0 errors, immediately before export.
- Drill: 63 vias, 16 plated through-hole positions, 4 non-plated mounting holes.
- The author credit on the bottom silkscreen clears all vias and pads (Gerber).

## Not verified

- Fabricator DFM, enclosure fit, LCSC stock and prices.
- Open items: [`docs/DESIGN-RECORD.md`](../../docs/DESIGN-RECORD.md#open-items-before-ordering).
