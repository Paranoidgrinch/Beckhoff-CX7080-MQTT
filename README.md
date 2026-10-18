# Beckhoff CX7080 + MQTT

TwinCAT 3 PLC programs (IEC 61131-3 Structured Text) for a **Beckhoff CX7080** that drives the power supplies
of the RFQ ion cooler in the laser-assisted AMS beamline at CologneAMS and publishes them over **MQTT**. The
PLC converts between engineering units and the analog I/O and makes every setpoint and readback available
as an MQTT topic, so [FLAVIA](https://github.com/Paranoidgrinch/FLAVIA-alpha-1.1-Fully-integrated-Laser-Anion-Variables-for-AMS-)
can control the hardware from the PC.

[![License](https://img.shields.io/badge/license-GPL--3.0-394B59?style=flat-square)](LICENSE)

## Controlled hardware

| Channel | Device | Range | MQTT topics |
| --- | --- | --- | --- |
| Guide field 1 (PSU 1) | Delta ES 030-5 | 0–30 V | `psu/1/cmd/set_v`, `psu/1/set_v`, `psu/1/meas_v` |
| Guide field 2 (PSU 2) | Delta ES 075-2 | 0–75 V | `psu/2/cmd/set_v`, `psu/2/set_v`, `psu/2/meas_v` |
| HV 1 | FuG HCN 7E-6500 | 0–6500 V | `hv/1/cmd/set_v`, `hv/1/set_v`, `hv/1/meas_v`, `hv/1/meas_i_m` |
| HV 4 | FuG HCN 7E-6500 | 0–6500 V | `hv/4/cmd/set_v`, `hv/4/set_v`, `hv/4/meas_v`, `hv/4/meas_i_m` |
| Pressure control | ion-cooler gas inlet | 0–10 V | `pressure/cmd/set_v`, `pressure/set_v`, `pressure/meas_v` |

Analog outputs via EL4104 and analog inputs via EL3164 (0–10 V ↔ 0–32767 counts). A client writes to
`…/cmd/set_v`; the PLC confirms the accepted setpoint on `…/set_v` and publishes the measured values.
Debug topics (`…/dbg/ao*_counts`) show the raw output counts.

## Files

| File | Content |
| --- | --- |
| `MAIN.TcPOU`, `PRG_IoT.TcPOU` | **Final version:** global variables and two POUs — I/O scaling in `MAIN`, MQTT in `PRG_IoT` — for better cycle performance |
| `PlcTask.TcTTO`, `Task_IoT.TcTTO` | task configuration for the two POUs |
| `TwinCAT Projekt2scratch.sln`, `*.tsproj`, `Untitled1.plcproj`, `.tmc` | the TwinCAT project |
| `MQTT … .txt` | earlier single-POU versions, each adding features: blank template → test counter → guide field 1 → guide fields 1+2 → + HV 1+2 → + pressure control |
| `MQTT ion cooler explanations.txt` | the annotated program with explanations of every variable and conversion |
| `meas.txt`, `trace_Lens_2_voltage_[V].txt` | measurement traces recorded with the setup |

## Usage

Open `TwinCAT Projekt2scratch.sln` in TwinCAT 3 XAE, map the `AT %Q*` / `AT %I*` variables to the EL4104 /
EL3164 terminals of your I/O configuration, set the MQTT broker address in `PRG_IoT` (`fbMqttClient.sHostName`, default `192.168.0.20:1883`), then activate the
configuration on the CX7080. The TwinCAT IoT (MQTT) function is required on the controller.

## License

GPL-3.0 — see [LICENSE](LICENSE).
