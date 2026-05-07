# Diode tester
A simple tester to measure, under different test currents, the voltage drop across a diode when forward biased, or the Zener breakdown voltage.<br/>
The circuit may be powered from a 12VDC or 24VDC DC adapter. The maximum measurable Zener voltage is determined by the supply voltage..<br/>
The circuit allows testing of different diode types, each requiring different operating currents. For this reason, the test current can be selected through calibrated current ranges, from a minimum of about $1mA$ up to $30mA$. This also allows observation of the variation of the forward voltage drop or Zener voltage as a function of the test current.

![overview](resources/diode-tester_overview.jpg)
![inside](resources/diode-tester_inside.jpg)


## Specifications

### Features
- measurement of forward voltage drop under different current values
- measurement of Zener breakdown voltage (up to $V_{CC}$ minus a few volts) under different current values
- supply voltage ($V_{CC}$) from DC-Adapter $12VDC$ or $24VDC$
- adjustable load current between $1.0mA$ and $30mA$
- display voltage and current across the diode under test
- protection against reverse polarity of the supply voltage


### Schematic
Schematic and PCB layout are designed with ExpressPCB free CAD software.

![board-schematic](resources/diode-tester_sch.jpg)


### Circuit
#### Load driver network:
The load current must be adjustable between $1mA$ and $30mA$:

The load current can be approximated as: $I_{C3} ≈ V_{BE2}/R_3$

Assuming $V_{BE2} = 0.7V  \implies$
- $R_{3_{1.0mA}} = 700$&Omega; $\implies R_{3a} = 1K$&Omega; trimmer
- $R_{3_{2.0mA}} = 350$&Omega; $\implies R_{3b} = 500$&Omega; trimmer
- $R_{3_{5.0mA}} = 140$&Omega; $\implies R_{3c} = 200$&Omega; trimmer
- $R_{3_{10mA}} = 70$&Omega; $\implies R_{3d} = 100$&Omega; trimmer
- $R_{3_{20mA}} = 35$&Omega; $\implies R_{3e} = 100$&Omega; trimmer
- $R_{3_{30mA}} = 23$&Omega; $\implies R_{3f} = 100$&Omega; trimmer

The current depends on transistor VBE and therefore exhibits moderate thermal drift.

$P_{R_{3_{MAX}}} = I_{C_{3_{MAX}}}^2 * R_{3_{MIN}} = 2mW$


$V_{CE_3} = V_{CC} - V_{load} - V_{BE_2}$

Assuming $V_{load_{MIN}} = 0.3V$ (for a forward biased schottky diode) and $V_{load_{MAX}} = 18V$ (for a reverse biased zener diode):

$V_{CE_{3_{12V}}} = (2.3 - 11.1)V$ and $V_{CE_{3_{24V}}} = (5.3 - 23.1)V \implies V_{CE} = (2.3 - 23.1)V$

$P_{Q_{3_{MAX}}} = V_{CE_{3_{MAX}}} * I_{C_{3_{MAX}}} = 0.7W$

A medium-power transistor is needed for $Q_3$ with a small heatsink:

&Theta; $_{jc} = 2.5$ &deg; $C/W$,

&Theta; $_{hs} = (5 - 10)$ &deg; $C/W$

Assuming $T_A = 30$&deg; $C$:

$T_{Q_{3_{MAX}}} = T_A + P_{Q_{3_{MAX}}}*($ &Theta; $_{tot}) \leq 40$&deg; $C$

At high supply voltages and test currents above 20mA, transistor dissipation may become significant. For reliable operation, prolonged measurements at the highest current settings should be avoided unless adequate heatsinking is provided.



#### Control & current reference network:
The role of the current reference network (a constant current generator) is to absorb the differences in the choice of the $V_{CC}$ value. In this way, the choice of Vcc determines only the maximum value of the Zener voltage that can be measured (tested) by the circuit. The control network (also a constant current generator) keeps the load current constant and ensures that $Q_3$ (and $Q_2$) always operates in the active region.

$V_{CE_2} = V_{BE_2} + V_{BE_3} = 1.4V$

$2*V_D = 1.2V = V_{BE_1} + R_2 * I_{R_2}$


$V_{CE_1} = V_{CC} - V_{R_2} - V_{CE_2} \implies V_{CE_1} = (9.1 - 21.1)V$

For $Q_3$ it is better to use a Darlington (*), which has a high $H_{FE}$, so as to keep its base current small and also the reference current of $Q_1$.<br/>
Assuming: $H_{FE_3} \geq 1000$

$I_{B_{3_{MAX}}} = I_{C_{3_{MAX}}}/H_{FE_3} = 30$&micro; $A$

So we can set: $I_{R_2} = 5mA => R_2 = (2*V_D - V_{BE_1})/I_{R_2} = 100$&Omega;

$P_{Q_{1_{MAX}}} = V_{CE_{1_{MAX}}} * I_{R_2} = 115mW$

$P_{Q_{2_{MAX}}} = V_{CE_2} * (I_{R_2} - I_{B_3}) = 7mW$

$V_{R_1} = V_{CC} - 2* V_D = (10.8 - 22.8)V \implies I_{R_1} = V_{R_1}/R_1 = (.9 - 1.9)mA \implies P_{R_{1_{MAX}}} = I_{R_{1_{MAX}}}^2*R_1 = 43mW$

(*) If $Q_3$ were not a Darlington, its $H_{FE}$ would be about $15$. In the worst case ($V_{CC}=24V$ and $I_{C_{3}}=30mA$), its base current should be in the $mA$ range, so $Q_1$'s collector current should be set to at least $(10-15)mA$. Since $V_{CE_1}$ must sink virtually all of the $V_{CC}$, the power to be dissipated would require a medium-power transistor for $Q_1$ as well.

#### Display network:
The display network translates the voltage drop across the diode into a ground-referenced signal for the digital voltmeter.<br/>
The core of this stage is the operational amplifier, configured as a unity-gain differential amplifier.
It converts the floating measurement into a single-ended output referred to GND, ensuring compatibility with standard 3-wire digital display modules.<br/>
The high input impedance of the op-amp minimizes loading effects on the test current loop.

*Note: When no diode is connected, the display will show the maximum available voltage (VCC​−Vdrop​). This is normal behavior for a constant current generator operating at no load.*


### LTspice Simulation
Below is the simulation of the circuit with the LTspice software.<br/>
The simulation plots currents, voltages and powers on the transistors of the circuit, assuming a forward biased diode as a load. The simulation was performed using two different values ​​of $V_{CC}$ $(12V, 30V)$, for each of which the resistance $R_3$ is linearly modified from the value $15$&Omega; to $500$&Omega;, and then brought back to the value $15$&Omega;.<br/>
The circuit was simulated up to 30V supply for stress analysis.
In Plot 2, the Y-axis unit is displayed as voltage by LTspice, but it actually represents the resistance sweep of $R_3$ $(15-500$&Omega;$)$.<br/>
For the simulation, the value $H_{FE}=1000$ was set for the transistor $Q_3$.
![plot](resources/ltspice-plot.jpg)
![schematic](resources/ltspice-schematic.jpg)
<br> 


### Calibration Procedure
A two-step calibration process is required to align the internal measurement electronics and to set the operational test currents. This ensures that the displayed values accurately reflect the load characteristics without interference from component tolerances.

#### Measurement circuit setting:
This step balances the op-amp differential stage.
1. Power the circuit without inserting any component into the TEST terminals.
2. Short-circuit the TEST terminals using a jumper.
3. Rotate the trimmer R4b until the Volt Meter reads exactly 0.00V.

#### Current ranges setting:
This step calibrates each position of the rotary switch (SW1) to deliver the exact programmed test current through the load.
1. Connect a multimeter set to DC Current (mA) mode across the TEST terminals.
2. For each switch position, adjust the corresponding trimmer (R3a through R3f) until the multimeter displays the target current (1mA, 2mA, 5mA, 10mA, 20mA, or 30mA).


### PCB Layout
![board-pcb](resources/diode-tester_pcb.jpg)


## About
Author : Alessandro Fraschetti (mail: [gos95@gommagomma.net](mailto:gos95@gommagomma.net))


## Licence
This project is under the [MIT license](LICENSE).
You are free to use this for any purpose, just try to give credit in the documentation of your project.
