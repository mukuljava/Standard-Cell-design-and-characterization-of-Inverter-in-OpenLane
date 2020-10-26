# Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane

The project involves the full ASIC implementation steps from RTL to GDSII, where a standard cell of inverter was embedded inside a RISC-V architecture of picorv32a core and then the whole automated PnR was done on Openlane flow using Google's skywater 130nm pdk. This project also involved standard characterization of the inverter using magic and ngspice. This characterization included the stating rise & fall transitions and delays.

# Contents

[1. Introduction to Openlane flow](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#1-introduction-to-openlane)

[2. Physical Design Flow](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#2-physical-design-flow)

[3. Invoking and running Openlane]()

# 1. Introduction to Openlane

Openlane is an automated RTL to GDSII flow based on several components embedded inside it: OpenROAD, Yosys, Magic etc. and consists different scripts, design exploration and optimization techniques. Openlane is built around Google's skywater 130nm process node and has the ability to perform full automated RTL to GDSII flow.

### Openlane Architecture flow

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/openlane_flow.png)

# 2. Physical Design Flow

## Simplified RTL to GDSII flow

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/simplified%20rtl%20to%20gdsii%20flow.png)

The normal generalized ASIC flow starts from Synthesis of RTL netlist and ends to getting GDSII file streamed after doing floorplanning, placement, timing analysis and routing.

### Synthesis

It converts RTL to a circuit out of components from standard cell library i.e creating gate level netlist using Yosys tool which is embedded inside of Openlane flow.

### Floorplanning 

Floorplanning is all about arranging system building blocks, pre-placed cells, decoupling capacitors, power planning, I/O pads placement in a proper way. Here power planning is done after routing. These pre-placed cells  such as SRAM, ADC/DAC, PLL are called as IPs (Intellectual Property). 

A chip to have a good floorplan following things should be taken under consideration:
- Utilization Factor

It can be defined as the ratio of total area occupied by netlist to the total area of the cell
  
i.e. Utilization Factor = Total area occupied by netlist / Total area of the cell
  
- Aspect Ratio

It can be defined as the ratio of Height and Width of die

i.e. Aspect Ratio = Height / Width

### Placement

Placement of the cells on floorplan rows aligned with the sites is called as placement. It is usually done in two steps : Detailed and GLobal.
