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

Take an arduino board, the entire board can be described in terms of a block diagram, with Processor, SD RAM chip, VCC, etc blocks associated with it. Inside the chip, there will be multiple pins available. This chip that is generally referred to should be called as a package with multiple pins. The particular package in the course is QFN-48 ![image](https://github.com/HarshaPraneeth8/VSD_PhysicalDesign_Workshop/assets/72025415/129c50d3-def5-4b87-85ef-d24d68ddee2f)

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

#### Introduction to OpenLANE and STRIVE Chipsets
