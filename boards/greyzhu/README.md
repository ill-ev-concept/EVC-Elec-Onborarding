# Lights Microcontroller Backpack - greyzhu

- GitHub username (provided screenshot): `saffiotialfredo949-ctrl`
- Display name: `Greyzhu0511`
- Discord: `grey077913`
- Submission branch and directory name: `greyzhu` (student-selected name)
- KiCad version: 10.0.6

Open `Lights_Backpack.kicad_pro`. The schematic, routed two-layer PCB, symbol and footprint tables, Gerber files, and PTH/NPTH drill files are included. Project-local libraries use `${KIPRJMOD}` and are bundled so the project can be reviewed without machine-specific library paths. Shared repository libraries have not been modified.

The 100 x 80 mm board includes an STM32F103C8T6, 8 MHz crystal, 5 V to 3.3 V LDO, custom team programming header, ISO1050 isolated CAN interface, RJ45 connector, and two buffered WS2812 outputs with fused 5 V supplies.

Native KiCad checks passed under the configured rules: ERC 0 errors / 0 warnings; DRC 0 violations / 0 unconnected pads / 0 footprint errors, including schematic parity. See `ERC_Passed.rpt` and `DRC_Passed.rpt` for the disabled check categories. No physical prototype or firmware has been tested.

Review assumptions: each LED port targets at most 0.4 A before thermal/voltage derating; CAN-side isolated 5 V is supplied externally; J1 uses the custom team pinout; verify the mating LED harness pinout before assembly. BOM entries without MPN require final purchasing selection. Component position CSV uses KiCad absolute coordinates and is a reference, not a vendor-qualified placement order.

See `README_使用说明.md` for complete pin assignments, fabrication parameters, power assumptions, and design references. `manufacturing/` contains nine Gerbers, one job file and two drill files. The PDF is the schematic preview.

This is prepared for team review. PR creation and review approval are separate from local design completion.
