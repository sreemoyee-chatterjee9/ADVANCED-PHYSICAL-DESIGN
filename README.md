# ADVANCED-PHYSICAL-DESIGN
Here, all the learnings during the workshop : [ADVANCED PHYSICAL DESIGN USING OPENLANE/SKY130](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/) is documented with the examples and the experiments. Essentially, this workshop document was made concentrating on the daily activities that has been explored during RTL2GDS end to end flow with the open-source flow: OpenLane. Core design utilized duirng this process is PICORV32A RISC-V.


# Introduction
- [Introduction](#introduction)
- [Day 1 - Inception of open-source EDA, OpenLANE and Sky130 PDK](#day-1---inception-of-open-source-eda--openlane-and-sky130-pdk)
- [Day 2 - Good floorplan vs bad floorplan and introduction to library cells](#day-2---good-floorplan-vs-bad-floorplan-and-introduction-to-library-cells)

## Day 1 - Inception of open-source EDA, OpenLANE and Sky130 PDK
FPGA, an acronym for Field Programmable Gate Array, is an integrated circuit (IC) that is built with a large number of logical processing resources, or logic blocks. These logic blocks consist of digital logic components such as multiplexers, flip-flops, lookup tables, and adders. We can divide the FPGA in three main parts: *
* <pre>Configurable Logic Blocks  — To implement logic functions.</pre>
* <pre>Programmable Interconnects — To implement routing.</pre>
* <pre>Programmable I/O Blocks    — To connect with external components.</pre>

We are focusing on the implementation of the logic functions, which comes within the Processor/SoC.
![image](https://user-images.githubusercontent.com/123591219/214771306-559acf6d-4dcb-4743-a274-a571e5870d68.png)

**Chip Package:**

Packages are empty vessels into which the bare chip is placed. Tiny wires connect the leads on the package with the leads on the chip. Pin Location of the package are driven by ARUDINO Board that we are trying to design. The Chip at the center of the package. All the input signals coming from the outside will be trasferred to the chip via the wire connection. Following is the example of QFN 48 PIN Package \[7mm X 7mm\]:
![image](https://user-images.githubusercontent.com/123591219/214792447-edf8fe49-895e-45d7-8d79-127c49dc1b0f.png)

The Chip sitting inside the package consists of various components.
* PADS : Through pads, the signals can be sent inside/outside of the chip. They are intermediate structures connecting internal signals from the core of the integrated circuit to the external pins of the chip package.
* DIE  : Die is the complete chip which gets manufactured on the silicon wafer. It is the square of silicon containing an integrated circuit that has been cut out of the wafer, on which the given functional circuit is fabricated.
* Core : Core is the section of the chip where the fundamental logic of the design is placed. A Core typically consists of following components - SoC, SRAM, ADC/DAC, PLL, SPI.

Foundry IPs : 
IP = Intellectual Property
All the devices are dependent on Foundry. It is a place where the Chip actually get manufactured. Generally, fabrication industries(foundry) provide IPs such as standard cells, IO blocks, etc.. PLL, SRAM, ACD/DAC are some examples of foundry IPs. We communicate with IP with the interface files provided.

MACROS :
RISCV SOC, SPI these are the digital blocks which are called Macros.
![image](https://user-images.githubusercontent.com/123591219/214797116-7103d9d9-03f7-492a-b751-bbb51c848ada.png)


RISCV Instruction Set Architechture (ISA) :

ISA serves as the boundary between software and hardware. If we want to run a program on the hardware i.e. the layout of the chip then we need to go through a flow to parse the information.

Step 1 : The program gets converted to a assembly language \[Hexa-decimal\].

Step 2 : The Assembly language program gets converted into machine language program \[Binary Format\].

Step 3 : Machine Language program gets executed on the Hardware \[Layout\].

Interface : RISCV ISA needs to be implemented in RTL. Then the RTL2GDS Flow gets executed to run the required program on Layout.
![image](https://user-images.githubusercontent.com/123591219/214822197-b91269ed-1bed-4c28-92a2-3cecbc6d4de1.png)

Software Application Execution on Hardware \[Layout\]:
![image](https://user-images.githubusercontent.com/123591219/214851823-2a65ea02-ccc5-4b87-a455-6c1bd71dd460.png)

Instructions that come from the compiler is called abstract interface, which performs its job as interface between the software programming language and hardware. These set of instructiions are called "Instruction Set of Architechture"/"Architechture of Computer". 
 
<pre>Example : add x6, x10, x6
After conversion to Binary ------> 00000000011001010000001100110011 </pre>

RTL Implementations : It is needed to understand the hardware specific instructions. Then that RTL instruction gets synthesized into netlist which contains the Gates and other logic components. Then Viw physic design implementation, the instruction input gets implemented into hardware.
![image](https://user-images.githubusercontent.com/123591219/214855726-eb9770e6-dcfe-4c56-a0f0-4d27f2f4ca82.png)


SoC Design Using OpenLANE
For Digital ASIC Design, we need following components:
RTL IPs : Hardware Description Language of the fuctions
EDA Tools/CAD Tools : Use for electronic design automation
PDK Data : Process Design Kits. It is the interface between the FAB and the Designers. PDK contains the files of Process Design Rules, Digital Standard Cell Libraries. I/O Libraries.

Following are some open sources for Digital ASIC Design:

![image](https://user-images.githubusercontent.com/123591219/214861680-5ff9002a-e4eb-452f-b5a3-f0ac29b6fa22.png)

Objective of ASIC Design : To take the design from register transfer level and implement it to GDSII which is the format used to final layout. This process is also known as Automated PnR/ Physical Implementation. The Steps of this Flow is as following:
![image](https://user-images.githubusercontent.com/123591219/214866786-07e5e5c0-ef55-4ae4-a819-84c060a6f871.png)

1. Synthesis : Design needs to be translated into circuits made of components from Standerd Cell Library. The result of synthesis is described in HDL which known as Gate Level Netlist, equivalent to RTL. 

![image](https://user-images.githubusercontent.com/123591219/214868418-9ca331f5-7021-4ea9-9133-631774f72d09.png)

2. Floor and Power Planning : Obective of floor and power planning is to plan the silicon area and create robus power distribution. In Floor-planning, the chip-dir is partitioned between dfferent syntem building blocks and place the I/O Pads. Macro-dimensions, pin and pad locations are defined during this stage. Reduction of resistance and IR Drop is dependent on Power Planning. 
3. Placement : Placement of gate level netlist cells on the vertical rows. Cell placement is done in two steps Global and Detailed. Global placement finds the optimal placement of core cells. During Detailed placements, the placements done in Global placement are minimally altered.
4. Clock Tree Synthesis : Routing of the clocks by creating the distribution network with minimum skew and minimum latency.
5. Route : Signal Routing. During this stage, the interconnects get implemented using available metal layers. During each routing, the PDK defines the thickness, tracks, pitch, minimum width of each of the metal layers. The mental tracks form the routing grid. Routing is also dont in two steps: Global and Detailed Routing. Global Routing to create the routing guide and Detailed Routing to create the actual wiring. 
6. SignOff : Once Routing is done, we can construct the final layout which undergoes the verifications. Following are the types of verifications:
<pre> Physical Verifications:
   * Design Rule Checking (DRC)
   * Layout vs. Schematics (LVS)
 Timing Verifications:
   * Static Timing Analysis (STA)
</pre>

OpenLANE
OpenLANE is a tape-out-hardened flow that addresses two main use cases: hardening a macro and integrating a System-on-a-Chip (SoC). It was used successfully to tape out a family of RISC-V based SoCs known as “striVe”. This paper reviews the various components of the flow with a particular focus on the challenges that faced SoC integration while working on the first of the striVe chips and the main ideas used to overcome them, achieving full automation.

Following Figure illustrates the basic default flow; this is what runs in the batch (non-interactive) mode. Most of the steps are configurable and custom flows can be created by the use of interactive scripts. The flow expects the design source HDL files as an input as well as the desired PDK source files. 

![image](https://user-images.githubusercontent.com/123591219/214880192-3e18a0fb-0f73-4935-aa56-e95eeca6ad75.png)

OpenLane is based on different open source framework tools to perform each of the steps:
<EXCEL Data Goes here>

 
## Open Source EDA Tool \[OpenLANE\]
 We will be using OpenLANE as our EDA Tool and for PDK, which we will be using, is SKY130nm. OpenLANE is build around this PDK.
 <pre>sky130_fd_sc_hd --> (PDK_Tool_Name)_(Foundry_Name)_(Standard_Cell)_(High_Density)</pre>
 
To invoke the Tool:
Command:
 <pre>docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) openlane:rc6</pre>
 alias : docker

To initiate the flow, we need to run ./flow.tcl.
To run the step by step flow, we need to enable interactive mode as OpenLANE is a fully automated tool and invoking it without interactive mode will cause the run of the full RTL2GDSII Flow.

![image](https://user-images.githubusercontent.com/123591219/214917959-b885718f-2187-4903-900d-8cac2acb4549.png)


Design Preparation :
 To import all the packages that are required to run this flow:
 ![image](https://user-images.githubusercontent.com/123591219/214920942-e819989e-db85-434a-b4a9-6f94e0e1363e.png)

 All the designsd are extracted from the folder mentioned below:
 /work/tools/openlane_working_dir/openlane/designs/
 
 Design Setup Stage:
 
 prep -design picorv32a
 ![image](https://user-images.githubusercontent.com/123591219/214923431-e07ed188-32f7-4bf4-b2ee-991d9152516e.png)
 
 Required Folder Structures are created:
 ![image](https://user-images.githubusercontent.com/123591219/214924022-9ca2b691-4dc0-4a27-857a-26e980864825.png)
 
 Results Directory contains the directories for all the stages of the flow and the Reports Directory contains the timing reports once the STA stage is done. config.tcl file contains all the default values during run and the default values can be modified based on the requirement.
 

Design Synthesis :
 
STEP 1: Synthesis is the first step for OpenLANE Flow. 
 
Command: run_synthesis
 
Task 1:

Find the Flop Ratio :
 
Number of Cells = 14876
 
Number of D-FF(sky130_fd_sc_hd__dfxtp_2) = 1613
 
Hence, Flop Coount = 1613/14876 = 0.1084 :: 10.84%

![image](https://user-images.githubusercontent.com/123591219/214929528-28cb9db0-0a90-40f5-a0de-3a1808a1081e.png)

Now, the results/synthesis directory contains the synthesized netlist containing all the mappings done by abc.

![image](https://user-images.githubusercontent.com/123591219/214931059-f5f7e3e6-9596-48f1-952e-796fda0307ee.png)

Reports directory contains all the reports generated by yosys.
 
![image](https://user-images.githubusercontent.com/123591219/214931731-c560bf92-7e82-4c0d-85a0-0a2fb147d169.png)


## Day 2 - Good floorplan vs bad floorplan and introduction to library cells

 Height - Width of Core and Die
 
 - It depends on the sizes of the logic gates and flip-flops i.e. it is dependent on the dimentions of the standard cells.
 - We need to remove the wiring and find out the total sizes of the standard cells. 
 
 ![image](https://user-images.githubusercontent.com/123591219/214935886-edd4c568-ebe1-43c7-84c2-f1b6a5d3109a.png)

 ![image](https://user-images.githubusercontent.com/123591219/214936531-44f9db49-4713-48a5-ac07-ca4fb37cc153.png)

Utilization Factor = (Area Occupied by the Netlist)/(Total Area of the Core)
 
Aspect Ratio = Height/Width

![image](https://user-images.githubusercontent.com/123591219/214937815-d0d15b51-59ff-40af-b06d-e817928395c9.png)

 
 Define Locations of PrePlaced Cells

 - Combinational Logics are divided into blocks and the blocks get implemented independently.
 
 ![image](https://user-images.githubusercontent.com/123591219/214939772-c2988746-6eeb-47c8-baf7-54dd52853bc6.png)

 ![image](https://user-images.githubusercontent.com/123591219/214941168-5758c2d6-f9ac-472c-b8bc-2189ec317b45.png)

 Following are the components, that can be implemented once and instantiated multiple times as per requirements:
 
 Memory, Clock-Gating Cells, Comparator, MUX
 
 - The arrangements of these IP's in a chip is referred to as Floor-Planning.
 
 - These IP's have user-defined locations, and they are placed on the chip before automated placement and routing, hence, called pre-placed cells.
 
 
 De-coupling Capacitors 
 
 Pre-Placed cells are surrounded by the de-coupling capacitors. A decoupling capacitor, also referred to as a bypass capacitor, acts as a kind of energy reservoir. When a decoupling capacitor is in place, it will do one of two things:
 
 1. If the input voltage drops, then a decoupling capacitor will be able to provide enough power to an IC to keep the voltage stable.
 
 2. If the voltage increases, then a decoupling capacitor will be able to absorb the excess energy trying to flow through to the IC, which again keeps the voltage stable.
 
Decoupling capacitors connect between the power source (5V, 3.3V, etc.) and ground. It's not uncommon to use two or more different-valued, even different types of capacitors to bypass the power supply, because some capacitor values will be better than others at filtering out certain frequencies of noise.

![image](https://user-images.githubusercontent.com/123591219/215056984-2cd3faed-7bf6-47e2-be74-b8e017be96e6.png)

![image](https://user-images.githubusercontent.com/123591219/215057008-a2ce247c-ef9d-4889-9816-c0d8f3359f2c.png)

![image](https://user-images.githubusercontent.com/123591219/215057032-134373d7-145e-4a93-a529-66f81593a4aa.png)

![image](https://user-images.githubusercontent.com/123591219/215057048-3e2ce250-b4d0-426f-9bc0-bcdb0a6f2cd0.png)


Power Planning 

![image](https://user-images.githubusercontent.com/123591219/215076874-60be390d-cd0c-4b29-8889-eaa9b7258a35.png)

![image](https://user-images.githubusercontent.com/123591219/215077464-78de599f-f1d4-43a8-9cd5-9a3cf9c7566f.png)

![image](https://user-images.githubusercontent.com/123591219/215077682-8a071e1b-75f4-482f-b627-b030a6f06509.png)
 
![image](https://user-images.githubusercontent.com/123591219/215077845-9f45cf2c-7825-4d76-b540-da7ea19559cb.png)


Pin and Logical Placement

The netlist pins gets implemented on the chip.

![image](https://user-images.githubusercontent.com/123591219/215080124-6ea11ef4-9348-4c7c-94ee-b6a8c98e31e5.png)

![image](https://user-images.githubusercontent.com/123591219/215080417-f4f23b72-9e66-4acf-b668-a796e31a8906.png)



Steps to run floorplan using OpenLANE

 Floor Planning can be done using following command.
 <pre>run_floorplan</pre>
 
 The final configuration file that is taken for consideration is :
 
 /home/sreemoyee07/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/sky130A_sky130_fd_sc_hd_config.tcl
 
 As an output, .def file gets generated:
 
 /home/sreemoyee07/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/26-01_18-58/results/floorplan/picorv32a.floorplan.def
 
 ![image](https://user-images.githubusercontent.com/123591219/215092262-418f67de-30d2-4739-878f-2b510bb737ca.png)

 1 Micron = 1000 Data Bits Units
 Area of the chip = ( 660685000 micro-meter X 671405000 micro-meter ) 
 
 To visualize the layout after floor plan, following command can be used to load the layout with magic tool with the input parameters : lef and def files
 
 ![image](https://user-images.githubusercontent.com/123591219/215095430-6e2dc491-52f0-4939-bc7e-e76fee4d2609.png)

 picorv32a Topmount Cell in the Window
 
 ![image](https://user-images.githubusercontent.com/123591219/215096989-804dd748-1948-41d9-8b73-19b64063f285.png)
 
 Equidisantant Input Ports :
 
 ![image](https://user-images.githubusercontent.com/123591219/215097923-1138f88b-04b7-48a9-bd1d-7bd8799751b0.png)

 To know which layer we are in: (Metal Layer 3, which we set in horizontal metal in configuration file)
 
 ![image](https://user-images.githubusercontent.com/123591219/215098502-fed25763-2b97-4434-a5a2-ed099e76f8bf.png)
 
 The Vertical Metal Layer is as follows: (Metal Layer 2)
 
 ![image](https://user-images.githubusercontent.com/123591219/215104238-e07cf04a-499b-4261-8ea8-86210be25278.png)

 ![image](https://user-images.githubusercontent.com/123591219/215107556-4d33f1f0-d2a1-4843-b0db-9e178e299d06.png)

 
 Floorplan does not take into consideration the placement of standerd cell. But standerd cells are already present in the floor plan as shown below:
 ![image](https://user-images.githubusercontent.com/123591219/215108286-f08153da-8707-4687-aaba-06279fdeae96.png)

 
 
Netlist binding and initial place design
 
- Library contains the dimensions and the timing (delay) informations of each of the cells and the required operation condition of each of the cells. It stores all the flavors of each and every cell. 
 
 ![image](https://user-images.githubusercontent.com/123591219/215111810-97cfd30e-2e1f-41df-92c8-21448c6564f4.png)

 
 After forming the proper dimentions to the cells, the next step is to place the cells on the floor plan.
 
 ![image](https://user-images.githubusercontent.com/123591219/215112305-8f78de8a-f65e-4821-b5ed-bc4fcd478fb1.png)

 Repeater : A repeater is a buffer which has an input receiver and a driver or transmitter to resend a cleaned up version of the signal which was received. Repeaters are widely used to combat the quadratic delay of long on-chip wires. A conventional repeater is a CMOS inverter placed periodically along the long wire. This design has two drawbacks. The wire must have an even number of repeaters to preserve signal polarity. This forces the designer to sometimes use a suboptimal number of repeaters. It is also awkward when a wire branches because repeaters must be placed in such a way that all branches have an even number of repeaters. The second drawback is that many repeaters are required. For floorplanning reasons, repeaters tend to be grouped into “gas stations” which may not be immediately under the best wire route. Thus, longer wires are required to reach the gas stations. To deal with these problems, some designers have constructed repeaters from pairs of inverters. This automatically solves the polarity problem. Moreover, it may lead to fewer repeaters along the wire, since each repeater has lower input capacitance and higher output drive. This reduces the number of gas stations and extra routing required. Unfortunately, buffers are inherently slower than repeaters. This document explores the use of buffers as repeaters. It comes to a remarkably elegant conclusion showing that “optimal” buffer repeaters” are only slightly slower than “optimal” inverter repeaters.
 
 Data Slew Analysis: Slew is dependent on the value of the capacitor. The higher the value of the capacitor, the amount of charge, required to charge the capacitor will be high and the slew will be even bad.

 Optimized Placement :
 ![image](https://user-images.githubusercontent.com/123591219/215120522-b076b064-20b4-4200-97c7-575d39ad8695.png)

 
 
 Need for libraries and characterization :
 
 Step 1 : Logic Synthesis (Conversion of functionality to hardware)
 
 This is an arrangement of Gates that will represent the original functionality, described by RTL.
 
 Step 2 : Floor Planning
 
 The output of logic synthesis is imported, and the size of the core and the die is decided based on that. Hence, the dimension of the core and die is dependent on the dimension of the output cells of the logic synthesis. 
 
 Step 3 : Placement
 
 Logic Cells are placed on the Core such that initial timing is closed. 
 
 Step 4 : Clock Tree Synthesis (CTS)
 
 Here, the target is to have clocks having zero skew, to receive the clock at each cell at exact same time. Clock Buffers are added to have equal rise-fall time.
 
 Step 5 : Routing
 
 For routing, we need to include some properties. The routing step determines the exact pathways for interconnecting standard cells, macros, and I/O pins. Routing is related to library characterization step.
 
 
Floor planning Process for Congestion :

The first phase in the physical design process is floorplanning. The primary goal of floorplanning is to determine the best position for each module on the layout surface based on interconnectivity. One key check to make while selecting the locations is that there should be no overlap between two modules. At the floorplan stage, the designer decides the size of the die and constructs wire tracks for typical cell arrangement. A power ground connection is made, and the position of the 1/0 pad/pin is determined.
 
 Placement in OpenLANE occurs in two steps:
 
 1. Global Placement; \[Objective : To reduce the wire-length. Half Parameter Wire Length (HPWL)\]
 
 2. Detailed Placement;
 
 Output of the placement : 
 
 ![image](https://user-images.githubusercontent.com/123591219/215134393-ecc5907c-bfd0-49d2-b42d-2886bdec4df7.png)

 ![image](https://user-images.githubusercontent.com/123591219/215135346-42d9a8c0-3fe6-4498-bf6c-e5f2fe128d33.png)
 
 ![image](https://user-images.githubusercontent.com/123591219/215135911-cf373067-45b4-458b-a651-b34f89248b28.png)
 
 
 Post Floor-Plan and Post CTS, Power Distribution Flow is executed.
 
 
 
Cell design and characterization flows
 
The different dimensions of the cells affect the thresold voltage and other components of the cells. Library consists of different cells with differenct dimensions and different functionalities. 
 
 Cell Design Flow has three stages:
 
 ![image](https://user-images.githubusercontent.com/123591219/215145971-1dc1754d-466a-412a-b65a-91864cc01041.png)

 
 Input : Process Deign Kit (PDK)
 
 - DRC and LVS Rules :
 
 ![image](https://user-images.githubusercontent.com/123591219/215146468-476ea80f-1255-4430-bd7f-a5790e71db7f.png)
 
 - SPICE MODEL :
 
 ![image](https://user-images.githubusercontent.com/123591219/215146650-9ecbcbc2-c39e-425c-893d-cd46abb1e8c5.png)

 
 - Library and User Defined Specs :
 
 The difference between power rail and ground rail defines the Cell Height and it is the responsibility of the libraby operator to maintain this height. Cell width is dependent on the timing information, which depends on drive-strength specification. Top Level Designer determines the supply voltage and noise margin needs to be taken care based on that.
 
 ![image](https://user-images.githubusercontent.com/123591219/215150232-f431aabf-0bf7-4d2c-bd91-97a7789d99b7.png)

 
 Design Steps
 
 - Circuit Design
 
 Step 1 : Implement the function.
 
 Step 2 : Model the p-mos and n-mos function to meet the library model requirement.
 
 ![image](https://user-images.githubusercontent.com/123591219/215151527-ff483a43-8552-4ce4-9c4c-9c2efa7341d1.png)

 
 The output of the circuit design is Circuit Description Language [CDP].
 
 
 - Layout Design
 
 Step 1 : Implement the function into MOS Transistor.
 
 Step 2 : Derivation PMOS and NMOS Network Graph. Euler's Path & Stick Diagram.
 
 
 ![image](https://user-images.githubusercontent.com/123591219/215158028-251bcc2d-4f91-48a5-a624-44ce6513d4e4.png)

 
 - Characterization 
 
 Step 1 : Read the models and tech files from the layout.
 
 Step 2 : Read the extracted SPICE Netlist.
 
 Step 3 : Define the behavior of the buffer.
 
 Step 4 : Read the sub-circuits of the inverters.
 
 Step 5 : Add the necessary power sources.
 
 Step 6 : Apply the stimulus.
 
 Step 7 : Provide necessary capacitances.
 
 Step 8 : Provide the necessary simulation command.
 
 
 ![image](https://user-images.githubusercontent.com/123591219/215161086-121cf306-be27-4699-9513-97ea32692a05.png)
 
 
 Characterization Software is GUNA where provide all this inputs and the output will be timing, noise, power libs and functions.
 
 We have three types of characterizations.
 - Timing Characterization;
 - Power Characterization;
 - Noise Characterization;
 
 
 ![image](https://user-images.githubusercontent.com/123591219/215166014-4c7be1ee-0771-4190-afb9-27ee0dcb76f4.png)
 ![image](https://user-images.githubusercontent.com/123591219/215166202-9d261aeb-80cf-43fb-a679-3939160be575.png)
 ![image](https://user-images.githubusercontent.com/123591219/215166253-1c5e0521-7dd4-4c32-80ff-4320dcc59889.png)

 
 - Propagation Delay
 
 ![image](https://user-images.githubusercontent.com/123591219/215169483-3012b200-8583-479a-9d67-79c635d6f313.png)

 ![image](https://user-images.githubusercontent.com/123591219/215169931-feff302e-3624-41a2-a2c0-e515994bedb9.png)

 Negative Delay is not expected. The reason of negative delay is poor choice of thresold point.
 
 If the length of the wire between the cells are larger, then the slew will be higher. 
 
 ![image](https://user-images.githubusercontent.com/123591219/215171315-9da9742a-77db-44ab-8a2d-8b35e33b9d93.png)
 
 
 ## Day 3 : Design library cell using Magic Layout and ngspice characterization
 
 
 Labs for CMOS inverter ngspice simulations
 
 
 I/O Placer : One of the open source of EDA Tool which helps to place the I/Os on the Core.
 FP_IO_MODE`  | Decides the mode of the random IO placement option. 0=matching mode, 1=random equidistant mode <br> (Default: `1`)
 
 
 When IO mode is set to 2 instead of 1, we observe the following changes. The pins are not equidistant.
 
 set ::env(FP_IO_MODE) 2
 
 ![image](https://user-images.githubusercontent.com/123591219/215182567-f67490b5-e269-408a-867d-feefb437b5bf.png)
 
 
 
 SPICE Simulations:
 
 
 Step 1 : Create the SPICE Deck \[Connectivity information about the netlist\]
 
 ![image](https://user-images.githubusercontent.com/123591219/215184890-4375c524-49bc-4250-a7ac-216c485fa4d4.png)

 ![image](https://user-images.githubusercontent.com/123591219/215186582-8f34bf32-2414-4b11-9d51-fb2bbb5053a4.png)
 
 ![image](https://user-images.githubusercontent.com/123591219/215188152-2281a631-b3c2-45e6-b3ff-2662281ecce0.png)

 Switching Thresold : 
 
The switching threshold, Vm, is defined as the point where Vin = Vout. Switching threshold can be set by the ratio of relative driving strengths of the PMOS and NMOS transistors. To move Vm upwards, a larger value of ratio is required, which means making the PMOS wider. Increasing the strength of the NMOS, on the other hand, moves the switching threshold closer to GND. The effect of changing the Wp/Wn ratio is to shift the transient region of the VTC. Increasing the width of the PMOS or the NMOS moves VM towards VDD or GND respectively. This property can be very useful, as asymmetrical transfer characteristics are actually desirable in some designs.
 
![image](https://user-images.githubusercontent.com/123591219/215192275-08fe9211-f8ea-4e00-8b04-45103d2a1614.png)
![image](https://user-images.githubusercontent.com/123591219/215192833-55e57ea2-9087-45e8-ac86-6fb6ed04edda.png)

 
Lab steps to git clone vsdstdcelldesign
 
 git clone https://github.com/nickson-jose/vsdstdcelldesign.git

 cd /home/sreemoyee07/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
 
 <pre>cp /home/sreemoyee07/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs\.tech/magic/sky130A.tech /home/sreemoyee07/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign/.</pre>
 
 
 ![image](https://user-images.githubusercontent.com/123591219/215199317-d0d4ddae-bc72-4369-b250-a38f3d7fb638.png)
 
 
 ![image](https://user-images.githubusercontent.com/123591219/215199592-86a637a7-6757-469c-be36-fc8d66ce94d6.png)

 
 ![image](https://user-images.githubusercontent.com/123591219/215252347-72c037e7-f07a-43f6-886a-482ae5ffb201.png)
 
 ![image](https://user-images.githubusercontent.com/123591219/215252379-618b3eb8-cbc0-43e8-aec9-c9732f95c529.png)

 
 ![image](https://user-images.githubusercontent.com/123591219/215255690-e59d6e94-ed3f-47bc-9808-415e89ab6a5a.png)

 ![image](https://user-images.githubusercontent.com/123591219/215255647-709957de-343a-4e02-a970-1c4ff726882a.png)

 
 
 Lab steps to characterize inverter using sky130 model files
 
 
 Modified .spice file for the inverter:
 
 ![image](https://user-images.githubusercontent.com/123591219/215261266-c05de58c-53d2-43be-9ae6-2752b9212116.png)

 
 Command : ngspice sky130_inv.spice
 
 SPICE PLOT ::
 
 ![image](https://user-images.githubusercontent.com/123591219/215258937-ee745fba-0e95-416a-a103-a0ead12d4493.png)

 
 Rise Transition Time [From 20% to 80%] : 2.24537e-09 - 2.17978e-09 = 6.559e-11 second = 0.06559 nanosecond
 
 ![image](https://user-images.githubusercontent.com/123591219/215258574-8c0cd1a0-a53a-4de3-b4cc-a501d4116d79.png)
 
 Fall Transition Time [From 80% to 20%] : 4.09486e-09 - 4.05256e-09 = 0.0423e-09 second = 0.0423 nanosecond
 
 ![image](https://user-images.githubusercontent.com/123591219/215259509-64c62028-d7a4-45fb-be26-f03cb06931b0.png)

 
 Cell Rise Delay [50% of the rise]: Output - Input = 2.21073e-09 - 2.14994e-09 = 0.06079e-09 second = 0.06079 nanosecond
 
 ![image](https://user-images.githubusercontent.com/123591219/215260049-e694af89-71d2-4533-96bb-e0ae5c14b13b.png)

 
 Cell Fall Delay [50% of the fall]: 4.07758e-09 - 4.04994e-09 = 0.02764e-09 second = 0.02764 nanosecond
 
 ![image](https://user-images.githubusercontent.com/123591219/215260226-69ac6213-4fec-48d2-89d5-c4425c95c4ac.png)

 
 
 
## Day 4 - Pre-layout timing analysis and importance of good clock tree
 
 Objective : To extract .lef file from .mag file.
 
 tracks.info : Contains the information which is required during routing. It has the information about the metal layers. This describes where we want our routes to go.
 
 ![image](https://user-images.githubusercontent.com/123591219/215268539-f3cf290f-b72b-4619-b257-59a4ceace790.png)
 
 
 ![image](https://user-images.githubusercontent.com/123591219/215268832-f8ba7b43-393d-4395-8257-b6c596ba1b6b.png)

 
 Width of the standard cell should be odd multiples of the "x" pitch.
 
 
 Command to extract .lef file :
 
 ![image](https://user-images.githubusercontent.com/123591219/215279638-f258e360-79f2-4f8a-ba88-bc520bfc4c8c.png)


 ![image](https://user-images.githubusercontent.com/123591219/215279670-58ab5895-9b10-401c-bc72-16649f7ef3d7.png)
 
 
 To Plug-in the custom .lef file into the flow following steps needs to be followed:
 
 set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
 
 add_lefs -src $lefs
 
 run_synthesis
 
 
 Custom .lef file with custom config.tcl file : 
 
 ![image](https://user-images.githubusercontent.com/123591219/215283701-9e28b9fc-3d09-4c8a-be42-41aa310a1991.png)
 
 
 ![image](https://user-images.githubusercontent.com/123591219/215283812-6d85090e-f952-4d3b-b60a-f8e950daa5d2.png)

  
 
 Introduction to delay tables
 
 
 Power Aware CTS:
 
 
 ![image](https://user-images.githubusercontent.com/123591219/215285566-4e7c59ec-ee10-461d-9799-0b3fa8679bf9.png)
 
 
 Lab steps to configure synthesis settings to fix slack and include vsdinv
 
 Before :: Chip area for module '\picorv32a': 147712.918400
 
 tns -711.59
 
 wns -23.89
 
 % echo $::env(SYNTH_BUFFERING)
 
 1
 
 % echo $::env(SYNTH_SIZING)
 
 0

 % echo $::env(SYNTH_DRIVING_CELL)
 
 sky130_fd_sc_hd__inv_8

 
 Modifying SYNTH_STRATEGY to 2 from 1
 
 set ::env(SYNTH_STRATEGY) 1
 
 set ::env(SYNTH_SIZING) 1
 
 
 We have plugged in out custom cell in the openlane flow:
  
 ![image](https://user-images.githubusercontent.com/123591219/215288866-9fba097d-c93d-46f4-a932-f00a2aaa6f3e.png)
 
 ![image](https://user-images.githubusercontent.com/123591219/215288989-751c00b7-048b-471d-a803-789ea8b0d58d.png)
 
 ![image](https://user-images.githubusercontent.com/123591219/215289090-270ab1f1-3dfc-4c80-998a-5267a8f770d6.png)


 Version Issue ::
 
 ![image](https://user-images.githubusercontent.com/123591219/215289561-f26fdf5b-3f52-48d6-bb23-c2e165f3c8a9.png)

 
 Timing analysis with ideal clocks using openSTA
 
 
 ![image](https://user-images.githubusercontent.com/123591219/215289376-24040c2e-3980-43ad-9437-808df83373e7.png)

 
 Lab steps to configure OpenSTA for post-synth timing analysis
 
 ![image](https://user-images.githubusercontent.com/123591219/215291843-ce888e31-5d44-4971-9a01-6573d4b106f6.png)

 ![image](https://user-images.githubusercontent.com/123591219/215291865-d9be6dd9-fcc6-46a5-b307-72391c12b06d.png)

 OUTPUT : command:: ![image](https://user-images.githubusercontent.com/123591219/215291925-2d455199-ce14-474f-98fc-16bdf23f28ae.png)

 ![image](https://user-images.githubusercontent.com/123591219/215291949-169dd418-6f5c-449d-a6fa-5b81cfdbae81.png)
 
 
 
 
Lab steps to execute OpenSTA with right timing libraries and CTS

openroad

read_lef /openLANE_flow/designs/picorv32a/runs/28-01_15-09/tmp/merged.lef

read_def /openLANE_flow/designs/picorv32a/runs/28-01_15-09/results/cts/picorv32a.cts.def

write_db pico_cts.db

read_db pico_cts.db

read_verilog /openLANE_flow/designs/picorv32a/runs/28-01_15-09/results/synthesis/picorv32a.synthesis_cts.v

read_liberty -max $::env(LIB_SLOWEST)

read_liberty -min $::env(LIB_FASTEST)

read_liberty $::env(LIB_SYNTH_COMPLETE)

link_design picorv32a

read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc

set_propagated_clock [all_clocks]    

report_checks -path_delay min_max -format full_clock_expanded -digits 4

report_checks -path_delay min_max -field {slew trans net cap input_pin} -format full_clock_expanded -digits 4

![image](https://user-images.githubusercontent.com/123591219/215314671-bf775630-334f-49e6-82c3-8d56324e092d.png)


 
 
## Day 5 - Final steps for RTL2GDS using tritonRoute and openSTA
 
 
![image](https://user-images.githubusercontent.com/123591219/215317101-5a9aa1a9-2430-48bc-b856-3b5541960d8a.png)


ROUTING ::
 
![image](https://user-images.githubusercontent.com/123591219/215319486-22cfbe00-2e4c-4996-9388-983ec5da337d.png)
 

 TritonRoute

- Performs initial detail route.

- Honors the preprocessed route guides (obtained after fast route) ,i.e. , attempts as much as possible to route within route guides.

- Assumes route guides for each net satisfy inter-guide connectivity.

- Works on proposed MILP-based panel routing scheme with intra-layer parallel and inter-layer sequential routing framework.


![image](https://user-images.githubusercontent.com/123591219/215320052-8238230a-ffc8-420f-a590-8f045034dfcd.png)

 Preprocessed Route Guide:
 
 Requirements:
 
 1. Should have Unit Width.
 
 2. Should be in the preferred direction.
 
 
 Inter-guide connectivity

- Two guides are connected if:
- They are on the same metal layer with touching edges, or
- They are on neighboring metal layers with a nonzero vertically overlapped area.

- Each unconnected terminal (i.e., pin of a standard-cell instance should have its pin shape overlapped by a route guide.

 
 Intra-Layer Parallel and Inter-Layer Sequential Panel Routing :
 
 
 ![image](https://user-images.githubusercontent.com/123591219/215320365-27b85d45-2d14-4e40-ae2f-96a061501612.png)
 
 

 Handling Connectivity:

 - Access Point (AP): An on-grid point on the metal layer of the route guide, and is used to connect to lower-layer segments, upper-layer segments, pins or IO ports.

 - Access Point Cluster (APC): A union of all Aps derived from same lower-layer segment, upper-layer guide, a pin or an IO port.
 
 ![image](https://user-images.githubusercontent.com/123591219/215320836-1456596c-2207-4e07-841d-7f979bc23e24.png)
 
 
 Routing Topology Algorithm:
 
 ![image](https://user-images.githubusercontent.com/123591219/215321122-c694328b-934d-4237-8276-0fe300a9d829.png)

 
 Rounting Command : run_routing
 
 ![image](https://user-images.githubusercontent.com/123591219/215323329-d057c328-db1f-4d54-87b0-0971842cdd3f.png)

 
 ![image](https://user-images.githubusercontent.com/123591219/215324093-16a27c8f-0ae4-40c2-b148-d208cdcdc0d3.png)
 
 ![image](https://user-images.githubusercontent.com/123591219/215324122-b7745924-dc09-4a1b-a349-56219f613918.png)

 
 
 
 ## Questions & Answers
 
 
 1. PCB IS NOT A REQUIREMENT FOR ASIC DESIGN FLOW.
 
 2. STANDARD CELL LAYOUT ARE : enclosed by fixed height and unit site width rectangle.
 
 3. openlane is closed sourced flow.
 
 4. FAULT OPENEDA TOOL FACILITATES DFT IN OPENLANE FLOW.
 
 5. Detail routing is performed by TritonRoute inside OpenROAD app in openlane flow.
 
 6. WHAT IS UTILIZATION FACTOR? - Area occupied by netlist / Total area of core
 
 7. 100% UTILIZATION MEANS UTILIZATION FACTOR IS 1.
 
 8. ASPECT RATIO OF CHIP IS `height/width`.
 
 9. ASPECT RATIO OF 1 SIGNIFIES THAT A CHIP IS `square`.
 
 10. IF AREA OCCUPIED BY NETLIST IS 4MM2 AND TOTAL AREA OF CORE IS 16MM2, THEN WHAT IS THE UTILIZATION FACTOR? 0.25.
 
 11. EXAMPLES OF PRE-PLACED CELLS IN FLOORPLANNING ARE `Memories`
 
 12. WHICH OF THE FOLLOWING IS NOT DONE DURING FLOORPLAN IN OPENLANE FLOW WHICH WE SAW IN LABS? Standard Cell placement.
 
 13. WHAT DOES SETTING FP_IO_VMETAL SWITCH DO? Decides the metal layer on which only vertical IO pins are placed.
 
 14. WHAT IS THE PRECEDENCE ORDER OF FLOORPLAN CONFIGURATION SETTING FOR A DESIGN?
 
     sky130A_sky130_fd_sc_hd_config.tcl > config.tcl > floorplan.tcl.
 
 15. A TYPICAL STANDARD CELL INSIDE A LIBRARY CAN HAVE : different functionality, different sizes, Different Vt.
 
 16. Vt Means Thresold Voltage.
 
 17. PDK = Process Design Kit
 
 18. WHAT DOES A PDK TYPICALLY CONSISTS OF? - DRC & LVS rules, SPICE models, library and user defined specs.
 
 19. ONE OF MANY PARAMETERS DEFINED IN LIBRARY AND USER DEFINED SPECS IS ? cell height.
 
 20. A TYPICAL CIRCUIT DESIGN REQUIREMENT IS `IDSN+IDSP = 0`
 
 21. TYPICAL OUTPUTS OF A LIBRARY CHARACTERIZATION CELL DESIGN FLOW ARE `CDL, GDS2, LEF, CIR`
 
 22. TIMING LIBRARIES CHARACTERIZATION TYPE CAN BE CLASSIFIED AS timing, noise, power.
 
 23. 
