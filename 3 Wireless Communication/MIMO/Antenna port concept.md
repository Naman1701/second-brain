# Antenna port concept

Antenna ports are logical transmit entities used for reference signals and spatial processing in MIMO systems.

## Why it matters
The concept helps separate:
- physical antenna elements
- logical antenna ports
- spatial layers and beamforming behavior



antenna port: 

In Mimo  

|                                     |                                      |                                         |                                                                                                                                  |
| ----------------------------------- | ------------------------------------ | --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| Channel / signal                    | Antenna-port range / <br><br>maximum | Max ports                               | Important comment                                                                                                                |
| PDSCH DM-RS                         | 1000–1023                            | 24                                      | Rel-18/19 enhanced DM-RS. Basic configurations <br><br>use fewer: 4/6/8/12 etc. (ETSI)                                           |
| PDSCH data                          | 1000–1023                            | 24 defined DM-RS <br><br>ports          | But max PDSCH transmission layers = 8; don't <br><br>equate 24 ports with 24 layers. (ETSI)                                      |
| PDCCH DM-RS                         | 2000                                 | 1                                       | PDCCH symbols are mapped on port 2000 in <br><br>current 38.211. (ETSI)                                                          |
| PBCH / SS-PBCH <br><br>block        | 4000                                 | 1                                       | SSB/PBCH is a single-port transmission.                                                                                          |
| CSI-RS, one <br><br>resource        | 3000–3031                            | 32                                      | A single CSI-RS resource can have 1, 2, 4, 8, 12, 16, <br><br>24 or 32 ports. (ETSI)                                             |
| CSI-RS, aggregated <br><br>resource | —                                    | up to 128 total                         | Rel-19 permits aggregation of multiple CSI-RS <br><br>resources, giving total CSI-RS ports of 48, 64 or <br><br>128. (ETSI)      |
| PRS                                 | 5000                                 | 1                                       | Downlink PRS is mapped to antenna port 5000. <br><br>(ETSI)                                                                      |
| PSS                                 | —                                    | not separately port-<br><br>multiplexed | Part of SS/PBCH structure.                                                                                                       |
| SSS                                 | —                                    | not separately port-<br><br>multiplexed | Part of SS/PBCH structure.                                                                                                       |
| PT-RS for PDSCH                     | associated with PDCCH transmisson    | —                                       | PT-RS is not a separate independent port family in <br><br>the same sense as CSI-RS; it follows the PDSCH transmission structure |

 

![[mimo_antenna_port_number.png]]

## Related notes
- [[MIMO]]
- [[Beamforming]]
- [[3 Wireless Communication/CSI/CSI]]
