# Understanding FT-991A TX Inhibit (TX INH) function
The TX Inhibit is a **very useful** function - that pretty much inhibits any RF output from your radio when it is active. The **TX INHIBIT** feature on the Yaesu FT-991A, accessible via the rear-panel 8-pin Mini-DIN `TUNER` jack, provides a fast hardware-level interlock to instantly block RF transmission. This is useful for sequencing tuners, amplifiers, relays etc. While the radio might engage the transmission mode when you hit PTT or via CAT, if TX INH is high, there will be no RF power output at all.

While developing my [SignalSurge](https://github.com/rfrht/SignalSurge) board, I felt the need to make use of the TX Inhibit to avoid the radio transmitting when the board was in amplifier/filter mode: if any RF power entered the board through the filter/amplifier mode, it would immediately fry my board's stage. So the TX Inhibit acts as a safety net by avoiding TX power while everything not is duly in place.

# Activating TX Inhibit (TX INH)
The FT-991A's TX INH line operates on an **Active-HIGH** logic structure and remains floating by default. To engage the TX Inhibit mode, an external peripheral - such as an antenna tuner, power amplifier protection circuit, or station sequencer - must apply a positive DC voltage between **+1.5 V DC and +13.8 V DC** to the TX INH pin - in the FT-991A, it is the **Pin 8** at the **TUN/LIN** port. When the pin is left floating or grounded (0 V DC), transmission remains fully enabled.

# How does that work
The transmission of the FT-991A is controlled by a BF2040W dual-gate MOSFET. Its gain is controlled by the gate 2 (G2, violet down arrow) via the TXG line (red horizontal arrow). When pushing voltage (1.5 - 13.8V) at the Pin 8 of TUN/LIN port (vertical red arrow), it feeds the base of a digital NPN transistor (Q1014, DTC143ZE). Once the transistor gets in operation, it shunts Gate 2 of the BF204W dual-gate MOSFET RF pre-driver directly to ground (the dark red star). This instantly eliminates the gain of the BF2040W and suppresses any RF signal outputted (G1, blue arrow), with no signal ever reaching the driver or power amplifier stages.

![FT-991A TX Inhibit chain](https://github.com/user-attachments/assets/fdab4e1f-a59c-4be5-ba87-99f2304802df)

# Safety concerns
While the integrated 4.7k Ohm internal base resistor allows the pin to directly interface with both standard +3.3 V / +5 V logic and raw +13.8 V equipment lines, you might want to insert a 2.2k Ohm series resistor or an optocoupler when connecting to relay-driven outputs to protect against back-EMF spikes.
