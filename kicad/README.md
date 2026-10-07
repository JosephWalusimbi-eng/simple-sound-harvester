# Piezo Energy Harvester -- Simplified 5-Part Passive Design

A KiCad project implementing the simplified schematic described in
[`../about.txt`](../about.txt):

```
Piezo Header -> Bridge Rectifier -> Storage Cap -> Zener Diode Clamp -> Output Terminal
```

## Files

- `piezo_harvester.kicad_pro` - KiCad project file
- `piezo_harvester.kicad_sch` - Schematic (flat, single sheet)
- `piezo_harvester.kicad_pcb` - 2-layer, through-hole PCB layout
- `BOM.csv` - Bill of materials

## Circuit

| Ref | Part | Role |
|-----|------|------|
| J1 | 2-pin screw terminal | Piezo element input |
| U1 | Bridge rectifier (or 4x 1N5817 diodes) | Full-wave rectification of the piezo AC output |
| C1 | 100 uF electrolytic capacitor | Smoothing / energy storage |
| D1 | 5.1 V Zener diode | Clamps `VOUT_PLUS` to a safe voltage |
| J2 | 2-pin screw terminal | Output to a supercapacitor/battery and/or load |

Nets: `PIEZO_A`, `PIEZO_B` (bridge inputs), `VOUT_PLUS` and `GND` (rectified/clamped
output rail, shared by C1, D1 and J2).

All parts are through-hole, and the PCB uses only basic rectangular/circular
pads and hand-routable orthogonal traces on `F.Cu`, per the "5 or 6 component,
hand-routable 2-layer board" goal in `about.txt`.

## How this was built / how to verify it

KiCad is not installed in the environment this was built in, so these files
were generated programmatically (coordinates computed in Python, not typed by
hand) and checked two ways instead of visually:

1. Parsed back with the `kiutils` library to confirm the S-expression syntax
   is well-formed.
2. A connectivity check (`check_connectivity` logic) confirms every pad that
   should share a net in the circuit (e.g. U1 pin 3, C1 pin 1, D1 pin 2, J2
   pin 1 all on `VOUT_PLUS`) actually does, and nothing unintended is merged.

**Before relying on this for a build, open it in KiCad (8.x recommended) and:**
- Run Schematic ERC and PCB DRC (Inspect menu).
- Visually check silkscreen/footprint placement and courtyards don't overlap.
- Confirm footprint choices match the exact parts you buy (pad sizes/pitches
  here are reasonable defaults, not pulled from a manufacturer datasheet).

## Design notes / what was simplified

See `../about.txt` for the full rationale. In short: one piezo input instead
of three, a single packaged bridge rectifier (or 4 diodes) instead of 12
SMD diodes, a passive cap+Zener clamp instead of the BQ25570 MPPT charger,
and a bare screw-terminal output instead of a USB boost stage -- dropping the
part count from ~40 to 5.
