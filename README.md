# VSD_PhysicalDesign_Workshop
This repo is a documentation of the Advanced Physical Design using OpenLANE/Sky130 workshop

## Contents
### 1. Day 1: Inception of open-source EDA, OpenLANE and Sky130 PDK
- How to talk to computers
- SoC design and OpenLANE
- Get familiar to open-source EDA tools

## Day 2: Good floorplan vs bad floorplan and introduction to library cells
- Chip Floor planning and considerations
- Library Binding and Placement
- Cell design and characterization flows
- General timing characterization parameters

## Day 3: Design of a library cell using Magic Layout and ngspice characterization
- Labs for CMOS inverter ngspice simulations
- Inception of Layout and CMOS fabrication process
- Sky130 Tech File Labs

## Day 4: Pre-layout timing analysis and importance of good clock tree
- Timing modelling using delay tables
- Timing analysis with ideal clocks using openSTA
- Clock tree synthesis TritonCTS and signal integrity
- Timing analysis with real clocks using openSTA

## Day 5: Final steps for RTL2GDS
- Routing and design rule check (DRC)
- Power distribution network and routing
- TritonRoute features


# Day-1

## How to talk to computers
### Introduction to QFN-48 Package, chip, pads, core, die and IPs

Take an arduino board, the entire board can be described in terms of a block diagram, with Processor, SD RAM chip, VCC, etc blocks associated with it. Inside the chip, there will be multiple pins available. This chip that is generally referred to should be called as a package with multiple pins.
