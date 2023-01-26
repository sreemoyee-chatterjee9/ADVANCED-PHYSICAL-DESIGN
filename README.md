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

