# Advanced-Physical-Desing-Using-OpenLANE-Sky130
# VSD Sky130 OpenLANE Workshop – Advanced Physical Design Using OpenLANE / Sky 130

This repository contains all the information included in the Advanced Physics Design Using OpenLANE / Sky 130 workshop. This workshop helped me in learning ASIC implementation steps from RTL to GDSII flow using open source EDA tools and Google – Skywater technology PDK. Google – Skywater130 PDK is the first manufacturable 130nm opensource PKD. 

## Table of Content
1.	Introduction to OpenLANE
2.	Day 1: Inception of Open-Source EDA, OpenLANE & Sky130 PDK
3.	Day 2: Good Floor planning Vs Bad Floor planning and Introduction to library cells
4.	Day 3: Design library cell using Magic layout and ngSpice characterization
5.	Day 4: Pre-Layout timing analysis and importance of good clock tree
6.	Day 5: Final steps for RTL2GDS using TritonRoute and OpenSTA
7.	Acknowledgements 

## 1. Introduction to OpenLANE

OpenLANE is an automated RTL to GDSII flow which includes different opensource tools like OpenROAD, Yosys, Magic, Netgen, Fault etc., apart from many custom methodology scripts for design exploration and optimization. It is an open-source flow for a true open source tape-out experiment. Its main goal is to produce a clean GDSII with no human interaction. It is tuned for Skywater 130nm Open PDK. It can be used to harden macros & chips.
It operates in 2 modes: Autonomous & Interactive. 

## 2. Day 1: Inception of Open-Source EDA, OpenLANE & Sky130 PDK

Day1 starts with introduction to System on Chip (SoC) basics like chip, PADs, core, die etc., and RISC-V Instruction Set Architecture (ISA). ISA is a language of the computer and the way we talk to the computer. In RISC-V to layout flow, the code in the RISC-V Architecture is implemented using RTL and converts code into Hardware Description Language (HDL) then code is converted to layout using the general RTL to GSD flow.  


### For open-source implementation of Digital ASIC Design, we need 3 components:

![Day 1 - ASIC components](https://user-images.githubusercontent.com/73480418/97202970-ac629680-178a-11eb-8aaf-d637127beb3c.PNG)


RTL IP’s: these are easily available on open-source platforms like librecores.org, opencores.org, github.com etc.,
EAD Tools: QFlow, OpenROAD, OpenLANE are open-source EDA tools available.  
PDK DATA: Open-source PDK released by Google + Skywater technology. 

### Simplified RTL to GDS flow:

![Day 1 - simplified RTL to GDS flow](https://user-images.githubusercontent.com/73480418/97203389-1aa75900-178b-11eb-943c-10ea44f71cec.PNG)

   -	**Synthesis**
            Design RTL is translated into circuits made out of components from Standard Cell Library (SCL). The resultant circuit is described in HDL and usually refers to as the gate level netlist. The gate level netlist is functionally equal to the RTL.
   - **Floor Planning and Power Planning (FP & PP)**
            Floor planning and power planning varied depending upon is we are implementing on one component (macro floor planning) or a whole chip. The main objective here is to plan a silicon area & create robust power distribution network to power the circuits.
            In Macro floor planning, macro dimension, pin location, rows & routing tracks are defined. 
            In power planning, power network is constructed using power pads, power straps and power rings.
   - **Placement**
            In Placement, gate level netlist cells are replaced by macros. Placement of the cells on the floorplan, aligned with the sites is called placement, it helps to interconnect the delay and also enable successful routing. Usually placement is done in 2 steps: Global placement and detailed placement. 
            Global Placement: It tries to find the optimal placements for all cells, such placement is legal which means there can be some cell overlaps.
            Detailed Placement: In detailed Placement, positions obtained from the global placement are minimally altered to be legal. 
   - **Clock Tree Synthesis (CTS)**
            This step is used to route the clock before the signal are routed. By creating a clock distribution network to deliver the clock to all sequential elements, with minimal skew and in a good shape. The clock network is usually in a shape of tree.
   - **Routing**
            After clock routing now it comes to signal routing. In Routing, valid horizontal and vertical pattern of wires are found to implement net connecting cells together using available metal layers. Routing is performed in 2 steps: Global routing – generates routing guides and detailed routing – uses the routing guides to implement the actual wiring.
    - **Sign-Off**
            In sign-off stage Physical verification is done using Design Rule Checking (DRC) & Layout Vs Schematic (LVS) and Timing verification is done using Static Timing Analysis (STA).

![Day 1 - Detailed ASIC design flow](https://user-images.githubusercontent.com/73480418/97203402-23982a80-178b-11eb-8d73-e5fb659bda6a.PNG)


Above picture shows the detailed ASIC design flow. Here new concepts like design exploration, Design for test (DFT), logic equivalence check (LEC), antenna diodes insertion was explained. 
Design exploration helps in finding the best configuration for the design. DFT is useful to make sure our design is ready for testing after fabrication. As optimization is performed in physical design step, which can form change in the gate level netlist (netlist generated by the synthesis step), we need to perform LEC. Every time netlist changes, verification mush be performed. LEC formally confirms the function did not change after modifying the netlist. Antenna Diode problem arises for long nets which is avoided by using fake antenna diodes next to every cell after placement. Later Antenna Checker (Magic) is run. If it reports antenna violations then fake diode replaced by actual one.

**LAB:**

To run OpenLANE

![Day 1 - to initate OpenLANE](https://user-images.githubusercontent.com/73480418/97203757-a5885380-178b-11eb-8f97-37d2b7763192.PNG)


Results of synthesis showing design components

![Day 1 - synthesis showing desing components](https://user-images.githubusercontent.com/73480418/97203957-df595a00-178b-11eb-9f34-c798b3e9301d.PNG)


Synthesis showing chip area and number of D F/F’s

![Day 1 - synthesis showing chip area and D FlipFlopss](https://user-images.githubusercontent.com/73480418/97203976-e54f3b00-178b-11eb-8b70-0015a8d021b8.PNG)


Slack calculation of design

![Day 1 - slack calculation of design](https://user-images.githubusercontent.com/73480418/97204035-f7c97480-178b-11eb-8343-2e6c6f93a2ae.PNG)


## 3. Day 2: Good Floor planning Vs Bad Floor planning and Introduction to library cells
On Day 2, definition of width and height of core and die were explained. Later, the concepts like Utilization factor and Aspect ratio were explained in detail and their importance to understand the design was also mentioned.
                Utilization factor = (Area occupied by netlist) / (Total area of the core)
Ideally, utilization is considered to be 50% - 60% and utilization factor to be 0.5 / 0.6.
                Aspect ratio = (Height (of the die)) / (Width (of the die))
Steps involved to define Pre-placed cell & its advantage of enhancing reusability and de-coupling capacitors and how they help during switching to avoid failure was explained. Once the pre-placed cells are located on the core, they cannot be moved further so these needs to be place carefully. The pre-placed cells are surrounded by De-coupling capacitors. A fully charged de-coupling capacitor is placed parallel to circuits to ensure proper supply of peak current Ipeak by decoupling them from main supply voltage. Hence de-coupling capacitor ensures proper local communication while multiple Vdd & Vss lines lead to proper global communication (which is taken care in power planning) avoiding voltage droop and ground bounce conditions. Step of pin placement & logical cell placement blockage is explained. Logical cell placement is done to avoid PnR tool to place anything cells and to reserve the area for inputs and output pins.


### Cell Design:
  Cell design is don’t in 3 parts.
        - 	**Inputs**
                  Inputs for cell design flow are PDKs (Process design kits), DRC & LVS rules, SPICE models, library & user-defined specs.
        - 	**Design Steps**
                  Design steps of cell design involve Circuit Design, Layout Design, Characterization. The software GUNA used for characterization. The characterization can be classified as Timing characterization, Power characterization and Noise characterization. 
        - 	**Outputs**
                  Outputs of the Design are CDL (Circuit Description Language), GDSII, LEF, extracted Spice netlist (.cir), timing, noise, power.libs, function.

### Typical Characterization Flow:
   - Reading the Model file of CMOS containing basic property definitions.
   - Reading the extracted Spice Netlist.
   - Recognize / Define the behaviour of the cell -- buffer cell.
   - Read the subcircuits -- of the inverter. 
   - Attach the necessary power sources. 
   - Apply an input or the stimulus.
   - Provide the necessary output capacitance.
   - Provide the necessary simulation commands.
   
### Important parameters of Timing Characterization:
   - Rise Delay: Time taken for waveform to rise from 20% to 80% of VDD.
   - Fall Delay: Time taken for waveform to fall from 80% to 20% of VDD.
   - Propagation Delay: Measured between 50% of Input transition to 50% of Output transition.

**LAB:**

Run Floorplan is completed successfully

![Day 2 - floor plan results](https://user-images.githubusercontent.com/73480418/97206826-a28f6200-178f-11eb-9015-d718946701fc.PNG)


Setting up VMETAL and HMETAL

![Day 2 - seting up VMETAL and HMETAL](https://user-images.githubusercontent.com/73480418/97205087-580ce600-178d-11eb-9da2-5ec8c61516be.PNG)


Review floor plan layout in Magic

![Day 2 - review floorplan layout in magic](https://user-images.githubusercontent.com/73480418/97205299-a7531680-178d-11eb-9937-93bc7e894bf0.PNG)


Run placement is completed successfully

![Day 2 - run placemnt is done](https://user-images.githubusercontent.com/73480418/97205346-bc2faa00-178d-11eb-892f-d54cb5c86dd6.PNG)


Review placement layout in Magic

![Day 2 - review placement layout in Magic](https://user-images.githubusercontent.com/73480418/97206959-c8b50200-178f-11eb-8056-06d8b19e8901.PNG)


## 4. Day 3: Design library cell using Magic layout and ngSpice characterization
On Day3, SPICE deck creation for an inverter was explained and later, 16-Mask CMOS process was explained step-by-step in detail. OpenLANE offers an interesting feature of making changes into parameters on the go. This helps to deal with issues like congestion. SPICE deck formation contains information’s like components connectivity, component values, Identify ‘nodes’ and name ‘nodes’. It was showed how W/L ratio of MOS impacts its conductivity and hence reason of carefully defining W/L ratio of MOS to ensure same rise and fall delay for clock signals. CMOS robustness defined with the help of parameters that are Switching Threshold (Vm). Switching threshold defined by condition Vin = Vout.


### 16-Mask CMOS Process steps:
  1.	Selecting a substrate.
  2.	Create Active region for transistors.
  3.	Formation of N-Well and P-Well.
  4.	Formation of Gate.
  5.	Lightly Doped Drain (LDD) formation.
  6.	Source and drain Formation.
  7.	Steps to form Contacts and interconnects (local).
  8.	Higher level formation.

**LAB:**

Inverter Layout in Magic

![Day 3 - inverter layout in Magic](https://user-images.githubusercontent.com/73480418/97207054-ebdfb180-178f-11eb-98aa-77fe474c9f5c.png)


Transient analysis of an inverter in ngspice

![Day 3 - trans1 of inverter in ngspice](https://user-images.githubusercontent.com/73480418/97207068-ef733880-178f-11eb-8602-7efbdfac332b.PNG)



## 5. Day 4: Pre-Layout timing analysis and importance of good clock tree
On Day4, Delay tables, setup & hold time concepts were explained. I learned that delay and output transition values of any particular cell are calculated with the help of values of input transition and output load values. Delay table and output transition table of a cell contain different value for each combination of input trans and output load, represented in form of lookup tables in liberty file.

### Setup & Hold Slack Analysis:
Setup and hold time define a window of time in which our data should remain unchanged for desired data transfer to take place. Factors like uncertainty and skew also play an important role in this. Clock skew is the difference between Source Clock path and Destination Clock path. Slack defined as difference between actual time and required time is monitored. Positive or zero Slack indicates no violation whereas negative slack value indicates violation of timing.

### OpenROAD Commands:
read_lef /openLANE_flow/designs/picorv32a/runs/trial/tmp/merged.lef
read_def /openLANE_flow/designs/picorv32a/runs/trial/results/cts/picorv32a.cts.def
write_db pico1.db
read_verilog /openLANE_flow/designs/picorv32a/runs/trial/results/synthesis/picorv32a.synthesis_cts.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
lik_design picorv32a
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock [all_clocks]
report_checks -path_delay min_max -digits 4

**LAB:**

Lab1: Layer information in sky130_fd_sc_hd file



Gird size modified to track size



Process to modify labels



Command to convert Magic layout to .mag




Converting Magic Layout to .mag




Creating LEF file from Magic layout



LEF file created



Layout of the sky130_vdsinv Cell



Layout in expanded view



Lab2: Reduction in the Slack Violation and increase in area




Lab3: Run CTS is completed successfully




Lab4: CTS values



Creating db file




Db file created in OpenLANE_flow directory



Reading SDC file



Slack value MET




Removed clkbuf_1



Inserted clkbuf_1 to the clock buffers



Change in slack due to clkbuf_2 



## 6. Day 5: Final steps for RTL2GDS using TritonRoute and OpenSTA
On Day5, explanation of how routing process done using TritonRoute and explanation of SPEF file extraction in done. Routing method finds out best possible pattern for connection between two end points, of which one point is target node while the other is source node. Maze Routing-Lee's Algorithm was introduced. In this technique firstly routing grids are created and source & target nodes are identified. Then the blocks adjacent to one under consideration are assigned same numbers and this process is repeated till we reach the target node. Once this done the pattern with minimum number of turns preferably a l spaced pattern is finalised for route.

### Typical DRC rules for pair of wires:
   - Wire width
   - Wire pitch
   - Wire Spacing
   
These DRC rules exist because of limitations of the Lithography technique. Deviation takes place in lithography leading to changes which might lead to an unintended open circuit or short circuit and to avoid this the DRC rules are fixed. Another DRC Violation type is Signal short which can be dealt by changing layers.

### Routing Technique Classified into 2 steps:
  - Global Route: Performed using fast route. In this step route guides are formed.
  -	Detailed Route: Performed using TritonRoute.
TritonRoute performs initial detailed route and tries to route within the route guide provided by fast route. It works on MILF-based panel routing with intra-layer parallel route and inter-layer sequential route technique. Input files required for triton route are LEF, DEF and pre-processed route guide. Output is in form of detailed routing with optimum wire length and Via count.

### Steps for pre-processed route guides:
-	Initial route guide
-	Splitting
-	Merging
-	Bridging
-	Pre-processed Route


**LAB:**

Generating PDN

![Day 5 - gen pdn code](https://user-images.githubusercontent.com/73480418/97207226-247f8b00-1790-11eb-9b89-17ee2ebb148e.PNG)


PDN generation completed successfully

![Day 5 - generating PDN](https://user-images.githubusercontent.com/73480418/97207239-28aba880-1790-11eb-9fc7-3b0eedbe6b1d.PNG)


Run routing is completed successfully

![Day 5 - routing completed](https://user-images.githubusercontent.com/73480418/97207243-2ba69900-1790-11eb-810f-004caef39709.PNG)


SPEF extraction (mine got killed)

![Day 5 - SPEF extraction -mine got killed](https://user-images.githubusercontent.com/73480418/97207263-306b4d00-1790-11eb-81a5-2b985bc70ee0.PNG)



## 7. Acknowledgements
-	Kunal Ghosh, Co-founder (VSD Corp. Pvt. Ltd)
