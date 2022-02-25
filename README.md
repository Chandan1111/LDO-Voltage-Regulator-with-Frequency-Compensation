# LDO-Voltage-Regulator-with-Frequency-Compensation
The purpose of this Hackathon is to implement the proposed design in 28 nm PDK (Process Design Kit).

 As a result of literature survey and Implemantation, this is a final Report Submission for successful completion of LDO-Voltage-Regulator-with-Frequency-Compensation design and simulation, for [Cloud Based Analog IC Design Hackathon](https://www.iith.ac.in/events/2022/02/15/Cloud-Based-Analog-IC-Design-Hackathon/)

## Table of Contents
1. [Introduction](#introduction)
2. [Problem with Typical LDO voltage regulator](#Problem-with-Typical-LDO-voltage-regulator)
3. [Capacitive Feedback for Frequency Compensation](#Capacitive-Feedback-for-Frequency-Compensation)
4. [Design Considerations for the VCCS](#Design-Considerations-for-the-VCCS)
5. [Error Amplifier](#Error-Amplifier)
6. [Pass Transistor](#Pass-Transistor)
7. [Working](#working)
9. [Implementation](#implementation)
10. [Schematic Netlist](#schematic-netlist)
11. [Simulation result](#simulation-result)
15. [References](#references)
16. [Acknowledgements](#acknowledgements)
17. [Author](#author)

## Introduction

POWER management is a very important issue in portable  electronic applications. The need for  multiple  on-chip voltage levels makes voltage regulators a critical part of an electronic system design. Portable electronic devices like cell phones require very efficient power management to increase the battery life whereas high-speed microprocessors need stable voltages that can supply fast varying currents on the order of few amperes.

The choice of a voltage regulator for a given application offers numerous design tradeoff considerations.The emphasis on efficiency has made low dropout (LDO) regulators the most popular class of linear regulators. But this increase in efficiency is achieved at the cost of a compromise in stability of the regulator. LDO regulators have high output impedance; this impedance, along with the load capacitance, creates a low frequency pole and decreases the overall phase margin.

This design gives an overview of stability problems in LDO voltage regulators and presents a modified LDO voltage regulator topology. Although the compensating circuit is very simple, the proposed topology successfully overcomes the problem of stability without significantly increasing the power consumption or die area.


## Problem-with-Typical-LDO-voltage-regulator

A closer look of a typical LDO voltage regulator reveals the fact that there are two low-frequency poles that need to be taken into consideration in evaluating the frequency response of the LDO’s closed-loop transfer function. One of the poles lies at the output of the regulator and the other one at the gate of the pass transistor.
There are, at least, two additional parasitic poles present in the LDO regulator. The third pole is lumped to the noninverting terminal of the error amplifier as a result of the input stage parasitic capacitors.
The error amplifier contributes with an internal pole since two-stage or cascode amplifiers are used to increase the amplifier’s dc gain. The system is therefore potentially unstable.

<p align="center">
	<img width="500" src="https://user-images.githubusercontent.com/20799294/155582127-fcf582cd-5070-435d-a4b7-a5d2079b0fc8.png" alt="Typical LDO voltage regulator."> 
	<h5 align="center">Figure 1: Typical LDO voltage regulator.</h5>
</p>


## Capacitive-Feedback-for-Frequency-Compensation

The basic idea behind the capacitive feedback which is responsible for frequency compensation is to introduce a left hand plane zero in the feedback loop that would replace the zero generated by ESR of the output capacitor. The proposed method starts with the addition of a pole–zero pair and proceeds toward eliminating the pole from the pole–zero pair.

The capacitor  is split into two frequency-dependent voltage-controlled current sources (VCCS) and grounded capacitors. The capacitor C1 and  the VCCS connected to vout do not significantly  alter  the voltage at that node since  CL is of severa l microfarads, whereas  C1  is on the order of few picofarads. It should also be noted that the  capacitor connected to vx is responsible for the additional pole Wp3; therefore it is eliminated to arrive to the final configuration of Fig. 1. This configuration generates the required zero; the LDO becomes a  two-loop  system. The VCCS is  a differentiator that increases the loop gain at high frequencies. The loop gain transfer function of the regulator with this configuration has one zero and two poles.

<p align="center">
	<img width="500" src="https://user-images.githubusercontent.com/20799294/155711189-ea65db89-bfe8-4918-9830-8b7f14f0f53a.png" alt="LDO voltage regulator topology with proposed frequency compensation"> 
	<h5 align="center">Figure 2: LDO voltage regulator topology with proposed frequency compensation</h5>
</p>

## Design-Considerations-for-the-VCCS

The transistor-level design challenge lies in realizing the frequency dependent VCCS with minimum die area and minimum power consumption while retaining the VCCS characteristics up to crossover  frequency of the loop  transfer function. The  simplest  realization of  this  circuit  and  transconductance gain enhanced structure is shown in Fig. 2(a) and 2(b) respectively.The bias current can be selected to meet the objective of minimal standby current; the limit is however determined by the frequency of its parasitic pole.

<p align="center">
	<img width="500" src="https://user-images.githubusercontent.com/20799294/155713863-b5b07387-6fbf-489c-8b5f-af0f3b091978.png" alt="LDO voltage regulator topology with proposed frequency compensation"> 
	<h5 align="center">Figure 2: Simplest realization of frequency dependent VCCS. (a) Simplest realization and (b) transconductance gain enhanced structure.</h5>
</p>

We need to improve the effective transconductance by increasing the bias current drastically increases the power consumption ( scales proportional to the square root of bias current). Therefore, alternate Gm enhancement techniques should be explored.
The transistor realization is shown in Fig. 3. The circuit consists of three parts. The first stage acts as a level-shifting buffer needed to down-shift the dc level which can be very close to  the  supply  voltage due to LDO characteristics of the regulator. The next stage is with enhancing OTA in feedback. The third stage consists of a 1:5 current mirror and bias sources that together perform  the  function of pumping the ac current through output. We can take advantage of a multiplication factor in the current mirror to increase the effective capacitance from 5 to 25 pF. Cascode current mirror and cascode bias current sources (bias by proper dc voltages and ) are used such that offset current is not significant enough to upset the dc output voltage of the regulator.

<p align="center">
	<img width="500" src="https://user-images.githubusercontent.com/20799294/155715916-4db711cd-1797-46e6-8a12-5e8218ad71c9.png" alt="Transistor level implementation of VCCS with G enhancement"> 
	<h5 align="center">Figure 3: Transistor level implementation of VCCS with Gm enhancement.</h5>
</p>

## Error-Amplifier

The error amplifier design demands careful attention to meet the required loop gain, transient response and stability. 
Ideal requirements of the error amplifier are: 1) high dc gain to ensure high loop gain (typically) for all loads; 
					       2) low output impedance to keep the pole at the input of the pass transistor at high frequencies; 
					       3) positive rail output to turn offpass transistor when the load turns off; and 
					       4) internal poles at significantly higher frequencies compared to the cross over loop frequency.
High output impedance of the error amplifier pushes the pole at the input of the pass transistor Wp2 to lower frequencies. Wp2 is pushed to higher frequencies by limiting the output impedance of the error amplifier; hence the use of a two-stage error amplifier is a must.

<p align="center">
	<img width="500" src="https://user-images.githubusercontent.com/20799294/155718678-27dd0311-fc2b-4674-9ede-64ff72bd6c04.png" alt="Transistor level implementation of amplifier"> 
	<h5 align="center">Figure 4: Transistor level implementation of error amplifier.</h5>
</p>

## Pass-Transistor

The important design consideration for the pass transistor is the dropout voltage. Increasing the size of the pass transistor lowers the dropout voltage for a particular output current, but wider pass transistor introduces higher input capacitance making it difficult to meet stability and slew rate requirements. The design presented uses a pass transistor of W/L ratio of 6000 that gives a maximum output current of 100 mA with a dropout voltage of 0.5 V. Minimum length is not used as it makes the transistor output impedance unacceptably low at high load currents

## Working





## Implementation 

### Schematic of LDO voltage regulator topology with proposed frequency compensation.

<p align="center">
	<img width="500" src="https://user-images.githubusercontent.com/20799294/155721410-34830e30-5fda-4fbd-8e00-908608cd165a.png" alt="Transistor level implementation of amplifier"> 
	<h5 align="center">Figure 5: Schematic of LDO voltage regulator topology with proposed frequency compensation.</h5>
</p>

### Schematic and symbol of Voltage controlled current source (VCCS) .

<p align="center">
	<img width="500" src="https://user-images.githubusercontent.com/20799294/155722237-004e5786-a87b-48bf-b52d-f80b4eec59a2.png" alt="Schematic of Voltage controlled current source (VCCS)"> 
	<h5 align="center">Figure 6: Schematic of Voltage controlled current source (VCCS).</h5>
</p>

<p align="center">
	<img width="500" src="https://user-images.githubusercontent.com/20799294/155722514-82b5f99a-9c02-41bb-a9bc-f388c28d461e.png" alt="Symbol of Voltage controlled current source (VCCS)"> 
	<h5 align="center">Figure 7: Symbol of Voltage controlled current source (VCCS).</h5>
</p>

### Schematic and symbol of Error Amplifier .

<p align="center">
	<img width="500" src="https://user-images.githubusercontent.com/20799294/155723177-adb88f20-6183-4933-9e7f-6b8a3bc6e58c.png" alt="Schematic of Error Amplifier"> 
	<h5 align="center">Figure 8: Schematic of Error Amplifier.</h5>
</p>

<p align="center">
	<img width="500" src="https://user-images.githubusercontent.com/20799294/155723188-614880f0-df92-44fb-bd89-6f25390d0b9b.png" alt="Symbol of Error Amplifier"> 
	<h5 align="center">Figure 9: Symbol of Error Amplifier.</h5>
</p>



## Schematic Netlist

The final netlist is as follows: 

```
*Custom Compiler Version S-2021.09
*Fri Feb 25 16:24:11 2022

*.SCALE METER
*.LDD
.GLOBAL gnd!
********************************************************************************
* Library          : LDO_regulator
* Cell             : error_amplifier
* View             : schematic
* View Search List : auCdl schematic
* View Stop List   : auCdl
********************************************************************************
.subckt error_amplifier vdd vinm vinp vout
*.PININFO vdd:I vinm:I vinp:I vout:O
MM4 net58 net58 gnd! gnd! n105_lvt w=0.1u l=0.04u nf=1 m=1
MM3 vout net58 gnd! gnd! n105_lvt w=0.1u l=0.04u nf=1 m=1
MM2 net46 net58 gnd! gnd! n105_lvt w=0.1u l=0.04u nf=1 m=1
MM1 net33 vinp net46 gnd n105_lvt w=0.2u l=0.08u nf=1 m=1
MM0 net24 vinm net46 gnd n105_lvt w=0.2u l=0.08u nf=1 m=1
MM7 net24 net24 vdd vdd p105_lvt w=0.2u l=0.04u nf=1 m=1
MM6 net33 net24 vdd vdd p105_lvt w=0.2u l=0.04u nf=1 m=1
MM5 vout net33 vdd vdd p105_lvt w=0.4u l=0.04u nf=1 m=1
.ends error_amplifier

********************************************************************************
* Library          : LDO_regulator
* Cell             : vccs_freq
* View             : schematic
* View Search List : auCdl schematic
* View Stop List   : auCdl
********************************************************************************
.subckt vccs_freq iout vdd vout
*.PININFO iout:O vdd:I vout:I
MM24 net117 Vbp net107 vdd p105_lvt w=0.1u l=0.1u nf=1 m=1
MM23 iout Vbp net110 vdd p105_lvt w=0.15u l=0.03u nf=1 m=1
MM22 net107 net117 vdd vdd p105_lvt w=0.1u l=0.1u nf=1 m=1
MM21 net110 net117 vdd vdd p105_lvt w=0.15u l=0.03u nf=1 m=1
MM7 net24 net24 vdd vdd p105_lvt w=0.1u l=0.04u nf=1 m=1
MM6 net104 net24 vdd vdd p105_lvt w=0.1u l=0.04u nf=1 m=1
CC25 net96 gnd! 1p $[CP]
MM20 iout Vbn net84 gnd n105_lvt w=0.15u l=0.03u nf=1 m=1
MM19 net84 net39 gnd! gnd! n105_lvt w=0.15u l=0.03u nf=1 m=1
MM17 net98 net39 gnd! gnd! n105_lvt w=0.1u l=0.1u nf=1 m=1
MM18 net96 Vbn net86 gnd n105_lvt w=0.1u l=0.1u nf=1 m=1
MM16 vdd vout net98 vdd n105_lvt w=0.1u l=0.05u nf=1 m=1
MM5 net117 net104 net96 gnd n105_lvt w=0.75u l=0.03u nf=1 m=1
MM4 net39 net39 gnd! gnd! n105_lvt w=0.1u l=0.1u nf=1 m=1
MM3 net86 net39 gnd! gnd! n105_lvt w=0.1u l=0.1u nf=1 m=1
MM2 net93 net39 gnd! gnd! n105_lvt w=0.1u l=0.1u nf=1 m=1
MM1 net104 net96 net93 gnd n105_lvt w=0.1u l=0.04u nf=1 m=1
MM0 net24 net98 net93 gnd n105_lvt w=0.1u l=0.04u nf=1 m=1
.ends vccs_freq

********************************************************************************
* Library          : LDO_regulator
* Cell             : LDO_reg
* View             : schematic
* View Search List : auCdl schematic
* View Stop List   : auCdl
********************************************************************************
.subckt LDO_reg Vout Vref vdd
*.PININFO Vout:O Vref:I vdd:I
XI0 net33 Vref net30 net34 error_amplifier
XI1 net30 net29 Vout vccs_freq
RR8 Vout gnd! 'Rload' $[RP]
RR5 net30 gnd! 5meg $[RP]
RR4 Vout net30 6.65meg $[RP]
CC9 Vout gnd! 2.2u $[CP]
MM13 Vout net34 vdd vdd p105_lvt w=0.182000m l=0.03u nf=52 m=1
.ends LDO_reg

```

- Netlist is generated by using Custom Compiler.


## Simulation result

- Custom Compiler Waveform

<p align="center">
	<img width="1100" src="https://user-images.githubusercontent.com/20799294/155723745-067eacce-52c3-46be-9a14-50ca24175b74.png" alt="refference ICG Trans"> 
	<h5 align="center">Figure 9: Experimental LDO output voltage as function of the load current.</h5>
</p>.

<p align="center">
	<img width="1100" src="https://user-images.githubusercontent.com/20799294/155725336-9aa2fcb6-fbb9-4bb6-ba8b-7799c032ef16.png" alt="refference ICG Trans"> 
	<h5 align="center">Figure 10: LDO output voltage and load current over the Parametric sweep of Rload .</h5>
</p>.

<p align="center">
	<img width="1100" src="https://user-images.githubusercontent.com/20799294/155725493-37c0a2ce-a8d5-42ca-a4e3-6ffea36d46fd.png" alt="refference ICG Trans"> 
	<h5 align="center">Figure 10: Tabulated values of LDO output voltage and load current over the Parametric sweep of Rload .</h5>
</p>.

<p align="center">
	<img width="1100" src="https://user-images.githubusercontent.com/20799294/155724285-fbf29eee-ad57-4b43-b154-0b3219819333.png" alt="refference ICG Trans"> 
	<h5 align="center">Figure 11: Load regulation characteristics of the LDO regulator as output current is varied from 1 to 40 mA without improved compensation.</h5>
</p>.

<p align="center">
	<img width="1100" src="https://user-images.githubusercontent.com/20799294/155724585-b6b5b6a6-4c77-4599-bd81-6c255663d1b9.png" alt="refference ICG Trans"> 
	<h5 align="center">Figure 12: Load regulation characteristics of the LDO regulator as output current is varied from 1 to 40 mA with improved compensation.</h5>
</p>.

<p align="center">
	<img width="1100" src="https://user-images.githubusercontent.com/20799294/155751928-432bd821-2512-4281-a1aa-345d39d223eb.png" alt="refference ICG Trans"> 
	<h5 align="center">Figure 13: Transient ouput voltage of the LDO regulator as Vref is varied without improved compensation.</h5>
</p>.

<p align="center">
	<img width="1100" src="https://user-images.githubusercontent.com/20799294/155751939-b933dd0d-24e2-4236-836b-bc5b0dff71c0.png" alt="refference ICG Trans"> 
	<h5 align="center">Figure 14: Transient ouput voltage of the LDO regulator as Vref is varied with improved compensation.</h5>
</p>.

<p align="center">
	<img width="1100" src="https://user-images.githubusercontent.com/20799294/155752589-022273c4-8099-4c7b-959b-eac66f93f30f.png" alt="refference ICG Trans"> 
	<h5 align="center">Figure 15: Start-up time of the regulator for a load current of 2 mA..</h5>
</p>.


## References

- [A Frequency Compensation Scheme for LDO Voltage Regulators ](https://ieeexplore.ieee.org/document/1304961)

- [Low dropout voltage regulator with non-Miller frequency compensation](https://patentimages.storage.googleapis.com/71/c9/da/48135b5c03c914/US6710583.pdf)

- [Designing With Low-Dropout Voltage Regulators](http://ww1.microchip.com/downloads/en/devicedoc/ldobk.pdf)

## Acknowledgements

- [Kunal Ghosh](https://github.com/kunalg123), Founder, VSD Corp. Pvt. Ltd
- [Indian Institute Of Technology (IIT), Hyderabad](https://iith.ac.in/)
- [Synopsys](https://www.synopsys.com/)

## Author

[Chandan K](https://github.com/Chandan1111),MTech in VLSI Design at Vellore Institute of Technology
