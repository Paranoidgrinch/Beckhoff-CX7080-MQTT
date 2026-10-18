# Beckhoff-CX7080-MQTT

TwinCAT 3 programs in IEC 61131-3 Structured Text for a Beckhoff CX7080. The PLC drives the power supplies of
the RFQ ion cooler at the laser-assisted AMS beamline at CologneAMS and publishes setpoints and readbacks over
MQTT, so that [FLAVIA](https://github.com/Paranoidgrinch/FLAVIA-alpha-1.1-Fully-integrated-Laser-Anion-Variables-for-AMS-)
can control them from the PC.

## Channels

| Channel | Device | Range | MQTT topics |
| --- | --- | --- | --- |
| guide field 1 (PSU 1) | Delta ES 030-5 | 0 to 30 V | `psu/1/cmd/set_v`, `psu/1/set_v`, `psu/1/meas_v` |
| guide field 2 (PSU 2) | Delta ES 075-2 | 0 to 75 V | `psu/2/cmd/set_v`, `psu/2/set_v`, `psu/2/meas_v` |
| HV 1 | FuG HCN 7E-6500 | 0 to 6500 V | `hv/1/cmd/set_v`, `hv/1/set_v`, `hv/1/meas_v`, `hv/1/meas_i_m` |
| HV 4 | FuG HCN 7E-6500 | 0 to 6500 V | `hv/4/cmd/set_v`, `hv/4/set_v`, `hv/4/meas_v`, `hv/4/meas_i_m` |
| pressure control | gas inlet of the ion cooler | 0 to 10 V | `pressure/cmd/set_v`, `pressure/set_v`, `pressure/meas_v` |

Analog outputs use an EL4104 and analog inputs an EL3164 terminal (0 to 10 V corresponds to 0 to 32767
counts). A client writes to `.../cmd/set_v`; the PLC publishes the accepted setpoint on `.../set_v` and the
measured values on `.../meas_*`. The `.../dbg/ao*_counts` topics show the raw output counts.

## Files

| File | Content |
| --- | --- |
| `MAIN.TcPOU`, `PRG_IoT.TcPOU` | current version: global variables and two POUs, I/O scaling in `MAIN` and MQTT in `PRG_IoT`, for a shorter cycle time |
| `PlcTask.TcTTO`, `Task_IoT.TcTTO` | task configuration for the two POUs |
| `TwinCAT Projekt2scratch.sln`, `*.tsproj`, `Untitled1.plcproj`, `Untitled1.tmc` | TwinCAT project |
| `MQTT *.txt` | earlier single-POU versions, in order of added features: blank template, test counter, guide field 1, guide fields 1 and 2, HV 1 and 4, pressure control |
| `MQTT ion cooler explanations.txt` | annotated program explaining every variable and conversion |
| `meas.txt`, `trace_Lens_2_voltage_[V].txt` | measurement traces recorded with the setup |

## Usage

Open `TwinCAT Projekt2scratch.sln` in TwinCAT 3 XAE, link the `AT %Q*` and `AT %I*` variables to the EL4104
and EL3164 channels of your I/O configuration, set the broker in `PRG_IoT` (`fbMqttClient.sHostName`,
default `192.168.0.20`, port 1883) and activate the configuration on the CX7080. The controller needs the
TwinCAT IoT MQTT function.

## License

GPL-3.0, see [LICENSE](LICENSE).
