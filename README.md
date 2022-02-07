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
- [References:](#references)

<!-- /code_chunk_output -->

---

# Day-1 Summary

Static Timing Analysis (STA) is a method of verifying timing performance of a design. Its key feature is that it is exhaustive compared to functional simulation and SPICE simulation. It doesn't require testbenches and is not used for asynchronous design. It doesn't rely on imput vectors and is a mathematical technique going through all the paths. It doesn't verify functionality of the design. Its pessimistic and conservative so as to ensure there is a definite guard band for sign-off. Typical inputs for STA are netlist, SDC or constraints file, and logic libraries.

STA breaks the paths at ports and sequential elements. We can see the sequential design below, where the input port, output port, clock port, D FFs along with some combinational logic are provided.

![timing_paths](https://user-images.githubusercontent.com/73732594/152146866-7672ff33-34cd-43f1-b77c-705dddb616be.png)

The illustrated timing paths for the former are as follows.

![timing_paths_illustrated](https://user-images.githubusercontent.com/73732594/152146941-96eda29e-9930-455a-97a6-c7cc5888fcce.png)

The timing analysis is performed on each of these paths.

Broadly speaking, there are three components of the path, which are, startpoint, endpoint and combinational logic.

Startpoint is where the data is launched by clock edge, or where the data must be available at a specific time. It is usually input port or register clock pin.

![timing_paths_startpoint](https://user-images.githubusercontent.com/73732594/152147843-6aa33146-8fe0-48e6-bea1-f55e19a86118.png)

![timing_paths_illustrated](https://user-images.githubusercontent.com/73732594/152146941-96eda29e-9930-455a-97a6-c7cc5888fcce.png)

For path 3 the CLK pin acts as the startpoint, the clock pin is taken into account because CLK -> Q delay also comes into picture.

Endpoints are where data is captured by clock edge, or where the data must be available at a specific time. It is usually output port or register data pin.

![timing_paths_endpoint](https://user-images.githubusercontent.com/73732594/152150078-5f617b9d-e4d8-4241-8696-17f4681b8191.png)

![timing_paths_illustrated](https://user-images.githubusercontent.com/73732594/152146941-96eda29e-9930-455a-97a6-c7cc5888fcce.png)

For Path 1 the endpoint is the OUT port.

Combinational logic blocks are elements which have no memory, or internal state. Ex.: AND gate, OR gate.

![image](https://user-images.githubusercontent.com/73732594/152150767-53a44b3e-61ec-451b-9185-401d6bff34c6.png)

Between a set of startpoint and endpoint, combinational logic might have multiple paths.

Setup time of a flop is dependent on the technology node. Value is available in logic libraries. This enforces max delay on the data path. Data should be available at the input of sequential device before clock edge that captures the data.

![image](https://user-images.githubusercontent.com/73732594/152221237-7cc71843-1cef-44cc-b903-1275ad4b2ef3.png)

Similarly hold time enforces min delay on the data path. Here, data should be stable at the input of sequential device for sometime after the clock edge that captures the data.

![image](https://user-images.githubusercontent.com/73732594/152221593-aa146a22-398a-44af-a4cd-27f9db709c0a.png)

Setup Slack = Data Required Time - Data Arrival Time

![image](https://user-images.githubusercontent.com/73732594/152223076-3592fd9d-e6a9-4aef-8ac7-20fe1ff30179.png)

![image](https://user-images.githubusercontent.com/73732594/152223261-51bbe8a9-342f-4607-98a6-94cd2ee1e810.png)

![image](https://user-images.githubusercontent.com/73732594/152223649-c6498fd7-dd4d-4a93-bf82-0c9521ae339a.png)

Similarly slack is negative when data arrives later than required time.

Timing is met when slack is positive, otherwise it isn't.

Synopsys Design Constraints (SDC) for timing specify parameters affecting operational frequency of the design. Examples: create_clock, create generated_clock, set_clock groups, set_clock_transition, set_timing_derate, etc.

Similarly, there are constraints for area and power, which specify restrictions about the area and power. Examples: set_max_area, set_max_dynamic_power, etc. and constraints for design rules which are requirements of the target technology. Examples: set_max_capacitance, set_max_transition, set_max_fanout, etc. 

![image](https://user-images.githubusercontent.com/73732594/152229305-fe96fbf0-5856-452f-ac50-7ee252fee69b.png)

![image](https://user-images.githubusercontent.com/73732594/152229547-92c342da-9c4f-4fd5-a6cf-7d3b8cde17b9.png)

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

# Day-2 Labs

## Liberty Files and Understanding Lib Parsing
The .lib file is an ASCII representation of the timing and power
parameters associated with any cell in a particular semiconductor
technology The .lib file contains timing models and data to calculate I/O delay paths, Timing check values and Interconnect delays.

Exercises:

## Understanding SPEF file and SPEF parsing
## Understanding OpenTimer tool messages
## Understanding timing reports and timing graphs

# Day-3 Labs

## Understanding full reg to reg STA analysis
## Understanding Slack computation







 

 
 










