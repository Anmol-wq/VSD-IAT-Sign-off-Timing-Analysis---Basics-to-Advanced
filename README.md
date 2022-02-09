# VSD-IAT: Sign-off Timing Analysis - Basics to Advanced
![image](https://user-images.githubusercontent.com/73732594/152016610-be3ef4c8-601c-40e7-af85-91dc3ae9b2a4.png)

Static timing analysis (STA) is a method of validating the timing performance of a design by checking all possible paths for timing violations. STA breaks a design down into timing paths, calculates the signal propagation delay along each path, and checks for violations of timing constraints inside the design and at the input/output interface.  The workshop covers all the basic concepts in STA and Timing constraints. 

It starts with basics of Static Timing Analysis, timing paths, startpoint, endpoint and combinational logic definitions. It explains setup and hold checks, how STA tools calculate setup and hold violations. Then it slowly builds up to cover all aspects of STA like multiple types of timing paths, design rule checks, checks on async pins and clock gates. After that we go into slightly advanced topics like Time borrowing on latches, timing arcs, cell delays and models, impact of clock network on STA. Since STA and timing constraints go hand in hand the workshop covers basics of all the timing constraints that an engineer should know for STA like clock definitions, clock groups, clock characteristics, port delays and timing exceptions. 

**Contents:**

<!-- @import "[TOC]" {cmd="toc" depthFrom=2 depthTo=6 orderedList=false`} -->

<!-- code_chunk_output -->

- [Day-1 Summary](#day-1-summary)
	
- [Day-1 Labs](#day-1-labs)
  - [OpenTimer Introduction](#ot_Intro)
  - [Understanding basics of OpenTimer](#ot_basics)
  - [Inputs to OpenTimer](#ot_inputs)
  - [Constraints creation](#constraints)
  - [OpenTimer Run script](#ot_run)  
- [Day-2_Summary](#day-2-summary)
  
- [Day-2_Labs](#day-2-labs)
  - [Liberty Files and Understanding Lib Parsing](#liberty_files_and_understanding_lib_parsing)
  - [Understanding SPEF file and SPEF parsing](#understanding_spef_file_and_spef_parsing)
  - [Understanding OpenTimer tool messages](#understanding_opentimer_tool_messages)
  - [Understanding timing reports and timing graphs](#understanding_timing_reports_and_timing_graphs)
- [Day-3_Summary](#day-3-summary)
 
- [Day-3_Labs](#day-3-labs)
  - [Understanding full reg to reg STA analysis](#reg-sta)
  - [Understanding Slack computation](#slack-computation)
  
- [Day-4_Summary](#day-4-summary)
  
- [Day-4_Labs](#day-4-labs)
  - [Understanding clock gating check](#understanding-clock-gating)
  - [Understanding Async pin checks](#understanding-async-check)
  
- [Day-5_Summary](#day-5-summary)

- [Day-5_Labs](#day-5-labs)
  - [Revisit slack computation](#revisit-slack)
  - [Clock properties](#clockproperties)
  - [CRPR](#crpr)
  - [ECO](#eco)
- [Acknowledgements:](#acknowledgements)
- [Author:](#author)

<!-- /code_chunk_output -->

---

# Day-1 Summary

Static Timing Analysis (STA) is a method of verifying timing performance of a design. Its key feature is that it is exhaustive compared to functional simulation and SPICE simulation. It doesn't require testbenches and is not used for asynchronous design. It doesn't rely on imput vectors and is a mathematical technique going through all the paths. It doesn't verify functionality of the design. Its pessimistic and conservative so as to ensure there is a definite guard band for sign-off. Typical inputs for STA are netlist, SDC or constraints file, and logic libraries.

STA breaks the paths at ports and sequential elements.

The timing analysis is performed on each of these paths.

Broadly speaking, there are three components of the path, which are, startpoint, endpoint and combinational logic.

Startpoint is where the data is launched by clock edge, or where the data must be available at a specific time. It is usually input port or register clock pinhe clock pin is taken into account because CLK -> Q delay also comes into picture.

Endpoints are where data is captured by clock edge, or where the data must be available at a specific time. It is usually output port or register data pin.

Combinational logic blocks are elements which have no memory, or internal state. Ex.: AND gate, OR gate.

Between a set of startpoint and endpoint, combinational logic might have multiple paths.

Setup time of a flop is dependent on the technology node. Value is available in logic libraries. This enforces max delay on the data path. Data should be available at the input of sequential device before clock edge that captures the data.

Similarly hold time enforces min delay on the data path. Here, data should be stable at the input of sequential device for sometime after the clock edge that captures the data.

Setup Slack = Data Required Time - Data Arrival Time

Slack is negative when data arrives later than required time.

Timing is met when slack is positive, otherwise it isn't.

Synopsys Design Constraints (SDC) for timing specify parameters affecting operational frequency of the design. Examples: create_clock, create generated_clock, set_clock groups, set_clock_transition, set_timing_derate, etc.

Similarly, there are constraints for area and power, which specify restrictions about the area and power. Examples: set_max_area, set_max_dynamic_power, etc. and constraints for design rules which are requirements of the target technology. Examples: set_max_capacitance, set_max_transition, set_max_fanout, etc. 

There are exceptions to design constraints which relax the requirements set by other commands. Examples: set_false_path, set_multicycle_path, etc.

# Day-1 Labs

## OpenTimer Introduction

OpenTimer is a new static timing analysis (STA) tool to help IC designers quickly verify the circuit timing. It is developed completely from the ground up using C++17 to efficiently support parallel and incremental timing. 

Compile OpenTimer and launch the shell program ot-shell.

## Understanding basics of OpenTimer

![image](https://user-images.githubusercontent.com/73732594/152780811-db8cd8d6-5b3e-4932-a504-b1ab46e57eed.png)

An STA tool takes design, standard cell, constraints as input and perform timing checks on the design. OpenTimer works on industry formats (e.g., .v, .spef, .lib, .sdc) and is designed to be parallel and portable.

## Inputs to OpenTimer

The inputs to the opentimer are design, standard cells associated with the netlist and the constraints.
 
![Screenshot from 2022-02-07 13-33-42](https://user-images.githubusercontent.com/73732594/152804282-a78572ba-9ba0-4f8c-b8d6-5331f0b7b408.png)
The Netlist for the lab.

![Screenshot from 2022-02-07 13-33-42](https://user-images.githubusercontent.com/73732594/152804739-04a6178c-a739-4655-b760-b46288af2a59.png)
A typical example for the standard cell from OSU-180.lib file.

## Constraints creation

The SDC file provided for the lab. This consists of the clock period, IO delays, input transition and capacitance delays. 
![Screenshot from 2022-02-07 19-46-47](https://user-images.githubusercontent.com/73732594/152805399-8f6b8c8b-793b-4286-81aa-5d88c9deecd8.png)

## OpenTimer Run script

![Screenshot from 2022-02-07 19-53-25](https://user-images.githubusercontent.com/73732594/152807446-c8a6da52-7e15-4342-b0d1-dad8deb7080b.png)
![Screenshot from 2022-02-07 20-01-22](https://user-images.githubusercontent.com/73732594/152807613-00e5c962-5d94-49b0-b1d2-c807d755e1bb.png)

The timing checks aren't met since the slack is negative.

![Screenshot from 2022-02-07 20-03-33](https://user-images.githubusercontent.com/73732594/152808162-776920d1-3ba1-449d-a0bf-13c76edbce7a.png)

# Day-2 Summary

Apart from setup and hold checks, STA also has other timing checks in place like clock gating checks, async pin checks and data to data checks. We also make a note of the rise and fall slew transitions. We also have to provide load analysis by specifying min and max capacitances on the IO net, and corresponding fanout load on ports and output pins. The skew between launch and capture clock waveforms also needs to be taken into account, the skew is positive if the capture flop clock leads the launch flop clock. The duty cycle of the clock is limited by various parameters apart from the technology node. Latch based designs allow more flexibility in timing and also aids time borrowing. A typical STA text report contains startpoint, endpoint, 'max' signifies setup time check and the nodes in the design mentioned as paths, whose respective delays are taken into account.   

# Day-2 Labs

## Liberty Files and Understanding Lib Parsing
The .lib file is an ASCII representation of the timing and power
parameters associated with any cell in a particular semiconductor
technology The .lib file contains timing models and data to calculate I/O delay paths, Timing check values and Interconnect delays.

Exercises:

• Find all the cells in simple_Late.lib.
• Find all the pins of the cell NAND2_X1 in simple_Late.lib

This could be done by some Tcl scripting it seems, will try and update as soon as possible.

• What difference you see between NAND2_X1 and NAND3_X1

The max capacitance increases multifold, and the number of input pins.

• What is the difference between ‘simple_Late.lib’ and ‘simple_Early.lib

Fabrication process variations could either increase or decrease the delay of a cell. So we need to set early and late value while setting the derate factor. STA tool would consider early or late timing derate based on the path and type of analysis.

## Understanding SPEF file and SPEF parsing

SPEF can be generated by place-and-route tool or a parasitic extraction tool, and then this SPEF is used by timing analysis tool for checking the timing, in-circuit simulation or to perform crosstalk analysis.

Parasitics can be represented at many different levels.
A Typical SPEF File has 4 main sections

1. Header
2. Name Map
3. Top Level Ports
4. Parasitic description

![Screenshot from 2022-02-07 21-21-25](https://user-images.githubusercontent.com/73732594/152823092-c6aac9a5-1b1c-4bc6-911a-728a2949ffa0.png)


## Understanding OpenTimer tool messages

![Screenshot from 2022-02-07 22-37-20](https://user-images.githubusercontent.com/73732594/152836739-3295abdf-d8a3-4b8b-812b-cd9b4fd2a36a.png)

Here the number of cells are reported as an information message and arrival time for the marked pin is given as 40.33.

## Understanding timing reports and timing graphs

![Screenshot from 2022-02-07 22-37-20](https://user-images.githubusercontent.com/73732594/152837812-50e222c9-106d-472f-94c2-2e2d5b9341f3.png)
This is the timing report we have obtained from the netlist and other inputs being parsed into the OpenTimer tool.

![Screenshot from 2022-02-07 22-53-31](https://user-images.githubusercontent.com/73732594/152839697-85913598-8ed4-49df-8d95-8e3cb26ad87c.png)


# Day-3 Labs

## Understanding full reg to reg STA analysis

![Screenshot from 2022-02-07 23-16-53](https://user-images.githubusercontent.com/73732594/152843403-144f3783-6c80-4160-87dc-e907c2843bb3.png)
![Screenshot from 2022-02-07 23-18-51](https://user-images.githubusercontent.com/73732594/152843754-f891934b-ab2d-4390-bba9-f6f8fc21783b.png)

This is the netlist and sdc file utilized for the analysis.

![Screenshot from 2022-02-07 23-03-03](https://user-images.githubusercontent.com/73732594/152841252-d13330d3-4576-46bd-a201-4d73e738630d.png)

Functionally, it might seem like the first path, corresponds to the slack computation but the same cannot be mapped from the report exactly.


## Understanding Slack computation

![Screenshot from 2022-02-07 23-07-07](https://user-images.githubusercontent.com/73732594/152842219-27d77509-e33c-4101-80a9-44b4c3acb55a.png)

![Screenshot from 2022-02-07 23-09-25](https://user-images.githubusercontent.com/73732594/152842243-401bf7ff-c1e7-4bc6-97b1-e2c8348d17b4.png)

# Day-4_Labs
  ## Understanding clock gating check
  ![Screenshot from 2022-02-07 23-37-03](https://user-images.githubusercontent.com/73732594/152846371-b79e12aa-b53e-466b-acff-fea2b30c7797.png)

  ![Screenshot from 2022-02-07 23-29-38](https://user-images.githubusercontent.com/73732594/152846178-92c2bc06-8966-437f-a476-d6a535481247.png)

  ## Understanding Async pin checks
  
  ![Screenshot from 2022-02-07 23-40-08](https://user-images.githubusercontent.com/73732594/152846814-6e7fc775-c548-4aca-b30e-264700bd9f6a.png)

  ![Screenshot from 2022-02-07 23-41-26](https://user-images.githubusercontent.com/73732594/152847251-387876f0-df1d-4bd1-8fe0-735e81107fa4.png)
  
  ![Screenshot from 2022-02-07 23-42-41](https://user-images.githubusercontent.com/73732594/152847313-5bad3d31-481b-430d-b235-5210887bcb26.png) 

OpenTimer 2.1.0 <br>
Time unit        : 1e-12 s <br>
Capacitance unit : 1e-15 F <br>
Voltage unit     : 1 V <br>
Current unit     : 0.001 A <br>
Power unit       : 1e-06 W <br>
Pins           : 22 <br>
POs            : 1 <br>
PIs            : 6 <br>
Gates          : 3 <br>
Nets           : 19 <br>
Arcs           : 25 <br>
SCCs           : 0 <br>
Tests          : 6 <br>
Cells          : 105 <br>
# Day-5_Labs
  ## Revisit slack computation
  ![Screenshot from 2022-02-07 23-48-15](https://user-images.githubusercontent.com/73732594/152848018-392cb1af-684d-4ef0-9329-735edb5ddcb1.png)

  ## CPPR
  ![Screenshot from 2022-02-07 23-50-44](https://user-images.githubusercontent.com/73732594/152848433-4f1a25bf-72fd-412b-8bf7-99f640e3a2ce.png)

  ## ECO
  ![Screenshot from 2022-02-07 23-52-54](https://user-images.githubusercontent.com/73732594/152848638-09978f8d-a4ad-40e7-805c-a0c47cc9c6a0.png)

## Acknowledgements

- [Kunal Ghosh](https://github.com/kunalg123), Co-founder of VLSI System Design (VSD) Corp. Pvt. Ltd.
- [Vikas Sachdeva](https://vlsideepdive.com/), Advisor, Tech and VLSI Coach, Trainer and Innovator at vlsideepdive.

## Author

[Anmol Saxena](https://www.linkedin.com/in/anmol-saxena-ee/), M.Tech ICT (2019-21), DA-IICT, Gandhinagar, Gujarat, India
- Contact: anmol.saxena2016@outlook.com, 201911053@daiict.ac.in  <br>
- System configuration during the undertaking:  <br>\
-- Processor: Intel(R) Core(TM) i5-5300U CPU @ 2.30GHz   <br>\
-- Installed RAM: 8.00 GB (7.88 GB usable)  <br>\
-- System type:	Ubuntu 18.04 LTS, 64-bit operating system, x64-based processor  <br>










 

 
 










