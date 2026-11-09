# CLAUDE.md

## Project

Solar MPPT charge controller, a 64 × 42 mm, 2-layer board around the CN3722. It is based on Malte Pöggel's design and must keep all of its options: chemistry, charge current, MPPT voltage, NTC modes, SMD or through-hole `C1`, and FOCV tracker compatibility.

## Where things live

The PCB design itself is in EasyEDA Pro, not in git: project `Solar Power Module CN3722 V2`. The `docs/` and `release/` folders hold only its records:

- `docs/SPEC.md`: requirements, build options and differences from the original.
- `docs/DESIGN-RECORD.md`: gate log, rules, step 4–8 decisions and evidence, and open items before ordering.
- `release/rev2/BOM.md`: full parts list, generated from the EasyEDA BOM export.

When a record and the CAD disagree, read the CAD. Update the records after any board change.

## Working rules

- Reply in Russian. Write all files and EasyEDA content in English.
- Follow the gated process of the `power-pcb-design` skill. Log gate decisions in `docs/DESIGN-RECORD.md`.
- Never order, upload to a fabricator or publish without explicit permission. Commit only when asked.
- Check any change against every build option, not only the default build.
- State what was calculated, checked in CAD or measured. Nothing on this board has been measured yet.

## EasyEDA bridge

Use the `easyeda-api` skill. Its bridge runs on `http://localhost:49620/execute` with a 30 s call limit, so batch long operations.

- PCB layer IDs: Top 1, Bottom 2, Top Silkscreen 3, Board Outline 11, Multi 12.
- PCB coordinates are in mil: mm × 39.3700787.
- Activate the PCB tab first. The 2D/3D preview tab has document type 15, and PCB calls then fail with null errors or `指定的主题消息在对应的画布内没有相关订阅`.
- `importChanges()` opens a confirmation dialog and returns true before anything changes. `force=true` did not skip it. Read the dialog, then press "Apply Changes".
- Pass `overwriteCurrentRuleConfiguration()` the `config` object, not the whole rule configuration.
- Pour priority: the lower number fills first. Rebuild pours in priority order with `rebuildCopperRegion()`. GND stays last on Top.
- Poured polygon coordinates are in 10 mil units.
- `getState_Hole()` does not return mil. Confirm drills in the generated `Drill_*.DRL`.
- Net port rotation cannot be changed with `modify`. Delete and recreate the port, then check the pin nets.
- `sch_Netlist.getNetlist()` and `lib_Footprint.getDocumentSource()` can hang. Use `sch_ManufactureData.getNetlistFile()`. To read a library footprint, place it outside the board, read the pads, then delete it.
- Gerber X uses mm from 4020 mil, and Y from −3392 mil. Y can be negative.
- Export `ComponentSilkscreen` only with `getGerberFile` to measure real silkscreen outlines.

## Board constraints that are easy to break

- `R10`: CSP and BAT must reach only the inner pad edges. Do not let `Z_V_BAT` or `Z_L_R10` cover those edges.
- Keep power pads on `K1`, `K2`, `K3` connected directly. Spokes are too thin for 3 A.
- The silkscreen is not checked by DRC. Keep at least 0.5 mm between part outlines and check them from Gerber.
- DRC does not report necks in pours. Measure them on the poured copper after any power copper change.
- `C11` is centred on `C1` on purpose.
- Keep the author credit on the bottom silkscreen. The license requires it.
