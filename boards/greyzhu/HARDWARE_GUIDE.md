# Lights Backpack Hardware Guide

Revision A. Design checks performed on October 7, 2026, with KiCad 10.0.6.

## Circuit overview

The board includes an STM32F103C8T6 minimum system, reset and boot configuration, an 8 MHz crystal, a 5 V to 3.3 V regulator, the team programming interface, an ISO1050 isolated CAN transceiver, an RJ45 power/CAN connector, two buffered WS2812 outputs with fused power, and four M4 mounting holes.

BOOT0 and BOOT1 have 10 kohm pull-down resistors for booting from main Flash. SW1 pulls NRST low to reset the MCU. U3 supplies the 3.3 V rail; LED power comes directly from the protected 5 V rail. U4 and U5 are 5 V SN74AHCT1G125 buffers that translate the MCU data outputs to LED logic levels.

The crystal is ABM3B-8.000MHZ-10-1-U-T. C15 and C16 are each 15 pF, based on a 10 pF load specification and an estimated 2.5 pF stray capacitance. Startup and frequency accuracy require prototype verification.

## J1: programming interface

Custom 2 x 5, 2.54 mm pitch header.

| Pin | Signal | Pin | Signal |
|---|---|---|---|
| 1 | +5V | 2 | +5V |
| 3 | SWCLK | 4 | Target +3.3V |
| 5 | SWO | 6 | Target +3.3V |
| 7 | SWDIO | 8 | Not connected |
| 9 | GND | 10 | GND |

This is a team-specific pinout, not a standard ARM 10-pin header. Wire an ST-Link adapter by signal name. Pins 4 and 6 provide the target voltage reference; do not connect them to a debugger's 3.3 V power output. J1 connects directly to the board's +5V rail without power-source selection. Use only one main 5 V source at a time through J1 or J2.

## J2: CAN and power

Amphenol 54602-908LF RJ45 connector.

| Pin | Signal |
|---|---|
| 1 | CANH |
| 2 | CANL |
| 3, 5 | +5V_IN, main power through F1 |
| 4, 6 | GND |
| 7 | 5V_CAN, external CAN-side 5 V supply |
| 8 | GND_CAN |

This connector carries CAN and power, not Ethernet or PoE. Verify the cable by pin number before connection. There is no isolated DC/DC converter on the board. Supply 5V_CAN externally and keep GND_CAN separate from GND to preserve the intended isolation. Do not bridge the two power domains through test equipment or the external supply.

Leave JP1 open by default. Close it to connect the 120 ohm R9 termination only when the board is at a bus endpoint that needs termination. Set the bus termination, bit rate and wiring as a system.

## J3 and J4: LED outputs

Molex 2157601003, Micro-Fit+ 3 mm, single-row three-pin right-angle connectors.

| Pin | J3 | J4 |
|---|---|---|
| 1 | 5V_LED_A | 5V_LED_B |
| 2 | GND | GND |
| 3 | DATA_A_OUT | DATA_B_OUT |

The mating LED board and harness pinout must match this table. R7 and R8 provide 330 ohm series resistance on the data outputs. Each output has its own resettable fuse and local supply decoupling.

## Power budget

- Main input: regulated 5 V.
- F1: Bourns MF-MSMF110/16X-2, 1.1 A nominal hold current.
- F2 and F3: MF-MSMF050/16X-2, 0.5 A nominal hold current each.
- Design target: no more than 0.4 A per LED port and approximately 1 A total main 5 V load, including logic. These are design assumptions, not measured ratings. Derate for temperature, fuse voltage drop and the LED's minimum supply voltage. Resettable fuses are not precision current limiters.
- 3.3 V budget: 150 mA. At 5 V input, the LDO dissipates approximately 0.255 W at that load. Verify temperature rise on a prototype.

Higher-current LED loads require a separate power and protection design.

## PCB fabrication

| Parameter | Value |
|---|---|
| Outline, measured at line centers | 100 x 80 mm |
| Layers / thickness | 2 / 1.6 mm |
| Material / outer copper | FR-4 / 35 um, approximately 1 oz |
| Minimum track width / clearance rule | 0.25 mm / 0.20 mm |
| Standard via pad / drill | 0.70 mm / 0.35 mm |
| Mounting holes | Four 4.3 mm NPTH, centers 5 mm from adjacent board edges |
| Plated / non-plated holes | 52 / 8 |
| Component side | Top |

The outline spans X=50 to 150 mm and Y=50 to 130 mm in KiCad. The Gerber job bounding box includes the 0.05 mm outline stroke and may read 100.05 x 80.05 mm. Finished dimensions follow the outline centerline.

Drill files use metric absolute coordinates with separate PTH and NPTH outputs. PTH includes 22 vias and 30 component holes. NPTH includes four mounting holes, two RJ45 locating holes and two Micro-Fit+ locating holes. Bottom paste and silkscreen are empty because there are no bottom components or markings.

The manufacturing directory contains nine Gerbers, one job file and two drill files. Select the surface finish with the team and fabricator. The component-position CSV uses KiCad absolute coordinates, downward-positive Y and KiCad rotations. Convert and verify it against the assembly vendor's origin and rotation convention before ordering assembly.

## Component selection and assembly

The BOM lists 48 references, including four mounting holes that are not purchased components. Blank MPN fields identify parts still needing a purchasing selection. Check the package dimensions and pad pattern as well as the electrical rating.

| References | Selection requirements |
|---|---|
| C1-C6, C10, C11, C13, C14 | 100 nF, 0603, X7R, at least 10 V |
| C7-C9 | 4.7 uF, 0805, X5R or X7R, at least 10 V |
| C12 | 1 uF, 0805, X5R or X7R, at least 10 V |
| C15, C16 | 15 pF, 0603, C0G/NP0 |
| C17-C19 | 10 uF, 0805, X5R or X7R, at least 10 V |
| R1-R3 | 10 kohm, 0603, 1% |
| R4 | 1 kohm, 0603, 1% |
| R5, R6 | 100 kohm, 0603, 1% |
| R7, R8 | 330 ohm, 0603, 1% |
| R9 | 120 ohm, 0805, 1% |
| SW1 | Matching 6 x 6 mm four-pin through-hole tactile switch |
| J1, JP1 | Matching 2.54 mm headers; check body and pin dimensions |

Check effective ceramic capacitance under DC bias. Verify diode polarity, IC pin 1, connector orientation and the switch's internal contact arrangement before assembly.

## Firmware signals

| Function | MCU pin |
|---|---|
| LED_A / LED_B | PA6 / PB1 |
| CAN_RX / CAN_TX | PA11 / PA12 |
| CAN status LED | PB15 |
| SWDIO / SWCLK / SWO | PA13 / PA14 / PB3 |
| External 8 MHz crystal | PD0 / PD1 |

Firmware is not included. Configure these signals for the intended functions and validate the CAN timing and WS2812 waveform against the actual system components.

## Initial prototype test procedure

All steps below are pending hardware assembly. Record measured values and results when testing.

1. Inspect solder joints, IC orientation and connector pin numbering. With power disconnected, check for unintended shorts between each supply and its ground, and confirm separation of GND and GND_CAN.
2. Disconnect the LED loads and debugger power outputs. Apply a single regulated 5 V main supply with a conservative current limit appropriate to the unloaded logic board. Check current draw and the 5 V and 3.3 V rails before increasing the limit or adding loads.
3. Connect the debugger using the J1 signal table and target voltage reference. Verify MCU identification, programming and reset. Test the external crystal and record startup behavior.
4. Check the external CAN-side 5 V source and ground arrangement. Connect a known working CAN node using the system's bit rate and endpoint termination. Verify transmit, receive and status indication.
5. Verify each LED harness against J3/J4. Test one small LED load at a time, then both channels. Measure output supply voltage and data timing at the load.
6. Increase load only within the stated design targets. Record total current, fuse voltage drop, minimum LED voltage and component temperatures under representative ambient conditions.

## Validation scope

ERC reports zero errors and warnings. DRC reports zero violations, unconnected pads and footprint errors, with schematic parity enabled. Reference, value, MPN and footprint fields were checked between schematic and PCB for all 48 references.

The reports list disabled checks, including SPICE models, single-use global labels, four-way junctions, footprint filters and selected courtyard, pad-type and tuning checks. Passing the configured rules does not establish physical or system performance. The board has not been fabricated, assembled or tested. This is a low-voltage course design without automotive qualification or high-voltage safety-isolation certification.

## Design references

- [ST STM32F103C8 datasheet](https://www.st.com/resource/en/datasheet/stm32f103c8.pdf)
- [TI ISO1050 datasheet](https://www.ti.com/lit/ds/symlink/iso1050.pdf)
- [TI TLV755P datasheet](https://www.ti.com/lit/ds/symlink/tlv755p.pdf)
- [TI SN74AHCT1G125 datasheet](https://www.ti.com/lit/ds/symlink/sn74ahct1g125.pdf)
- [Abracon ABM3B datasheet](https://abracon.com/Resonators/abm3b.pdf)
- [Bourns MF-MSMF datasheet](https://www.bourns.com/docs/product-datasheets/mf-msmf.pdf)
- [Amphenol 54602-908LF](https://www.amphenol-cs.com/product/54602908lf.html)
- [Molex 2157601003](https://www.molex.com/en-us/products/part-detail/2157601003)
- [Molex family mechanical drawing, including 2157601003](https://www.ic-components.com/files/23/2157601004.pdf)
