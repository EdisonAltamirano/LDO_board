# EE372 LDO board — regenerated manufacturing package

Generated from the saved KiCad files on 2026-10-05 after refilling zones and
running fresh KiCad 10 checks.

## Digi-Key cart — important purchasing link

**Shared cart:** https://www.digikey.com/short/w9fvn9rt

The shared page blocks automated access, so the package also contains the cart
CSV supplied with the project:

- `bom/DigiKey_cart_stock_snapshot_2026-10-02.csv` — unchanged source cart.
- `bom/DigiKey_cart_stock_assessment.csv` — normalized stock assessment with
  the shared-cart URL on every row.

The saved cart contains 44 lines. All 44 show `Available >= Quantity` and zero
backorder as of the CSV's 2026-10-02 snapshot. This is historical evidence,
not a guarantee of live stock on order day; open the shared cart and reconfirm
availability immediately before purchasing.

## Rail inventory and measurement coverage

See `Rail_inventory_and_measurement_map.md`. It contains:

- every power/input/output rail and its role;
- direct access through banana jack, SMA, jumper, or pin header;
- a short mapping from output accuracy, line/load regulation, dropout,
  quiescent current, PSRR, noise, load transient, reference monitoring, I2C,
  reset/clock, and temperature testing to the enabling net and connector.

Important examples:

| Measurement | Net(s) | Access feature |
|---|---|---|
| DC output accuracy | `/VOUT_CUSTOM`, `/GND` | J13.1 and J18.4 |
| Line regulation / dropout | `/VIN_DUT`, `/VOUT_CUSTOM` | J3.1 and J13.1; source at VIN1 |
| Input or quiescent current | `/VIN_RAW`, `/VIN_SENSE` | Ammeter across JP1.1–JP1.2 |
| PSRR | `/VIN_RAW` → `/VOUT_CUSTOM` | S1 input and S2 output SMA |
| Output noise / transient | `/VOUT_CUSTOM` | S2 SMA and its ground shell |
| VREF / IREF | `/VREF_MON`, `/IREF_MON` | J18.5 and J18.6 |
| I²C | `/SCL_DUT`, `/SDA_DUT` | J5.1 and J7.1; controller side on J19 |

## Fabrication and assembly files

- `gerbers/` — four copper layers, solder mask, paste, silkscreen, edge cuts,
  PTH/NPTH drill files, maps, and the Gerber job file.
- `ldo_eval_gerbers_drill.zip` — clean board-fabrication upload bundle.
- `CPL_smd.csv` — fitted SMD centroid file in millimetres.
- `CPL_all.csv` — complete fitted placement export.
- `bom/BOM_schematic.csv` — reference/value/footprint/DNP schematic BOM.
- `reports/DRC_zero.rpt` — 0 violations, 0 unconnected pads, 0 footprint
  errors, and 0 schematic-parity issues.
- `reports/ERC_zero.rpt` — 0 errors and 0 warnings.
- `board_top.png`, `board_bottom.png` — current 3D-review renders.
- `SHA256SUMS.txt` — integrity hashes.

## Changes needed to obtain zero DRC

- Removed the small `IC FIlling` bottom-copper polygon because it was entirely
  isolated from GND and generated six isolated-copper violations.
- Increased eighteen user silkscreen labels from 0.5 mm to the configured
  minimum height of 0.8 mm.
- Repositioned several labels and abbreviated six crowded labels (`EXT`,
  `3V3`, `BGR`, `I2C`, `IO`, `VR`) to clear pads, footprints, and the edge.

## Release warning

Zero ERC/DRC means the current files satisfy the enabled KiCad rules. It does
not prove electrical correctness or assembly readiness. The prior fabrication
review identifies unresolved blockers, including the custom DUT exposed-pad
connection and a BOM that lacks manufacturer/supplier fields tied to every
reference. The schematic BOM has 91 grouped rows while the Digi-Key cart has
44 purchasing rows and no Customer Reference values, so they cannot be joined
reliably for automated assembly.

Do not submit the assembly order until every fitted reference has an approved
MPN/Digi-Key number and the exposed-pad/package decisions are resolved. Upload
the BOM and CPL to the assembler and visually inspect every asymmetric part's
rotation in the placement preview.
