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

The **config.tcl** file will have all the default settings.
Highest priority is for the sky130A_sky130_fd_sc_hd_config.tcl
To look at the floor plan
```
cd results/floorplan ( this is from inside runs)
```
def file is present there = Design exchange format
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/5cbd8c78-595c-4c79-ac05-cb427fed1581)
- microns 1000: 1 micron = 1000 database units
- The first lines contain the die specifications, first 0 is the lower left x value and the second 0 is the lower right x value
- To see the actual layout, we use magic

### Review floor plan layout in magic

```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```
This command considers the merged lef file that we have seen previously and the tech file.

- To align the layout to the centre, press **s** to select the entire layout and press **v** to align it to the centre.
- To zoom into a particular portion of the layout, do a left mouse click and a right mouse click to form a box, press **z** to zoom in
- We have set the input and output pin mode as 1, which makes them equidistant, it can be observed as shown below:
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/50caa3a0-8e39-42c7-8062-e8de88563409)
- Move onto an object and press **s**, open the tkcon window and type **what**, it will show the layer
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/bf01a3dd-b9f7-4081-a5d9-67b59ad3f519)
The below are the tapcells
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/97afe323-3d14-41e2-b86b-a7a4404f8511)

Tapcells are meant to avoid the latchup conditions which occur in CMOS devices, they connect nwell to vdd and substrate to ground. These tapcells are diagonally equidistant, this value has also been set.
The bottom left of the floorplan will have the standard cells
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/4f8d0d37-753d-4a6e-9c0e-aa516164291e)

## Library binding and initial place design

### Netlist binding and initial place design
This is the Placement and routing stage of the physical design flow.
Say, we have a particular netlist with some gates, the shape of these gates will represent the functionality of these gates
- In reality, we dont have shapes, we have boxes, it has physical dimensions
- A library will contain all the information of these boxes a.k.a cells, this can be subclassified into sublibraries
- It will have delay information, conditions, width and height conditions, when conditions, shapes of the gates, etc.
- A library has basically got flavours of each and every cell
- Next, we have placement, to take those particular shapes and sizes and to place them on the floorplan
- Till now, we have the following:
- - Floorplan: it inclues all the IO pins 
- - Netlist: all the gates
- - Physical view of logic gates
- The netlist is to be placed onto the floorplan, we take the physical view of logic gates and place them
- The cells are placed near to its IO pins so that delays are avoided

### Optimize placement using estimated wire length and capacitance
an example placement is as shown below:

![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/9fba1d70-52c6-4a06-95d4-8a8338f8181c)

- For the last ff, it becomes difficult due to restrictions
- The problems discussed such as only some blocks being closer to their ports, can be addresssed, this is called optimizing placement
- We take some estimations, say ff1 to din2, we try to estimate the capacitances in the wires that would be connected before actually routing
- Basically optimize placement is the step when the wire length, and capacitances are estimated, and based on that repeaters are inserted.
- This is done mainly to maintain signal integrity. Repeaters are buffers that will recondition the original signal
- But due to these repeaters, we will have a loss of area
- slew is based on the value of the capacitor, the higher the capacitance, the higher is the amount of charge required to charge the capacitor,and slew will be bad.
- Based on this, we estimate the signal integrity and if distances are reasonable enough --> this is done for each box

### Final placement optimization

- after the placement optimization, we need to do a timing analysis.
- The buffers are placed accordingly wherever required to maintain signal integrity
- Based on the setup timing analysis, the placement done is verified and checked.


### Need for libraries and characterization

- Logical synthesis: output is an arrangement of gates that describe the original circuit.
- Floorplanning:     netlist out of logic synthesis based on gates of the before step (shapes, types and sizes).
- Placement:         the logic cells are placed on the chip such that the initial timing is met.
- Clock tree synthesis: for even spread of clock among all the logic cells
- Routing stage:    
- Static timing analysis: to see the setup time, hold time , maximum available frequency: this is the signoff step
The one thing commona across all these steps are the LOGIC GATES
This collection of gates is referred to as the library, these should be represented in a manner such that the EDA tool understands what the gates are.

### Congestion aware placement using RePLAce

Placement occurs in 2 stages: global and detailed
- global placement is basically port placement and there is no legalization happening here
- Legalization happens in detailed placements

The standard cells are placed in rows, they have to be exactly inside the rows and no overlaps should be there, this is LEGALIZATION

```
run_placement
```

- Global placement happens first: The main objective in this step is to reduce wire length, in openLANE, there is a concept of HPWL (hald parameter wire length)
- When the run_placement is run, the placement analysis can be seen as below:
 ![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/95218ed3-7862-436f-802a-20a346002790)

Placement is where the standard cell positions are fixed, to visualize these changes, we go into 
```
Directory: results/placement/
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
````
All the standard cells that we initially saw in the lower left corner of the floorplan can now be seen as below:
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/3dc88bf0-de98-42cf-8dfe-f07f45aad0bc)
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/c586a10e-ca3e-4778-ac8e-080e3d7ec092)

The second image is a zoomed in version of the standard cells.
- The power-ground network creates the power distribution network
- **But in OpenLANE, this does not happen during placement**
- A post floorplan and post placement CTS, we do power ground generation just before the routing.


## Cell design and characterization flows
In a typical IC design flow, the buffers, logic gates, etc. are called standard cells
- Standard cell is being placed in a place called the library
- One of the sections of the library is to keep the standard cells
- Library has got different gates for different functionality
- It also has cells with different sizes, i.e different sized logic gates for example.
- These sizes are referred to as drive strengths
- Using these different sized cells will directly have a change in the characteristics

If we take a particular inverter, the inv should be represented in terms of its shape, timing behaviour, power, etc. A cell as small of an inverter has to go to the steps of the cell design flow:
- Inputs: Process design kits (PDKs), they include DRC & LVS rules, SPICE models, library and user-defined specs
- - basically a tech file with some rules
- - say rules for poly width, minimum extension rules, etc.
- - There are thousands of rules that are present and need to be followed.
- - For the SPICE models, the threshold voltage equations are seen, they have some parameters called foundry parameters, also called SPICE model parameters

### Circuit design step
 - The separation between the power rail and the ground rail will decide the cell height
 - It is the responsibility of the library developer to maintain this cell height
 - The top level designer deicded on the supply voltage
 - The noise margin levels should also be taken care of
 - If there is a specification that cerain libraries must be built on a particular layer, these are user-defined specifications
 - library designer should decide all the pin functions and locations

Based on these inputs, a library cell is to be developed by the libary developer

The design steps are three:
- Circuit design: 
- - implement the function
- - model the pmos and nmos to meet the library specifications( for example, w/l ratios)
- - Typical output is CDL: circuit description language
- Layout design:
- - Implement the function using MOS transistors
- - to get the PMOS and NMOS network graphs from the implemented design
- Characterization

The circuit design step is mostly based on SPICE simulations.

### Layout design

The first step is to implement the function itself.
- Next, PMOS and NMOS network graphs are to be derived
- Generally, the path of layout is Euler's path and stick diagram

Euler's path: Path that is being traced only once. Based on the Euler's path, a stick diagram is drawn
- The next step is to convert this stick diagram into a proper layout, by also following the DRC rules and the top level user specifications.
- The layout can be drawn using an open source tool called MAGIC 
- The final step is to extract the parasitics from the layout and characterize it interms of timing
- GDSII, LEF , extracted spice netlist(.cir) are the outputs
- cir file: parasitics of each and every element
- The next step is characterization to obtain the timing, noise and power information: output is **.libs** file

### Typical characterization flow

- Input stage
- Design steps
- output

From the input stage, the output is a layout, description of the entire circuit, SPICE extracted netlist, subcircuit(has nmos and pmos models)
- Read in the model: This comes out of the foundry
- Read the extradcted SPICE netlist
- to recognise the behaviour of the circuit
- To read the subcircuit of the inverter
- Attach the necessary power sources
- Apply the stimulus
- Provide the necessary output capacitances
- Provide the necessary simulation commands (trans simulation?DC simulation..etc)

Next step is to feed in all these inputs from the above steps in the form of a characterization software called as GUNA. The software will generate Timing, noise, power.libs files

## General timing characterization parameters
The syntax is understood before, this is important to understand the GUNA software
- Timing threshold definitions:
- - Slew_low_rise_thr (threshold) : low means near the 0 level, defines the point towards the lower side of the power side, typically 20%
- - slew_high_rise_thr
- - slew_low_fall_thr
- - slew_highfall_thr
- - in_rise_thr: related to input waveforms 
- - in_fall_thr
- - out_rise_thr: related to output : point at which delay can be calculated on the output waveform
- - out_fall_thr

## Propagation delay and transition time

- for delay, out-in needs to be calculated,
```
time(out_*_thr) - time(in_*_thr)
```
- If there is a movement of the threshold, then this creates problems, we see the output coming before the input, creating a negative delay, which is not possible.
- This is due to choosing the wrong threshold points
- If the waveform has high wire delays, then we can have timing problems even after choosing the correct threshold positions

**Transition time**
For a rise wavform, 
```
time(slew_high_rise_thr) - timme(slew_low_rise_thr)
```

For a falling waveform,
```
time(slew_high_fall_thr) - time(slew_low_fall_thr)
```

# Day-3

## Design library cell using Magic layout and ngspice characterization

### Labs for CMOS inverter ngspice simulations
### IO Placer revision
From a github link, we will be downloading a .magic file, from it, we will be doing post-layout simulation in ngpsice and post-characterizing  by plugging it into the openLANE flow

- There is an option to make changes on the fly in the openLANE flow
= after running synthesis and floorplan, say we want to change the config of how the IO pins are placed
= IO placer allows 4 strategies
```
set ::env(FP_IO_MODE) 2
run_floorplan
```
as shown below, the IO positions are stacked
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/3a184421-e68e-4d4f-b75e-f1c40276bbc8)

### SPCIE deck creation for CMOS Inverter
SPICE deck is a connectivity information about the netlist, it has inputs, connectivity information, tap points
- The connectivity of the substrate pin should also be defined
- The load capacitor will depend on the input capacitors of the circuit
- Next, we have to define the component values, in the example taken, thw (W/L) ratios are: 
- - PMOS: 0.375/0.25 u
- - NMOS: 0/375/0.25 u
- The PMOS Should be generally wider than the NMOS, we will be doing this later
- Ideally, twice or thrice larger than the NMOS
- The output load calculated here is 10fF
- Input gate voltage: 2.5v and drain voltage: 2.5v
- The next step would be to identify the nodes, to say a component lies betweeen 2 places, we define nodes, the schematic of the inverter being considered is shown below:
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/41fa7a0c-61fb-485d-90c4-b439f843de06)
The SPICE netlist can be written as

### SPICE Simulation lab for CMOS Inverter
For MOSFET: **Drain-gate-source-substrate**
```SPICE
M1 out in vdd vdd pmos W=0.375u L=0.25u
M2 out in 0 0 nmos W=0.375u L=0.25u
cload out 0 10f
Vdd vdd 0 2,5
Vin in 0 2.5
*** Simulation commands ***
.op
.dc Vin 0 2.5 0.05

*** include tsmc_025um_model.mod ***
.LIB "tsmc_025um_model.mod" CMOS_MODELS
.end
```
- All the tech parameters are located in the MODEL file
- here, W/L of both NMOS and PMOS = 1.5

### Lab setup steps
- We need to clone the git repo at first
```
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```
we copy the tech file into the folder here
```
cd pdks/sky130A/libs.tech/magic/
cp sky130A.tech pwd
```
Here clone into the pwd from the source
In the pwd, the following command invokes magic
```
magic -T sky130A.tech sky130_inv.mag &
```

### 16-MASK CMOS Process
The steps are:
- Selecting a substrate: generally, P-type substrate
- Creating active region for transistors: Mask1
- - The field oxide is grown, this process is called LOCOS ( Local Oxidation of silicon): Bird's beak
- N-well and P-well formation which will be used for PMOS and NMOS fabrication respectively
- Formation of gate
- Lightly doped drain (LDD) formation
- Source and drain formation
- Formation of contacts and interconnects
- Higher metal level formation

### Lab introduction to Sky130 basic layers layout and LEF using inverter
- the left pane has all the layer information
- move the cursor across the poly-ndiff intersection and press s, this will select only to that area and type **what** on the tkcon window, this will tell the information
- When s is pressed twice, the connected is also selected

![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/24adda24-32d8-402b-9ddc-8465789f254b)

- DRC in magic is an interactive DRC that can be accessed from the top bar
- To extract SPICE, in the tkcon window
- - Create an ext file
- - convert to SPICE
```
extract all 
ext2spice cthresh 0 rthresh 0
ext2spice
```
- The below is the final SPICE netlist
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/7f7f2176-c643-4fde-81a2-6790cee4a985)

![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/63c729c0-c68f-4d02-8958-4941c58efb34)
```
plot y vs time a
```
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/2f744f93-7355-4864-bb9d-572714ebef55)
- Characterizing a cell is done by obtaining 4 parameters"
- - Rise tranisition: Time taken for output waveform to transit from a value of 20 percent of max value to 80 percent of max value
- - Fall transition: Time taken for output to fall from 80 percent to 20 percent
- - Fall cell delay
- - Rise cell delay

**Rise tranisiion**
- Max value: 3.3V, 20 percent is 0.66
```
at 0.6 : x0 = 2.17941e-09, y0 = 0.66
at 2.66: x0 = 2.23861e-09, y0 = 2.64075
Rise transition = x0 diff = 0.0592ns
```
- For fal tranistion, 80 percent to 20 percent
```
at 2.66: x0 = 2.12e-09, y0 = 2.63981
at 0.6 : x0 = 2.18e-09, y0 = 0.66
Fall tranisition: 0.06ns
```
- For propagation delay,
```
at 1.65:
x0 = 2.20667e-09, y0 = 1.65047
x0 = 2.14989e-09, y0 = 1.65047
cell rise delay(propagation delay): 0.05678ns

x0 = 4.07487e-09, y0 = 1.65008
x0 = 4.0749e-09, y0 = 1.65016
Cell fall delay: 0.0038ns
```

- Next objective is to use the layout and create a lef file, this will be used in openLANE and it is plugged into the picorv32 core

# Day-4
## Timing modelling using delay tables
### Lab steps to convert grid info to track info

- For place and route, the only information required is :
- - Power and ground rail info
- - PR boundary
- - Input and output
- LEF file has all this information
- Next obj is to extract a lef file from this mag file, this lef file can be inserted into the picorv32 core
- One of the gudelines to be followed, is that
- - Input and output ports must lie on the intersection of the horizontal and vertical tracks
- - width of the standard cell should be a odd multiple of track pitch and height should be an odd multiple of the track vertical pitch
- Track info can be seen in this directory:
```
/home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd
```
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/cd303fe8-4c90-4b88-934d-1c846344e87f)
- Tracks are used in the routing stage, routes can go over the tracks
- routes are metal traces basically
- We need to specify where we have specified route to go, this specification is given by this file
- For the li1 layer, horizontal point is 0.23 with an X pitch of 0.46
- Every metal layer has a direction
- Ports are in li1 metal layer, we need to make sure that this is present in the intersection
- From the tracks file, in the tkcon window,

```
grid 0.46um 0.34um 0.23um 0.17um
```
- This is basically converting the grid def to track definition, routing of li1 layer can only happen along this layer, now we need to check if io ports are at the intersection of the tracks, this ensures that the route can reach the port from the y as well as the x direction
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/f3ec0bf2-8610-4371-989e-f875aaeaa41d)

### Mag file to lef cell

- Width of std cell should be odd multiples of x-pitch, this can be checked using the grid drawn from the previous step
- the height of the std cell is also to be of the same condition
- the ports are defined as pins when the lef file is extracted
- 
- X-pitch is 0.46
- Use the below commands to set the ports
```
port class input
port class inout
port use signal
port use ground
port use power
```
- Now we extract the lef file.
```
lef write
```
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/1f3ac7e3-ea69-4b39-8c1e-fc77e3b83064)

- The pins are created as shown when we use the commands in magic to create ports, this is done because the PnR pin requires this information
- Next step is to plug in this lef file to the picorv32 core
- We need to move the files in the src folder
- The first stage now would be synthesis, so we need to have the library that has the cell designs, the library is already present in the csdstdcelldesign
- The following modifications should be done to the config.tcl file inside the picorv32a core
```tcl
# Design
set ::env(DESIGN_NAME) "picorv32a"

set ::env(VERILOG_FILES) "./designs/picorv32a/src/picorv32a.v"
set ::env(SDC_FILE) "./designs/picorv32a/src/picorv32a.sdc"

set ::env(CLOCK_PERIOD) "12.000"
set ::env(CLOCK_PORT) "clk"


set ::env(CLOCK_NET) $::env(CLOCK_PORT)

set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]



set filename $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/$::env(PDK)_$::env(STD_CELL_LIBRARY)_config.tcl
if { [file exists $filename] == 1} {
        source $filename

```
- After opening openlane, we normally do prep -design picorv32a, for the work to continue in the same directory,
```
prep -design picorv32a -tag 04-06_06-15 -overwrite
```
Next we paste the following after the above is run:
```
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs 
```
Now we run synthesis and check for the mapping, this happens from the change in config.tcl file
```
run_synthesis
```

![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/8979d3b8-14cf-476f-bafb-f6d8c513302b)

- TNS is total negative slack
- wns is worst negative slack
- These parameters can be improved by changing the area and delay requirements/configs of the placement stage, so to do them, the following commands are used:

```tcl
set ::env(SYNTH_STRATEGY) "DELAY 0"
set ::env(SYNTH_SIZING) 1

set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

run_synthesis

```
For running floorplan, the below following commands are used:
```
init_floorplan
place_io
global_placement_or
tap_decap_or
```
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/7b542b8e-5549-42f6-8849-cf96dbedcda5)

Now, to run placement,
```
detailed_placement
```
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/52f57a51-50e6-4b3e-8de0-83e368ebf452)
Now, we open the layout after placement, this is done by going into the results/placement directory and then using the below command to open magic
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def 
```
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/69dc7b63-e938-4e73-8707-a321101301ed)
- After expanding using :expand on tkcon window, we can visualize the alignment,
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/b9c71243-7f62-404a-8f79-3684d80d0619)

### Setup timing analysis
- say, we have a laucnch flop, capture flop and a combinational logic in between, and an ideal clock connected.
- In basic Setup timing analysis, one edge of the clk is sent to the launch flop and the other edge to the capture flop
- Assume the combinational logic has a delay of **x**
- The combinational delay should be less than the time period 
```
x < T
```
- if time period is increased, then the frequency decreases which is a drawback
- Now, we introduce practical conditions, take a particular flop, which has mux(s) inside it, this induces a delay 
- The changes happen when the clock switches from logic 1 to logic 0
- The Mux delay will affect the combinational delay requirement, the initial ideal conditions will now change
- say, initially, if a complete time period T is available for the combinational logic, now it has less than that
- this Mux delay induced is known as **setup time**
- Now the equation chaneges to
```
x < (T - S)
```
- The clock is created using PLL, the clock circuitry is again based on some real circuitry, this clock source might have its own inbuilt variations
- Even though the variation is minimal, it is still present, and is called jitter
- after all the considerations, the combinational delay x becomes
```
x < (T-S-SU)
```
### Lab steps to configure OpenSTA for post-synth timing analysis
- usually, if there are timing violations, seperate tools are used, in this case, that tool is OpenSTA

- The buffers can be removed or changed and the openSTA can be used to change the values to meet slack, since slack is met here, we move forward

```
run_cts
``` 
- the above command is used to run clock tree synthesis
- now, we run cts using
```
run_cts
```
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/7893191c-b006-4452-be8b-09a896e75ee0)

Since all conditions are met, we move onto routing

### Final steps for RTL2GDS using tritonRoute and openSTA
### Lab steps to build power distribution command

- For power and ground rails, we run the below command,
```
gen_pdn
```
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/47229e0b-6395-46b1-82f7-14d0c7ff865f)

- Here, the standard cell rows are placed at 2.72, this is the height of the standard cells
- for inv cell to get vdd and gnd, height of standard cell should be the same as 2.72 or in the multiples of 2.72
- From the pads, power goes to the straps and from there it goes down to the standard cell rails
- The green boxes can be assumed to be the picor
- The red pad is the power pad and the blue pad is the gnd pad,
- From the pads, the power is supplied to the ring
- The vertical straps are present to get the power inside the chip
- For the power to be supplied to the standard cells, rails are used
- Straps are in metal4 and 5 as shown below

![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/60f5b4a8-5590-42a2-bbd0-1ccccbed15dd)
By running the 
```
echo $::env(CURRENT_DEF)
```
we can see that the def file has changed from cts def to pdn def
![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/bcbdcff9-c5ad-4a4b-a1f6-446cde50fa66)

### From power straps to standard cell power
- TritonRoute is the engine used for routing, the configurations of routing can be seen under configuration/README.md
- 
