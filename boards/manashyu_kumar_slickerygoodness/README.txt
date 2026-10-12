Routed PCB draft - 2026-10-11

Open onboarding project.kicad_pro, then its PCB.
32 components on an 85 x 65 mm, two-layer board.
Original project is unchanged; this folder is the edited copy.

Completed: component placement, routing, main GND and separate GND_CAN copper fills, and MCU ground vias.
KiCad 10.0.7 DRC including schematic parity: 0 violations, 0 unconnected items, 0 schematic parity issues.
See routed-drc.json for the report and its existing ignored-check settings.
The original placement-preview.png shows the earlier unrouted stage; routed-preview.png and routed-back-preview.png show the current copper layout.

Before fabrication: review against assignment dimensions and connector orientation, verify downloaded footprints/pin numbering against the exact purchased parts, and confirm LED load current and power-track capacity. Ground separation here is not a certified isolation barrier. DRC verifies configured layout rules, not circuit function or current/thermal ratings. Gerber and Excellon drill files were exported on 2026-10-12 into manufacturing and packaged in manufacturing.zip. Includes both copper, mask and silkscreen layers, board outline, job file, and separate plated/nonplated drills. Export checks: closed 85 x 65 mm outline, 50 plated holes and 8 nonplated holes. DRC rerun before export: zero violations, unconnected items, or schematic parity issues.

