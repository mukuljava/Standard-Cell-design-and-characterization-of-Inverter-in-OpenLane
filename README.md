# Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane

The project involves the full ASIC implementation steps from RTL to GDSII, where a standard cell of inverter was embedded inside a RISC-V architecture of picorv32a core and then the whole automated PnR was done on Openlane flow using Google's skywater 130nm pdk. This project also involved standard characterization of the inverter using magic and ngspice. This characterization included the stating rise & fall transitions and delays.

# Contents

[1. Introduction to Openlane flow](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#1-introduction-to-openlane)

[2. Physical Design Flow](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#2-physical-design-flow)

[3. Invoking and running Openlane](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#3-invoking-and-running-openlane)

[4. Building Design in Openlane](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#4-building-design-in-openlane)

[5. RTL to GDSII flow](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#5-rtl-to-gdsii-flow)
  - [Synthesis](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#synthesis-1)
  - [Floorplan](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#floorplan)
  - [Placement](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#placement-1)
  - [Cell design and Characterization](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#cell-design-and-characterization)
  - [Extraction of LEF file](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#extraction-of-lef-file)
  - [Plugging the lef file into Openlane](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#plugging-the-lef-file-into-openlane)
  - [Improving Timing Violation](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#improving-timing-violation)
  - [Clock tree Synthesis](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#clock-tree-synthesiscts)
  - [PDN and Routing](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#pdn-and-routing)
  - [SPEF and GDSII](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#spef-and-gdsii)
 
[6. Acknowledgements](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane#6-acknowledgements)

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

### Floorplanning/Power Planning

- Floorplanning is all about arranging system building blocks, pre-placed cells, decoupling capacitors, power planning, I/O pads placement in a proper way. Here power planning is done after routing. These pre-placed cells such as SRAM, ADC/DAC, PLL are called as IPs (Intellectual Property). 
- Dimensions, pin locations and tracks of the cell are defined.
- Power Distribution Network (PDN) is framed so that the cells or IPs or Macros get desired voltage.

A chip to have a good floorplan following things should be taken under consideration:
- Utilization Factor: It can be defined as the ratio of total area occupied by netlist to the total area of the cell 

i.e. Utilization Factor = Total area occupied by netlist / Total area of the cell
  
- Aspect Ratio: It can be defined as the ratio of Height and Width of die

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

# 3. Invoking and running Openlane

### For open-source implementation of Digital ASIC Design, we need 3 components:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Digital%20ASIC%20Design.png)

- RTL design: Available on librecores.org, opencores.org or github.com.
- EDA tools: Qflow, OpenRoad, Openlane, Magic, Spice simulation.
- PDK(Process Design Kit): It is a collection of files used to model a fabrication. Here we used open source pdk of Google's skywater 130nm pdk.

Detailed description on how to build and invoke openlane is given [here](https://github.com/nickson-jose/openlane_build_script).

# 4. Building Design in Openlane

- Openlane is opened in interactive mode(step by step) by traversing to the directory - "openlane_working_dir". 
```
./flow.tcl -interactive
```
- Some input packages are needed to run this flow:
```
package require openlane 0.9
```

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/invoking%20openlane.png)

## Preparation:

- Before staring the flow, we first need to prepare the design by setting up the files with respect to the flow. Openlane as approximately 30-40 designs of which we chose a design based on RISC-V architecture (picorv32a).
```
prep -design picorv32a
```
- Under this design you will find config.tcl file which contains the configuration of this specific design. The default values from openlane by overidden by the values of this design. Here is how it looks:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/preping%20design.png)

# 5. RTL to GDSII flow:

## Synthesis:

After preparation the next step is to do synthesis. The following command runs Yosys and abc for synthesis:
```
run_synthesis
```
After synthesis the following configuration is observed:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Synthesis/after_synthesis_config.png)

Here, we can see the flop ratio:

Flop ratio = No. of DFFs / Total no. of cells
           = 1634/17323
           = 0.094
           
Successful synthesis with slack:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Synthesis/synthesis%20success.png)

After successfully running synthesis some files/reports are generated. These reports contain synthesized netlist:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Synthesis/after%20running%20run_synthesis.png)

## Floorplan:

- Default settings: openLane_flow/configurations/floorplan.tcl
Here the default settings are: VMETAL = 2, HMETAL = 3

Now set the switches of vertical and horizontal metal layer by commands:
```
set ::env(FP_IO_HMETAL) 3
set ::env(FP_IO_VMETAL) 4
```
Now, run the floorplan step by executing the command:
```
run_floorplan
```
Successful running of floorplan step is shown in below snapshot:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Floorplan/run_floorplan.png)

To see whether the changes are implemented or not open the .def file which will be found in the following path. In this file you can also observe the die area:
openLane_flow/designs/picorv32a/runs/17-10_10-23(this folder is created after you do synthesis)/results/floorplan/picorv32a.floorplan.def/

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Floorplan/overriden_values.png)

Below snapshot shows the die area after the floorplan step in which the coordinates can be read as(Lower left X value, Lower left Y value)(Upper right X value, Upper right Y value).

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Floorplan/die_area.png)

To see the actual layout after the floorplan open Magic by using command:
```
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

This is how it looks like:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Floorplan/magic.png)

These are the standard cells:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Floorplan/standard%20cells.png)

*Note: Floorplan does not take care of standard cells. Power distribution is done in floorplanning in an ideal terms but in openlane, order is little bit different. Floorplan does not create power distribution network. We do power/ground generation post placement and CTS.*

## Placement:

The next step is the placement step by using command:
```
run_placement
```

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Placement/run_placement.png)

As the overflow(OVFL) decrease, there is increase the number of iterations, this helps in converging the design. 

There are two ways in which placement is done:
- Global Placement: It is also called as coarse placement. PnR tool will determine the approximate position of wach cell according to timing and congestion. Coarse placement is mainly performing for initial timing and congestion analysis.

- Detailed Placement: It means that standardization or proper placement of cells in done in rows and there are no overlaps.

To observer the layout after placement stage navigate to the following path:
```
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

Here is the how the layout of standard cells is seen after the placement stage:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Placement/std%20cells%20after%20run_placement.png)

Here DEF and LEF are files which contains some placement information. It will be discussed in next section when we plug in the custom layout of inverter into picorv32a design.

## Cell design and characterization:

- Clone the git [link](https://github.com/nickson-jose/vsdstdcelldesign). This contains the .mag file of custom layout of the inverter. 
- Copy the folder into the folder /vsdstdcelldesign. 
- Launch maic from this location by using command ```magic -T sky130.tech sky130_inv.mag &```

This is how the layout looks like:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Cell%20design%20and%20characterization/cmos%20inverter.png)

- To create extraction files:
```
extract all
```

- To spice file to be used in ngspice. These are to extract parasitic capacitances
```
ext2spice cthresh 0 rthresh 0
ext2spice
```

Please find the snapshot below:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Cell%20design%20and%20characterization/ext2spice.png)

- Make the specified changes in the extracted spice file which include libraries, power supply, type of analysis(transient) etc. Snapshot below

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Cell%20design%20and%20characterization/spice_file.png)

- Navigate to vsdcelldesign folder and run this command to execute ngspice:
```
ngspice sky130_inv.spice
```

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Cell%20design%20and%20characterization/ngspice.png)

- After invoking ngspice at the prompt we need to spcify the values between which we want the plot
```
plot y time a
```

This is the transient plot which we asked for;

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Cell%20design%20and%20characterization/transient.png)

- Now characterizing a cell can be done by finding 4 parameters -> rise transition, fall transition, fall cell delay(propagation delay) and rise cell delay(propagation delay)
  - Rise transition: Time taken by a signal to go from 20% to 80% of its maximum value.
  - Fall transition: Time taken by a signal to go from 80% to 20% of its maximum value.
  - Rise cell delay(propagation delay): For any propagation delay is measured between 50% of input fall transition to the
corresponding 50% of output rise transition.
  - Fall cell delay(propagation delay): For any propagation delay is measured between 50% of input rise transition to the
corresponding 50% of output fall transition.
  - Since the maximum value of voltage is 3.3v, there for rise transition:
    - 20% of 3.3 = 0.66v -> 2.18198e-09
    - 80% of 3.3 = 2.64v -> 2.24605e-09
    therefore, 
    
    1. rise transition = 2.24605-2.18198
                       = 0.064ns
  - For fall transition, 2.64v -> 4.0525e-09 and 0.66v -> 4.0949e-09
  
    2. rise transition = 4.0949-4.0525
                       = 0.0424ns
  - For propagation delay, 50% = 1.65v
  
    3. rise delay = o/p rise - i/p fall
                  = 2.21065e-09 - 2.15022e-09
                  = 0.06043ns
                  
    4. fall delay = o/p fall - i/p rise
                  = 4.07748e-09 - 4.0501e-09
                  = 0.02738ns

## Extraction of LEF file

- What is a lef file? It can be found out from this [link](https://github.com/nickson-jose/vsdstdcelldesign#defining-lef-properties-and-extracting-lef-file).

- There are some guidelines to make standard cell set before we extract a lef file:
  - IO ports must lie on the intersection of the vertical and horizontal tracks.
  - Width of standard cell should be in odd multiples of the track pitch and Height in odd multiples of the vertical path.
  - Defining port and setting correct class and use attributes to each port is the first step. Find the steps [here](https://github.com/nickson-jose/vsdstdcelldesign#create-port-definition).

- Press g to activate the grid. This is how it looks. 

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/LEF%20extraction%20and%20plugging/grid%20changes.png)

- Convert the grids into tracks by executing this command in tkcon window:
```grid 0.46 0.34 0.23 0.17```

This is how it is done

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/LEF%20extraction%20and%20plugging/grid_conversion.png)

- After conversion the tracks need to be saved using command:
```save sky130_vsdinv.mag```

- Extract the lef file from magic using the command in tkcon window:

```lef write```

## Plugging the lef file into Openlane

- Copy the created lef file to the src folder of picorv32a design.
- We need to add custom cells into openalne flow.
- Lib files are needed which are present in directory of vsdcelldesign.
- Copy these lib files as well.
- Make some changes in the config.tcl file as shown:
- Also add this line:
```set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]```

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/LEF%20extraction%20and%20plugging/config_file.png)

- Now invoke openalne using the same commands used in 3.

- After invoking add the 2 files to ensure openlane flow takes our lef files

```
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
```

Here is the snapshot:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/LEF%20extraction%20and%20plugging/lef_preparation.png)

- Now run synthesis to include the custime lef file into the design by running command: ```run_synthesis```

- We can now see the changes made to the design as it includes the instance of the custom inverter and the value of slack.

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/LEF%20extraction%20and%20plugging/custom_inv_instance.png)

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/LEF%20extraction%20and%20plugging/synhtesis%20after%20doing%20chnages%20in%20config.tcl%20and%20adding%20lef%20files%20.png)

## Improving timing violation:

- After synthesis, the worst negative slack(WNS) came out to be negative(-17.96), which is not acceptable. Timing constraints are very important and hence need to be improved.
- We need to check the strategy which defines the balance between delay and area. If the strategy is set to 2, we reset it to 1. Here are the commands respectively:

```
echo $::env(SYNTH_STRATEGY)
set ::env(SYNTH_STRATEGY) 1
```

*Note: This will increase the area but improves the timing*

- Another method to improve timing is sizing the cells i.e upsizing or downsizing the cells. We are upsizing the cell if the value is 1:

```
echo $::env(SYNTH_SIZING)
set ::env(SYNTH_SIZING) 1
```
- Now again execute ```run_synthesis``` to check the slack.
- run_floorplan
- run_placement
- Check the results in ~/trial/result/placement
- Now run magic to see the instances of the cell we plugged in:

```
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Timing%20Violation/vsdinv%20instances.png)

- Power and Ground rails are shared, called as abutment. See the snapshot:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Timing%20Violation/abutment%20.png)

- To check the pre sta configuration, create a file naming pre_sta.conf in openlane directory
  - Copy the file my_base.sdc from extras folder to src folder
  - execute the sta command to run pre sta condition: ```sta pre_sta.conf``` to get the slack of -3.36ns. See below snapshot.
  
![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Timing%20Violation/sta%20pre_sta.conf.png)
  
- Optimize the delays as Fanouts increases when delay increase. We can reduce the fanouts by command:

```set ::env(SYNTH_MAX_FANOUT) 4```

- Now run synthesis again to check the slack

- To check the nets with driver and fanouts use command:

```report_net -connections _12904_```, where _12904_ is net.

Here is the snapshot:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/Timing%20Violation/to%20check%20te%20load%20of%20driver.png)

- Now again execute: ```sta pre_sta.conf``` to get the slack of -1.76ns.
- Now we can replace the cell instance with a bigger buffer. It will increase the size of the design but timing is improved.

```replace_cell _41881_ sky130_fd_sc_hd_buf_4```

In this way we can increase the size of the buffer and improve the timing

## Clock Tree Synthesis(CTS)

- To create a new netlist to be used by openlane flow:

```write_verilog /openlane_flow/........../results/synthesis/picorv32a/synthesis.v``` This will overwrite current verilog file.

- To check whether this is the new verilog file just check the last cell which is replaced.

- Hence, we will not do syntesis again or else it will take previous netlist.

- Now do run_floorplan and run_placement 

- Now run clock tree synthesis by executing command: ```run_cts```

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/CTS/cts_run.png)

- This creates a file cts.v where buffers are added

- Check the current DEF(Design Exchange Format)

```echo $::env(CURRENT_DEF)```

- Start Openroad to do some STA by executing: ``` openroad```
  - First create db by executing: ```read_lef /openlane/designs/picorv32a/runs/trials1/tmp/merged.lef```
  - ```read_def /openlane/designs/picorv32a/runs/trials1/results/cts/pricorv32a.cts.def```
  - ```write_db pico_cts.db```
  - ```read_db pico_cts.db```
  - ```read_verilog openLANE_flow/designs/picorv32a/...../results/synthesis/picorv32a.synthesis.cts.v```
  - ```read_liberty -max $::env(LIB_MAX)```
  - ```read_liberty -min $::env(LIB_MIN)```
  - ```read_sdc openLANE_flow/designs/picorv32a/src/my_base.sdc```
  - To calculate actual cell delay: ```set_propagated_clock [all_clocks]```
  - To check slack: ```report_checks -path_delay min_max -fields {slew trans set cap input_pin} -format full_clock_expanded -digits 4```
  - Now the slack came out to be = 4.656ns and Hold = 0.388ns 
  - Now exit openroad
  
## PDN and Routing

- We need to check the last step which was performed to check current .def. 

```echo $::env(CURRENT_DEF)``` This will tell that the current def is *cts.def*

- Now we need to do routing, but before that we have Power Distribution Network which ideally is done during floorplan but due to adjustments in openlane we did not do it. Hence we will do it now.

```gen_pdn```

- It creates .lef and .def file. It also creates grids and tracks for power and ground.

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/PDN%20and%20Routing/gen_pdn.png)

- For routing we will be using TritonCTS route. The routing ranges from 0 to 3 and 14.
  - 0-3: It takes approximately 30 mins to route. It also requires less memory.
  - 14: It takes approximately 45 mins to route. It requires more memory.

- You can check which range is used and set it to 14 by:

```echo $::env(ROUTING_STRATEGY)```
```set ::env(ROUTING_STRATEGY) 14```

- Now start routing by using command

```run_routing```

Here is the snapshot:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/PDN%20and%20Routing/routing%20completed.png)

- It first performs Global Route which is done by Fast Route. Output is set of the routing guides for each of the nets.

- It then performs Detailed Route executed by Triton Route which honours preprocessed routing guides(obtained after fast route) i.e., attempts as much as possible to route within route guides.

- Works on propsed Mixed Integer Linear Programming (MILP) based routing scheme with intra-layer parallel and inter-layer sequential routing framework.

- So here can see that the memory required is 545.41MB with 0 violations. Had routing been done at some different range, it could have shown some violations and less memory.

## SPEF and GDSII

- Now we will extract SPEF(Standard Parasitic Exchange Format) file outside openlane. Navigate to this path - ~/work/tools/SPEF_EXTRACTOR

- To generate SPEF we need to specify the LEF and DEF file - ```python3 main.py /openLANE_flow/designs/picorv32a/runs/trial1/tmp/merged.lef /openLANE_flow/designs/picorv32a/runs/trial1/results/routing/picorv32a.def```

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/SPEF%20and%20GDSII/spef_extraction.png)

- SPEF file is generated in the routing folder. Here is the snapshot:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/SPEF%20and%20GDSII/spef%20file%20generated.png)

- Here is the snippet from SPEF file:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/SPEF%20and%20GDSII/snippet%20of%20spef%20file.png)

- GDS file is generated from the routed def. See the snapshot here:

![alt text](https://github.com/mukuljava/Standard-Cell-design-and-characterization-of-Inverter-in-OpenLane/blob/main/Openlane/SPEF%20and%20GDSII/gds%20file%20created.png)

- To read the .gds file go to magic and in the tkcon window execute - 

```gds read picorv32a.gds```

- This will open the gds file. It takes some time as this is a heavy one. The file contains all the information of the design which we designed.

# 6. Acknowledgements

- [Kunal Ghosh](https://github.com/kunalg123), Co-founder (VSD Corp. Pvt. Ltd)
- [Nickson Jose](https://github.com/nickson-jose)
