# Diode tester
A simple tester to measure, under different current values, the voltage drop across a diode when forward biased, or the working voltage of a Zener diode when reverse biased.<br/>
The circuit may be powered with a DC-Adapter $12VDC$ or $24VDC$, obviously the maximum measurable zener voltage depends on the supply voltage.<br/>
The circuit allows you to test diodes of different power, which require different minimum activation currents. For this reason, the current flowing in the diode under test can be selected by a variable resistor, from a minimum of about $1.5mA$ up to $50mA$. This also allows you to observe the variations in voltage, voltage drop or Zener, for different current values.

![overview](resources/diode-tester_overview.jpg)
![inside](resources/diode-tester_inside.jpg)


## Specifications

### Features
- test of voltage drop across a diode under different current values
- test of the working voltage of a Zener diode (up to $V_{CC}$ minus a fews Volts) under different current values
- supply voltage ($V_{CC}$) from DC-Adapter $12VDC$ or $24VDC$
- adjustable load current between $1.5mA$ and $50mA$
- display voltage and current across the diode under test
- protection against reverse polarity of the supply voltage


### Schematic
Schematic and PCB layout are designed with ExpressPCB free CAD software.

![board-schematic](resources/diode-tester_sch.jpg)


### Circuit
#### Load driver network:
The load current must be adjustable between $1.5mA$ and $50mA$:

$I_{C3} = V_{BE2}/R_3 \implies R_{3_{1.5mA}} = 470$&Omega; and $R_{3_{50mA}} = 15$&Omega; $\implies$  
$R_{3a} = 15$&Omega;, $R_3 = 470$&Omega;

$P_{R_{3_{MAX}}} = I_{C_{3_{MAX}}}^2 * R_{3_{MAX}} = 38mW$


$V_{CE_3} = V_{CC} - V_{load} - V_{BE_2}$

Assuming $V_{load_{MIN}} = 0.3V$ (for a forward biased schottky diode) and $V_{load_{MAX}} = 18V$ (for a reverse biased zener diode):

$V_{CE_{3_{12V}}} = (2.3 - 11.1)V$ and $V_{CE_{3_{24V}}} = (5.3 - 23.1)V \implies V_{CE} = (2.3 - 23.1)V$

$P_{Q_{3_{MAX}}} = V_{CE_{3_{MAX}}} * I_{C_{3_{MAX}}} = 1.1W$

A medium-power transistor is needed for $Q_3$ with a small heatsink:

&Theta; $_{jc} = 2.5$ &deg; $C/W$,

&Theta; $_{hs} = 5$ &deg; $C/W$

Assuming $T_A = 30$&deg; $C$:

$T_{Q_{3_{MAX}}} = T_A + P_{Q_{3_{MAX}}}*($ &Theta; $_{tot}) = 40$&deg; $C$


#### Control & current reference network:
The role of the current reference network (a constant current generator) is to absorb the differences in the choice of the $V_{CC}$ value. In this way, the choice of Vcc determines only the maximum value of the Zener voltage that can be measured (tested) by the circuit. The control network (also a constant current generator) keeps the load current constant and ensures that $Q_3$ (and $Q_2$) always operates in the active zone.

$V_{CE_2} = V_{BE_2} + V_{BE_3} = 1.4V$

$2*V_D = 1.2V = V_{BE_1} + R_2 * I_{R_2}$


$V_{CE_1} = V_{CC} - V_{R_2} - V_{CE_2} \implies V_{CE_1} = (9.1 - 21.1)V$

For $Q_3$ it is better to use a Darlington (*), which has a high $H_{FE}$, so as to keep its base current small and also the reference current of $Q_1$.<br/>
Assuming: $H_{FE_3} \geq 1000$

$I_{B_{3_{MIN}}} = I_{C_{3_{MAX}}}/H_{FE_3} = 50$&micro; $A$

So we can set: $I_{R_2} = 5mA => R_2 = (2*V_D - V_{BE_1})/I_{R_2} = 100$&Omega;

$P_{Q_{1_{MAX}}} = V_{CE_{1_{MAX}}} * I_{R_2} = 115mW$

$P_{Q_{2_{MAX}}} = V_{CE_2} * (I_{R_2} - I_{B_3}) = 7mW$

$V_{R_1} = V_{CC} - 2* V_D = (10.8 - 22.8)V \implies I_{R_1} = V_{R_1}/R_1 = (.9 - 1.9)mA \implies P_{R_{1_{MAX}}} = I_{R_{1_{MAX}}}^2*R_1 = 43mW$

(*) If $Q_3$ were not a Darlington, its $H_{FE}$ would be about $15$. In the worst case ($V_{CC}=24V$ and $I_{C_{3}}=50mA$), its base current should be in the $mA$ range, so $Q_1$'s collector current should be set to at least $(10-15)mA$. Since $V_{CE_1}$ must sink virtually all of the $V_{CC}$, the power to be dissipated would require a medium-power transistor for $Q_1$ as well.


### LTspice Simulation
Below is the simulation of the circuit with the LTspice software.<br/>
The simulation plots currents, voltages and powers on the transistors of the circuit, assuming a forward biased diode as a load. The simulation considers 2 different values ​​of $V_{CC}$ $(12V, 30V)$, for each of which the resistance $R_3$ is linearly modified from the value $15$&Omega; to $500$&Omega;, and then brought back to the value $15$&Omega;.<br/>
Plot 2 shows voltage as a unit of measurement on the Y-axis $(15-500V)$, but it should be read as the resistance in Ohms of $R_3$ $(15-500$&Omega;$)$.<br/>
For the simulation, the value $H_{FE}=1000$ was set for the transistor $Q_3$.
![plot](resources/ltspice-plot.jpg)
![schematic](resources/ltspice-schematic.jpg)
<br> 


### PCB Layout
![board-pcb](resources/diode-tester_pcb.jpg)


## About
Author : Alessandro Fraschetti (mail: [gos95@gommagomma.net](mailto:gos95@gommagomma.net))


## Licence
This project is under the [MIT license](LICENSE).
You are free to use this for any purpose, just try to give credit in the documentation of your project.
