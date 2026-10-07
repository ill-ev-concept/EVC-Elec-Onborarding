# Lights Microcontroller Backpack

Two-layer STM32F103C8T6 board with isolated CAN and two buffered WS2812 interfaces.

| Item | Value |
|---|---|
| Contributor | greyzhu |
| GitHub | saffiotialfredo949-ctrl |
| Discord | grey077913 |
| KiCad | 10.0.6 |
| Board | 100 x 80 mm, two layers, 1.6 mm FR-4 |

## Open the project

Open `Lights_Backpack.kicad_pro` in KiCad. Keep the directory structure intact: the symbol library, footprint library and library tables use `${KIPRJMOD}` paths.

## Contents

- `Lights_Backpack.kicad_sch` and `Lights_Backpack.kicad_pcb`: schematic and routed PCB.
- `Lights_Backpack.pdf`: schematic preview.
- `Backpack.kicad_sym`, `Backpack.pretty/` and library tables: project libraries.
- `BOM.csv`: component list; blank MPN fields require purchasing selection.
- `Component_Positions_Reference.csv`: placement reference in KiCad absolute coordinates.
- `manufacturing/`: nine Gerbers, one Gerber job and two Excellon drill files.
- `ERC_Passed.rpt`, `DRC_Passed.rpt` and `Delivery_Checks.json`: design checks.
- `SHA256SUMS.txt`: file integrity manifest.
- [Hardware guide](HARDWARE_GUIDE.md): interfaces, power limits, fabrication details and initial test procedure.

## Validation and status

Native KiCad ERC reports zero errors and warnings. Native DRC reports zero violations, unconnected pads and footprint errors, with schematic parity enabled. These results apply to the configured checks; the reports list disabled categories.

The design is ready for team review. No prototype has been assembled or tested, and firmware is not included. Before fabrication or assembly, confirm the mating harness pinout, remaining component MPNs and system power budget. Each LED port targets at most 0.4 A before thermal and voltage derating. The CAN side requires an external isolated 5 V supply. J1 uses the custom team programming pinout.
