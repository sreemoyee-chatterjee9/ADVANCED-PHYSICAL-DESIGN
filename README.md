# ADVANCED-PHYSICAL-DESIGN
Here, all the learnings during the workshop : [ADVANCED PHYSICAL DESIGN USING OPENLANE/SKY130](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/) is documented with the examples and the experiments. Essentially, this workshop document was made concentrating on the daily activities that has been explored during RTL2GDS end to end flow with the open-source flow: OpenLane. Core design utilized duirng this process is PICORV32A RISC-V.

# Introduction


### Day 1 :: Inception of open-source EDA, OpenLANE and Sky130 PDK
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

