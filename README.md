# VSD_PhysicalDesign_Workshop
This repo is a documentation of the Advanced Physical Design using OpenLANE/Sky130 workshop

## Contents
### Day 1: Inception of open-source EDA, OpenLANE and Sky130 PDK
- How to talk to computers
- SoC design and OpenLANE
- Get familiar to open-source EDA tools

### Day 2: Good floorplan vs bad floorplan and introduction to library cells
- Chip Floor planning and considerations
- Library Binding and Placement
- Cell design and characterization flows
- General timing characterization parameters

### Day 3: Design of a library cell using Magic Layout and ngspice characterization
- Labs for CMOS inverter ngspice simulations
- Inception of Layout and CMOS fabrication process
- Sky130 Tech File Labs

### Day 4: Pre-layout timing analysis and importance of good clock tree
- Timing modelling using delay tables
- Timing analysis with ideal clocks using openSTA
- Clock tree synthesis TritonCTS and signal integrity
- Timing analysis with real clocks using openSTA

### Day 5: Final steps for RTL2GDS
- Routing and design rule check (DRC)
- Power distribution network and routing
- TritonRoute features


# Day-1

## How to talk to computers
### Introduction to QFN-48 Package, chip, pads, core, die and IPs

Take an arduino board, the entire board can be described in terms of a block diagram, with Processor, SD RAM chip, VCC, etc blocks associated with it. Inside the chip, there will be multiple pins available. This chip that is generally referred to should be called as a package with multiple pins. The particular package in the course is QFN-48 
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/129c50d3-def5-4b87-85ef-d24d68ddee2f)

- The chip is generally placed at the centre of the package and wire bonds are usually used to connect them to the package pins
- The chip will have various components:
- - Pads: Through which signals go through
- - Core: Place at which the digital logic sits
- - Die: The size of the entire chip
- If an example of **RISC V** SoC is taken, the SoC will look like
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/62cee403-addc-41c5-81f5-b7c0d3c9fc76)

The PLL, SRAM, etc are called **foundry IPs**, 
- A foundry is basically a huge factory that has machines to manufacture silicon chips
- As a physical design engineer, one must communicate with the foundry on a regular basis

The digital blocks seen are called the **Macros**
- There is a clear distinction between macros and IPs, for simple understanding, one can say that Macros are basic Digital blocks and IPs require some level of intelligence to build and develop.

There are Interface files that the foundry passes to companies/individuals through which one can communicate with the foundry



### Introduction to RISC-V 
#### RISC-V Instruction set architecture (ISA)

- Say a C Program is to be run on a particular layout, to pass the C program, it is first compiled into ALP(depending on the architecture), and then converted into Machine language(binary)
- The RISC-V should be implemented through a particular RTL
- The entire flow starts from RISC-V architecture and then to RTL and then layout

### From software applications to hardware
- Apps run on hardware
- The application software enters into a block called as System software, which inturn converts into binary format that the hardware can understand.
- The major components of system software are:
- - Operating system: handles IO operations, allocates memories, etc.
- - Compiler: Output is according to the instruction set
- - Assembler: output is binary
- The Instructions of the compiler output will depend on the architecture of the Chip(RISC-V, x86, etc): this is an exe file
- The input to the compiler is of a standard format depending on the languages
- From assembler to hardware, we need a HDL (RTL) which implements the specific implementations
- This RTL is synthesised into a netlist, in the form of gates, which is then converted into layout.

## SoC Design and OpenLANE
### Introduction to all components of open-source digital ASIC Design

- Deigning Digital ASICs requires multiple elements, these include:
- - RTL IPs
- - EDA Tools
- - PDK(process design kits) data
- Open source RTL designs are available all over the internet, some sites include librecores.org, opencores.org, and github.com
- Some EDA tools like Spice simulator, magic, etc are open source

**PDK**
In the early days, the design of an IC was tightly integrated with the manufacturing processes available within each company

- Lynn Conway and Carver Mead saw the need for seperating the design from technology, and this is when the Lambda-based rules were born.
- Since then, we have seen Pure Play Fabs( companies that only fabricate) and Fabless companies( those who only design)
- PDK is the interface betwwen the FAB and the designers
- The PDK includes
- - Device models
- - technology information
- - I/O libraries, etc.

- Google took upon an agreement with Skywater and released the first ever open PDK, called the FOSS 130nm skywater PDK.
- The current market share for the 130nm process is 6 percent, which is still a major chunk of the market. The fabrication node for this node is often cheaper due to its maturity.
- ASIC design includes a lot of steps
- The main objective of this flow is to take the RTL Design to the GDSII format, which is finally used by the fabs

#### Simplified RTL to GDSII flow

![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/060475ba-bdad-4637-a578-aaa5b3b0dce8)

- Synthesis
- Power/floor planning
- placement
- clock tree synthesis
- Routing
- Sign off
The first major step in a typical ASIC flow is **Synthesis**
- Synthesis basically converts RTL to a circuit of components from the standard cell library to a gate level netlist
- Generally, the cell layout is enclosed by a fixed height and the width is variable, it is an integer multiple
- Each has different views/models, for example, layout views, SPICE views, electrical views, etc.

**Floor and power planning**
Objective here is to plan the silicon area and obtain the most optimum result possible
- In chip floor planning , the chip die is paritioned between different system building blocks and I/O pads
- In Macro floor planning, the macro dimensions and its pin locations are defined. In this step, the rules and routing tracks are defined
- In power planning, the power network is constructed. Typically, a chip is powered by multiple VDD and gnds. Typically, upper metal layers are used for lower resistance
- In placement, the cells are placed on the floor plan. The cells are placed as close as possible to reduce interconnect delay and for routing purpoes that are seen later
- Placement is done in 2 steps usually
- - Global placement: Tries to find the optimal positions for all cells. - May not be legal positions
- - Detailed Placement: The positions are minimally altered to make them legal
- Before routing the signals, we need to route the clock, ensuring that the clock is delivered to all sequential cells with minimum skew
- The clock network usually looks like a tree with various branches (X-tree, H-tree, etc)
- Next, signal routing to implement the nets. The interconnects are made based on the PDK and the available metal layers
- The skywater pdk has 6 layers. 5 layers are aluminium layers
- Routing grid is huge so, a divide and conquer approach is used. again, we have 2 steps
- - Global routing: To generate the routing guides
- - Detailed routing: uses the routing guides to implement the actual wiring
- For signoff
- - Physical verifications like DRC(Design rule check), LVS( makes sure that the final netlist is matched with the gate level schematic)
- - Timing verification through STA(Static timing analysis)

### Introduction to openLane and STRIVE chipsets
When using openSource EDA tools, the task becomes a bit complicated
- OpenLANE is open source and free
- Openlane is for an open source flow for a true open source tapeout experience
- STRIVE SoCs are from Efabless 
- This family has many memners like
- - Strive, 2, 2a, 3, 5, 6
- The main goal for OpenLANE is to produce a clean GDSII with no human in the loop
- OpenLANE is tuned for the skywater 130nm PDK
- It has 2 modes of operation:
- - Autonomous
- - Interactive
- openLANE has a design space exploaration feature which can be used to find the best set of flow configurations
- It also comes with a large number of design examples

### Introduction to openLANE detailed ASIC Flow design
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/77c8056f-87d9-4458-a2eb-9d0ce3de7e76)

- openLANE is based on several opensource projects such as:
- - OpenROAD
- - MAGIC VLSI
- - QFlow
- - ABC
- - KLayout
- - Yosys
- - Fault
- The Flow starts with RTL synthesis, it is fed to yosys with design constraints, it is converted into logic using components, this circuit can be optimized and mapped into library using abc
- ABC needs to be guided during this process
- this guidance comes in the form of ABC strategies: synthesis strategies are used to find the best strategy to continue with
- openLANE has design exploration which can be used to sweep the design exploarations and it generates the number of violations generated, this is useful to find the best configurations 
- openLANE regression testing is used to test among the best known designs and to compare among the best results
- After synthesis, we have DFT:
- - Scan insertion
- - Automatic test pattern generation
- - Test patterns compaction
- - Fault coverage
- - Fault simulation
- Next comes the **physical implementation** which involves several steps, this is done by the openROAD App,Automated power place and route (PnR)
- - Floor/ Power planning
- - End decoupling capacitors and tap cells insertion
- - Placement: Global and detailed
- - Post placement optimization
- - Clock Tree synthesis
- - Routing: Global and detailed
- Logic equivalance checking can be done using yosys
- Every time the netlist is modified, verification needs to be done
- During physical implementation, antenna rules violations should be addressed
- when a wire segment is fabricated, if it is long enough, it can act as antenna, so the length of transistors is reduced
- It has 2 solutions:
- - Bridging: attaches a higher level intermediary
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/972e7431-37eb-452e-b9b2-9d9ad855f63b)
- - The other solution is to create another antenna diode
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/7551d84d-e6f4-42f6-bac5-d45f1810a81a)

With OpenLANE, a preventive approach has been taken
- a fake antenna diode is created next to every cell input after placement
- This cell is not a real diode but matches the footprint of the library being used
- If the checker reports a violation on cell input pin, replace the fake diode by a real one
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/cfb55a34-d306-4908-98a9-0be406e0b8ac)

One of these 2 approaches can be used in openLANE

**Signoff** in openLANE
STA is done by openSTA
- physical signoff includes DRC and LVS
- Magic is used for DRC and spice extraction from layout
- Magic and Netgen are used for LVS
- extracted spice by magic vs verilog netlist

## Getting started with Labs

## OpenLANE directory structure

- OpenLANE is a flow which consists of many opensource tools
- the basic aim is to have a complete RTL2GDSII flow

The working directory is 
```
~/Desktop/work/tools/openlane_working_dir/openlane
```
The contents are:
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/9c272410-3028-4f38-8554-3a16f254b8f6)

Under sky130A,
libs.tech contains files specific to the tool
Under libs.ref and libs.tech, we have
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/58af08ad-346b-4201-b015-d0bfaddd3c56)

sky130_fd_sc_hd means that
- sky130: the name
- fd : foundry name, for example, OSU is for oklahoma state university
- sc: standard cell
- hd: variant of PDK, hd is high density
Inside the directory, we can see various files present associated with that folder
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/0ede5112-811b-4a9e-9d24-87a4700661bc)

### Design preparation steps
```
docker
./flow.tcl -interactive
```
runs the script in an interactive mode, without the interactive command, it will run completely from RTL to GDSII
Opening involves running the docker command and executing the tcl script as shown
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/1a2be6a9-2984-4a1b-ae70-451e5dcb661a)
All the designs being run by openlane are extracted from the designs folder. First, we will be doing for picorv32a
It will have 3 files,
- src file: RTL will be present and sdc file
- config.tcl - bypasses any configs already done into openLANE., i.e, many of the switches already have a default value. The below image shows the default values
```
less config.tcl
```
results in the below image
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/afa049d3-822f-408f-9b07-82cd85ba219e)
config.tcl will have the highest priority

Before running synthesis, we need to prepare the datastructure and the file system
Upon running the command,
```
prep -design picorv32a
```
The 2 LEFS are merged, techlef and lef files. 
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/f11db0e9-4df1-4ca1-9c08-acbd4ab6b0a7)
A runs directory is created under the picorv32a folder and this will have all the necessary files
- The LEF file inside this directory will have all the cell, layer, etc information
- the results folder will have seperate folders for each step
The config.tcl basically shows all the default parameters being taken by the run
- cmds.log file records all the commands

```
run_synthesis
```
runs the **synthesis**
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/6aa35c0d-3ca7-42b8-9c03-cbb33f2553f6)
To find the **FLOP RATIO** i.e, number of D-Flip Flops:
```
Number of cells: 14876
Number of D FF: 1613
Flop count (Number of D-FF/ Number of cells) : 0.10842968
Flop count percentage: 10.8429
```
To check the reports:
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/264419d3-418a-47d5-b5c0-ec8d26ce6c51)

For the actual synthesis statistical report:
```
yosys_4.stat.rpt
```
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/4a8c31be-70d7-4077-87da-f5139a65a4e4)


# Day-2
## Floorplanning and introduction to library cells
### Utilization factor and aspect ratio

In the physical design flow, the first step is to define the height and width of the core and the die
- We start with a netlist, say 2 ff and a combinational logic in between(for example)
- A netlsit basically describes the connectivity between all the components
- we are mostly interested in  the dimensions of the standard cells, wires are not considered as of now
- say standard cells are given a dimension on 1x1 unit, assume the same area for the ff as well
- With the help of these dimensions, area on the silicon can be identified
- Total length and width would be 2 x 2 = 4 sq units(area), this is the minimum area that would be occupied
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/b7135f6e-70c5-412e-9c15-2f9ea77ee5f3)

- On the silicon wafer, one section is referred to as the die and inside the die, we have the core
- The die encapsulates the core, a die is a small semiconductor material specimen on which the fundamental circuit is fabricated.
- If the netlist occupies the entire coore --> 100% utilization, 
```
Utilization factor = Area occupied by the netlist/Total area of the core
```
- When utilization factor is one, the core is completely occupied, ideally we go for 50-60% utilization

```
aspect ratio = height/width
```
- When aspect ratio is 1, it signifies that the core is of a square shape

### Concept of pre placed cells

- Take a combinational logic, we needn't implement the logic everytime completely, the circuit can be divided into smaller bits
- The individual blocks are later connected via wires
- The IO pins are extended and the boxes are detached, this creates 2 different modules or IPs
- The advantage of doing this is that if a particular block is being replicated multiple times, this black-boxed blocks can be implemented and then connected
- Implementing it once is enough, multiple implementations are not needed
- There are IPs available in the market such as, Memory, clock-gating cell, comparators, etc.
- The arrangement of these IPs in a chip is called **Floorplanning**
- Automated placement and routning tools places the remaining logical cells in the design onto chip

### De-coupling capacitors

Consider we have the pre placed cells: block a,b and c which are memories
- These are only implemented once and used multiple times
- The locations of these pre placed cells cannot be moved once placed.
- Preplaced cells should be surrounded with decoupling capacitors

When a particular circuit switches, it needs a current demand, basically at the switching logic, we can say that there is an amount of capacitance there , and when closed, it needs an amount of charge, which it demands from the supply voltage. The supply logic should be adequate to supply enough current to all the gates. Similarly, it is the responsibilty of the Vss to take the charge when the gates switch from logic 1 to 0.

- In reality, there is a drop in the wire due to resistances, inductances, etc..
- If the voltage drop is too huge, proper logic 1 and logic 0 cannot be defined and will lead to errors
- This is the problem of having a large physical distance
- this problem can be solved by using decoupling capacitos: they are large capacitors, completely filled with charge
- The capacitor essentially de couples the circuit from the power supply
- Whenever there is switching activity, the capacitor slowly depletes its charge and when no switching takes place, then the capacitor replenishes itself.
- The preplaced cells are placed with Decaps to ensure no problems occur
- Local communication(inside the preplaced cells) is taken care of but global planning is to be taken care of next.

### Power planning

When connecting from one preplace cell to the other, the power  supply needs to connect and drive the signal as using decoupling capacitors always is not possible. A voltage drop will most likely happen.
- Ground bounce is a phenomenon when all the capacitances discharge at the same time, if this bump exceeds the noise margin level, it can lead to an undefined logic value.
- When all the capacitances want to charge, they demand current at the same time and therefore there is a voltage droop, it should also not exceed the Noise margin level
- The problem in the example is that the power supply is only at one position and from a single source
- distributing this power source and having multiple power supplies may solve the above mentioned problems
- So, multiple Vdd and Vss lines are placed inside the core.

### Pin placement and logical cell placement blockage
- Take the following example
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/2ff75627-e980-4ffb-a35b-3c0fc869847d)
The grey boxes are pre placed cells that are defined before
and
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/39610fae-decd-42ee-957d-e52a944abc20)

- Now looking into the complete deisgn, the 2 sections are merged together, we have 2 clocks, clk1 and clk2
- All this connectivity is defined in HDL
- This is basically a netlist, connectivity information among different circuits
- Usually, all the i/p ports are kept on the LHS, and o/p ports are kept on the RHS
- The ordering of the input and output words are random, this depends upon where we want to place the cells
- The clock ports are larger than the other ports, because the clock ports drive the circuit continously, so we need low resistance, so the larger IO areas.
- Next, a logical cell placement blockage is performed, so that the automatic tool doesn't put items on the IO area.

### Steps to run floorplan using openLANE

After synthesis, we have floorplanning
The standard cell placements happen in placemenet stage, it does not happen in floorplanning
- To view the configuration variables and their default values
```
~/Desktop/work/tools/openlane_working_dir/openlane/configuration
less README.md
```
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/9a986825-4d93-4325-bcd5-a648c50e6396)
after scrolling down, in floorplanning, there are multiple switches that can be seen and the utilization, aspect ratio, etc can be seen(Defaults)
- the design is preferrably initial spread, because when we do the PnR flow, congestion analysis is carried out first, post that we want the timing constraints to be met.
- These switches are set in .tcl files
- for example, for the floor plan stage
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/71652f4e-865c-4b43-bd69-dad948783ec5)

- pin mode 1: random poition, but equidistant
- pin mode 0: not equidistant
- The highest priority for these defaults will be **sky130A_sky130_fd_sc_hd_config.tcl**, and then config.tcl, and then the tcl file inside the folder as shown above
- In openlane flow, verical metal and horizontal metal values are 1 more than the normal ones

The command to run floorplan is
```
run floorplan
```
- the warnings neednt be taken seriously
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/309a2b45-b1de-4623-9d1c-c931001000e9)

### Review floorplan files and steps to view floorplan

