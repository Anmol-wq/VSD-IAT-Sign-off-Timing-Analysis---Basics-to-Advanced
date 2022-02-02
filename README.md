# VSD-IAT: Sign-off Timing Analysis - Basics to Advanced
![image](https://user-images.githubusercontent.com/73732594/152016610-be3ef4c8-601c-40e7-af85-91dc3ae9b2a4.png)

Static timing analysis (STA) is a method of validating the timing performance of a design by checking all possible paths for timing violations. STA breaks a design down into timing paths, calculates the signal propagation delay along each path, and checks for violations of timing constraints inside the design and at the input/output interface.  The workshop covers all the basic concepts in STA and Timing constraints. 

It starts with basics of Static Timing Analysis, timing paths, startpoint, endpoint and combinational logic definitions. It explains setup and hold checks, how STA tools calculate setup and hold violations. Then it slowly builds up to cover all aspects of STA like multiple types of timing paths, design rule checks, checks on async pins and clock gates. After that we go into slightly advanced topics like Time borrowing on latches, timing arcs, cell delays and models, impact of clock network on STA. Since STA and timing constraints go hand in hand the workshop covers basics of all the timing constraints that an engineer should know for STA like clock definitions, clock groups, clock characteristics, port delays and timing exceptions. 

**Contents:**

<!-- @import "[TOC]" {cmd="toc" depthFrom=2 depthTo=6 orderedList=false`} -->

<!-- code_chunk_output -->

- [Day-1 Lectures](#day-1-lectures)
	- [STA Definition](#definition)
	- [Timing Paths](#paths)
	- [Timing path elements](#elements)
	- [Setup & Hold Checks](#checks)
	- [Slack Calculation](#slack)
	- [SDC Overview](#sdc)
	- [Clocks](#clocks)
	- [Generated Clocks](#generated_clk)
	- [Boundary Constraints](#boundary_constraints)
- [Day-1 Labs](#day-1-labs)
  - [OpenTimer Introduction](#ot_Intro)
  - [Understanding basics of OpenTimer](#ot_basics)
  - [Inputs to OpenTimer](#ot_inputs)
  - [Constraints creation](#constraints)
  - [OpenTimer Run script](#ot_run)  
- [Day-2_Lectures](#day-2-lectures)
  - [Other timing checks](#other_timing_checks)
  - [Design Rule Checks](#drcs)
  - [Latch Timing](#latch_timing)
  - [STA Text Report](#sta_report)
- [Day-2_Labs](#day-2-labs)
  - [Liberty Files and Understanding Lib Parsing](#liberty_files_and_understanding_lib_parsing)
  - [Understanding SPEF file and SPEF parsing](#understanding_spef_file_and_spef_parsing)
  - [Understanding OpenTimer tool messages](#understanding_opentimer_tool_messages)
  - [Understanding timing reports and timing graphs](#understanding_timing_reports_and_timing_graphs)
  
 


- [Acknowledgements:](#acknowledgements)
- [References:](#references)

<!-- /code_chunk_output -->

---

# Day-1 Lectures
## STA Definition

Static Timing Analysis (STA) is a method of verifying timing performance of a design. Its key feature is that it is exhaustive compared to functional simulation and SPICE simulation. It doesn't require testbenches and is not used for asynchronous design. It doesn't rely on imput vectors and is a mathematical technique going through all the paths. It doesn't verify functionality of the design. Its pessimistic and conservative so as to ensure there is a definite guard band for sign-off. Typical inputs for STA are netlist, SDC or constraints file, and logic libraries.

## Timing Paths

STA breaks the paths at ports and sequential elements. We can see the sequential design below, where the input port, output port, clock port, D FFs along with some combinational logic are provided.

![timing_paths](https://user-images.githubusercontent.com/73732594/152146866-7672ff33-34cd-43f1-b77c-705dddb616be.png)

The illustrated timing paths for the former are as follows.

![timing_paths_illustrated](https://user-images.githubusercontent.com/73732594/152146941-96eda29e-9930-455a-97a6-c7cc5888fcce.png)

The timing analysis is performed on each of these paths.

## Timing path elements

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








