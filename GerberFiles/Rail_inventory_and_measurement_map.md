# Rail inventory and spec → board-feature map
Verified 2026-10-05 against `ldo_eval.kicad_pcb` pad nets and
`test_access_net_map.csv`. Supersedes the tables in the release README, which
omit `/BUFFER_VDD` and all four SMA coax ports.

## Connector legend

| Ref | Type |
|---|---|
| `GND1 VIN1 DVIN1 BUFFER1` | CalTest CT3151 banana jack |
| `S1`–`S4` | Amphenol 132134 vertical SMA |
| `J1`–`J17` | 1×1 pin header (0.1″ post) |
| `J18` | 1×6 header — analog monitor |
| `J19` | 1×8 header — 3V3 debug drive |
| `JP1`–`JP5` | 1×2 jumper |

## Rail inventory

| Rail / net | Role | Direct access | Notes |
|---|---|---|---|
| `/GND` | Common reference | **GND1** (banana); J1.1, J18.4, J19.8; shell of S1–S4 | Use the SMA shell for anything above ~100 kHz |
| `/VIN_RAW` | Raw analog input | **VIN1** (banana); JP1.1; **S1.1** | S1 is the ripple/transient injection port |
| `/VIN_SENSE` | Input sense | JP1.2 | Far side of the JP1 current break |
| `/VIN_DUT` | DUT input rail | J3.1 | Line / dropout measurements |
| `/VIN_CUSTOM` | Custom LDO analog input | J2.1 | |
| `/DVIN_RAW` | Raw digital input | **DVIN1** (banana); JP2.1 | |
| `Net-(JP2-B)` | Digital-input link, far side | JP2.2 → R19.1 | **Unnamed.** No Kelvin sense point, unlike JP1 |
| `/DVIN_CUSTOM` | DUT digital supply | J8.1 | |
| `/BUFFER_VDD` | Buffer supply | **BUFFER1** (banana) | *Missing from the release README table* |
| `/CTRL_3V3` | Controller logic 3V3 | J19.6; JP5.1 | |
| `/ANALOG_3V3` | Analog monitor supply | J18.1 | Measurement electronics |
| `/I2C_1V8` | DUT-side I²C domain | J18.2 | Pull-up rail |
| `/TEMP_VDD` | Temp-sensor supply | JP5.2 | Gated from `/CTRL_3V3` via JP5 |
| `/VOUT_CUSTOM` | **Primary DUT output** | J13.1; **S2.1** | S2 for noise/transient |
| `/VOUT_REFERENCE` | Reference regulator output | J10.1 | Comparison leg |
| `/VOUT_COMP_ENABLED` | Compensated output branch | J14.1 | |
| `/VOUT_BUFFERED` | Buffered output | J15.1; **S3.1** | High-Z path |
| `/BUFFER_IN` | Buffer input | J16.1; **S4.1** | |
| `/BUFFER_OUT` | Buffer output | J17.1 | |
| `/BUFFER_BW`, `/BUFFER_BW_BIAS` | Buffer bandwidth select | JP3.1/.2, JP4.1/.2 | |
| `/VREF_EXT` | External reference input | J12.1 | Input only — respect approved limits |
| `/VREF_MON`, `/IREF_MON` | Reference monitors | J18.5, J18.6 | |
| `/IOUT_ADC` | Output-current sense to ADC | J18.3 | |
| `/SCL_DUT`, `/SDA_DUT` | DUT-side I²C | J5.1, J7.1 | 1.8 V domain |
| `/CLK_DUT`, `/RST_DUT` | DUT clock / reset | J4.1, J6.1 | |
| `/SEL_BGR`, `/SEL_EXT_REF` | Reference selects | J9.1, J11.1 | |
| `/SCL_3V3 /SDA_3V3 /CLK_3V3 /RST_3V3 /SEL_BGR_3V3 /SEL_EXT_REF_3V3` | 3V3-side debug drive | J19.1, .2, .5, .4, .3, .7 | Level-shifted controller side |

## Spec → board feature map

| Specification | Net(s) | Feature that enables it | Return |
|---|---|---|---|
| Programmed DC output accuracy | `/VOUT_CUSTOM` | J13.1 (DMM post) | J18.4 |
| Line regulation | `/VIN_DUT`, `/VOUT_CUSTOM` | J3.1 + J13.1; sweep source at VIN1 | J18.4 |
| Load regulation | `/VOUT_CUSTOM`, `/IOUT_ADC` | J13.1 + J18.3; load via output path | J18.4 |
| Dropout voltage | `/VIN_DUT`, `/VOUT_CUSTOM` | J3.1 + J13.1, sweep VIN1 down | J18.4 |
| Quiescent / input current | `/VIN_RAW`, `/VIN_SENSE` | **JP1 break** — ammeter across JP1.1–JP1.2 | J1.1 |
| Digital-rail current | `/DVIN_RAW`, `Net-(JP2-B)` | **JP2 break** — ammeter across JP2.1–JP2.2 | J1.1 |
| **PSRR** | inject `/VIN_RAW`, measure `/VOUT_CUSTOM` | **S1 → S2, coax both ends** | SMA shell |
| **Output noise** | `/VOUT_CUSTOM` | **S2** coax (preferred); J13.1 only if short-return | SMA shell |
| **Load-step transient** | `/VOUT_CUSTOM` | **S2** coax; step driven from J19 debug side | SMA shell |
| Buffered high-Z output | `/VOUT_BUFFERED` | **S3** coax, or J15.1 | SMA shell / J18.4 |
| Buffer characterisation | `/BUFFER_IN`, `/BUFFER_OUT` | **S4** in, J17.1 out; BW select on JP3/JP4 | SMA shell |
| Buffer supply | `/BUFFER_VDD` | **BUFFER1** banana | GND1 |
| Reference vs custom comparison | `/VOUT_REFERENCE`, `/VOUT_CUSTOM` | J10.1 + J13.1 | J18.4 |
| Compensation response | `/VOUT_COMP_ENABLED` | J14.1 | J18.4 |
| VREF / IREF observation | `/VREF_MON`, `/IREF_MON` | J18.5, J18.6 | J18.4 |
| External reference injection | `/VREF_EXT` | J12.1 in, select via J11.1 | J18.4 |
| I²C program / readback | `/SCL_DUT`, `/SDA_DUT` | J5.1, J7.1; 3V3 side J19.1–.2 | J19.8 |
| Reset / clock / select timing | `/RST_DUT /CLK_DUT /SEL_BGR /SEL_EXT_REF` | J6.1, J4.1, J9.1, J11.1; 3V3 side on J19 | J19.8 |
| Temperature monitor | `/TEMP_VDD` + sensor path | JP5.2 enable | J18.4 |

### Caveats carried from the fab review

- **PSRR, output noise and load-step transient are the three specs most exposed
  to finding F1** — there is no inner ground plane, so the return path for all
  three runs on outer-layer pour only. Expect degraded, layout-dominated
  numbers rather than true device performance.
- **Finding F2** — the DUT exposed pad is floating, which can shift noise and
  PSRR independently of the layout issue above.
- The temperature-sensor signal endpoint is still unconfirmed; only the supply
  gate (JP5.2) is verified.
