# Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane

The project involves the full ASIC implementation steps from RTL to GDSII, where a standard cell of inverter was embedded inside a RISC-V architecture of picorv32a core and then the whole automated PnR was done on Openlane flow using Google's skywater 130nm pdk. This project also involved standard characterization of the inverter using magic and ngspice. This characterization included the stating rise & fall transitions and delays.

# Contents

[1. Introduction to Openlane flow](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#1-introduction-to-openlane)

[2. Physical Design Flow](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#2-physical-design-flow)

[3. Invoking and running Openlane](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#invoking-and-running-openlane)

# 1. Introduction to Openlane

Openlane is an automated RTL to GDSII flow based on several components embedded inside it: OpenROAD, Yosys, Magic etc. and consists different scripts, design exploration and optimization techniques. Openlane is built around Google's skywater 130nm process node and has the ability to perform full automated RTL to GDSII flow.

### Openlane Architecture flow

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/openlane_flow.png)

# 2. Physical Design Flow

## Simplified RTL to GDSII flow

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/simplified%20rtl%20to%20gdsii%20flow.png)

The normal generalized ASIC flow starts from Synthesis of RTL netlist and ends to getting GDSII file streamed after doing floorplanning, placement, clock tree synthesis and routing.

### Synthesis

- It converts RTL to a circuit out of components from standard cell library i.e creating gate level netlist using Yosys tool which is embedded inside of Openlane flow. 
- Cell mappimg is done to define a particular area for cell placement.
- Pre-Layout Static Timing Analysis is done using OpenSTA.

### Floorplanning 

- Floorplanning is all about arranging system building blocks, pre-placed cells, decoupling capacitors, power planning, I/O pads placement in a proper way. Here power planning is done after routing. These pre-placed cells such as SRAM, ADC/DAC, PLL are called as IPs (Intellectual Property). 
- Dimensions, pin locations and tracks of the cell are defined.
- Power Distribution Network (PDN) is framed so that the cells or IPs or Macros get desired voltage.

A chip to have a good floorplan following things should be taken under consideration:
- Utilization Factor:

It can be defined as the ratio of total area occupied by netlist to the total area of the cell 

i.e. Utilization Factor = Total area occupied by netlist / Total area of the cell
  
- Aspect Ratio:

It can be defined as the ratio of Height and Width of die

i.e. Aspect Ratio = Height / Width

### Placement

- Placement of the cells on floorplan rows aligned with the sites is called as placement. It is usually done in two steps : Detailed and Global.
- Detailed placement is done to legalize the global placement.

### Clock Tree Synthesis

- As the name suggests, clock tree is synthesized. Here the tool used is [TritonCTS](https://github.com/The-OpenROAD-Project/OpenROAD/tree/master/src/TritonCTS) which is again part of Openlane flow. You just need to run the command in Openlane:
```
run_cts
```
- Create a clock distribution network including Flip Flops with minimum skew (although tough).
- Usually the tree shapes like an H hence called H tree or X called X tree etc.

### Routing

- Implements interconnect using the available metal layers. These metal tracks form a huge routing grid.
- Two types of routing:
  - Fast route: Fast route engine (efficient and high quality)is used for global route. It generates routing guides for each of the nets. 
  - Detailed route: Uses the routing guides from global routes and implement the actual wiring.

### Sign-Off

- It contains the Physical varifications like Design Rule Checking(DRC) and Layout vs Schematic(LVS)
- Timing Verification like Static Timing Analysis
- Lastly final GDSII layout file is streamed out from routed def(Magic).

# 3.Invoking and running Openlane
