# Troubleshooting a stuck TX Ground in FT-991A
## Introduction and problem statement
I had my FT-991A turned on and providing feed to pskreporter on 30m band when lightning struck nearby. From that moment onwards, its `TX GROUND` (that I use to drive a linear amp) pin was active all the time, even during RX - as if the radio were permanently transmitting. The transceiver sensitivity was unaffected: FT8 software gives around a minute of coverage of signals and the subsequent signal strength was similar to before the event.

Called a local repairman: I found that he was booked out for the rest of the year. I decided to try to find out what was going on.

## Schematic
This is a summarized and annotated version of the schematic, highlighting the parts relevant to this defect.

<img width="647" height="766" alt="schema-manutencao" src="https://github.com/user-attachments/assets/2dff95ef-52c1-46ab-87e6-6762f2f199f3" />

Legend:
| Color | Signal |
| --- | ---- |
| Green | RF Input | 
| Orange | TX9 |
| Blue | 9V bus |
| Light Blue | Logic TX signal |
| Violet | TX Ground |

## Troubleshooting
After checking the schematic and the nature of the defect, my first suspect fell on a damaged `Q1145` (the transistor that generates TX Ground): Maybe it was shorted from emitter/collector. After removing it from the radio and testing it _ex vivo_, I found it to be fine.

After further investigation, I found a residual 1.05V on TX9 line (more than enough to bias `Q1145`) and then the suspect fell on a leaky `Q1061` - in charge of feeding the `TX9` line. After probing and testing it off the radio, I found it to be nominal too.

Even with `Q1061` off the radio, the 1.05V was still present on the TX9 bus - that clearly highlighted that something else was leaking it.

After checking the schematic TX9 line, I found `D1113` as a strong candidate, as this is very close to the RF front-end, which could have been damaged by the strike. After probing it, I found it leaky both ways. After removing this diode, TX9 dropped to 0V in RX mode. That was the culprit.

## Fix
Since `D1113` was damaged, the other nearby diodes could be as well, as they all feed from the same line. I replaced all the three parts: `D1113`, `D1089` and `D1090`. These are [Rohm RN142S RF PIN diodes](https://www.digikey.com/en/products/detail/rohm-semiconductor/RN142SMT2R/11685079). Bought a tape with 25 of them and added to my inventory.

Since we had a damaged (yet not destroyed) diode, chances are that `D1002` [Pico Denshi 1608SGX ESD supressor](https://picodenshi.com/product/1608sgx/) is also damaged. I replaced it with an [Eaton 0603ESDA 35V ESD supressor](https://www.digikey.com/en/products/detail/eaton-electronics-division/0603ESDA2-TR2/3681416). Scratched the varnish at the ground plane and added a second supressor downstream.

<img width="600" height="800" alt="board-fix" src="https://github.com/user-attachments/assets/e5827ba3-de8d-41b7-bf99-a61bd1290bb5" />

Legend:

| Color | Component |
| --- | ---- |
| Red | `D1089 D1090 D1113` |
| Blue | `D1002` |
| Yellow | Extra TVS (mounted laterally) |

## Protection
It could have been worse, for sure - but the chained protection - [learn more in this article](https://github.com/rfrht/FT-991A/wiki/FT-991-A-deaf-in-IPO-mode,-receives-normally-in-AMP-modes-in-HF-bands) transformed this event from destruction to failure.

As per the aforementioned article, the FT-991A has strong protections - BUT, the GDTs (gas discharge tube) at the PA boards are at the flip side of the PCB - in order to access that, you must desolder your finals - and this isn't a great idea.

In order to overcome that limitation, I have soldered a GDT directly at the connector where it meets the board: a 300V variant at the HF connector and 90V at the VHF/UHF connector.

After replacing the 300V GDT, I cracked open the one that was at the radio, soldered at the connector terminal.

<img width="800" height="442" alt="gdt-damaged" src="https://github.com/user-attachments/assets/3bcab97a-f3ef-418c-8fcc-438e5fdc7f82" />

While there are no signals of massive destruction, it's undeniable that it was pressed into operation a number of times; you can see some charring on the internal wall of the GDT.

Hope that helps. Stay safe, 73 de PY2RAF.
